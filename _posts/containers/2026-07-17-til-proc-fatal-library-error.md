---
title:   Today I Learned Why unshare --pid Throws a 'fatal library error, lookup self'
description: proc file system and unshare command
author: win
date: 2026-07-15 11:33:00 +0800
categories: [Operating Systems, Linux, Namespaces]
tags: [Linux, OS, Process,Containers]
pin: true
math: true
mermaid: true
image:
 path: https://images.unsplash.com/photo-1612742034062-9714e6bd1792?q=80&w=1678
 alt: grid
 
---



If you are learning how Linux containers work under the hood, you have probably tried to build one from scratch using the `unshare` command. It’s a rite of passage! 

But if you ran this command:
```bash
sudo unshare --fork --pid /bin/bash
```
...and then tried to run a basic command like `ps aux` or `ls -l /proc/self`, you likely crashed headfirst into this cryptic error:

> `fatal library error, lookup self`

At first glance, this looks like a broken . But it’s actually practical demonstration of how Linux namespaces and the `/proc` filesystem interact. Let me go through what is happening under the hood.

---

## 🔍 The Mystery: What is `/proc`?
To understand the error, you first need to understand `/proc`. It is not a real folder on your hard drive. It is a **pseudo-filesystem** generated dynamically in RAM by the Linux kernel. It acts as a real-time window into the kernel's internal data structures, most notably exposing running processes as directories named after their Process IDs (e.g., `/proc/1`, `/proc/7890`).

Crucially, `/proc/self` is a special symlink that always points to the directory of the process that is currently accessing it.

---

## ⚠️ The Problem: The Namespace Mismatch
When you run `unshare --pid`, the kernel creates a brand new PID namespace. Inside this new, isolated world, your new shell becomes **PID 1**. 

However, there is a catch: **the kernel does not automatically change your view of the `/proc` filesystem.** 

Your new shell *believes* it is PID 1. But when it (or a tool like `ps`) asks the `/proc` directory to resolve `/proc/self`, the kernel looks at the *host's* process tree. It sees that your shell's actual host PID is something like `7890`. 

So, `/proc/self` resolves to `/proc/7890`. 

This creates a severe mismatch. The C library expects the process to find its own namespace PID in `/proc`, but instead, it finds the host's PID. The library gets confused, fails to resolve its own identity, and throws the `fatal library error, lookup self`. Worse, it can inadvertently leak information about the host system.

---

## ✅ The Solution: The Magic of `--mount-proc`
To fix this, you must explicitly tell `unshare` to remount the proc filesystem. The correct command is:

```bash
sudo unshare --fork --pid --mount-proc /bin/bash
```

This single flag does two critical things:
1. **Creates a new Mount Namespace:** Giving your new process an isolated view of mounted filesystems.
2. **Mounts a fresh `proc` filesystem:** It executes the equivalent of `mount -t proc proc /proc` *inside* that new mount namespace.

This fresh `/proc` is explicitly tied by the kernel to your **new PID namespace**. Now, when your shell asks for `/proc/self`, it correctly resolves to `/proc/1`. Tools like `ps` work perfectly, and your container is truly isolated.

---

## 🕵️‍♂️ Deep Dive: What `unshare` Does Behind the Scenes
I was curious about what the `unshare` CLI tool does differently than a simple C program calling `unshare()`. By using `strace`, I found some fascinating differences.

### The Strace Comparison
When I ran a simple C program that only called `unshare(CLONE_NEWNS)`, the logs were very short. 

```c
#define _GNU_SOURCE
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>
#include <sched.h>
#include <sys/wait.h>
#include <errno.h>

int main() {
    // 1. Unshare the mount namespace (equivalent to syscall.Unshare(syscall.CLONE_NEWNS))
    if (unshare(CLONE_NEWNS) != 0) {
        perror("unshare failed");
        return EXIT_FAILURE;
    }

    // 2. Fork the process
    pid_t pid = fork();
    if (pid < 0) {
        perror("fork failed");
        return EXIT_FAILURE;
    }

    if (pid == 0) {
        // --- Child Process ---

        // Prepare arguments for bash
        char *argv[] = {"bash", NULL};

        // Execute bash.
        // This automatically inherits stdin, stdout, stderr, and the environment.
        execvp("bash", argv);

        // If execvp returns, it means it failed
        perror("execvp failed");
        exit(EXIT_FAILURE);
    } else {
        // --- Parent Process ---

        int status;
        // Wait for the child process (bash) to finish
        if (waitpid(pid, &status, 0) < 0) {
            perror("waitpid failed");
            return EXIT_FAILURE;
        }

        // Return the exact exit code that bash returned
        if (WIFEXITED(status)) {
            return WEXITSTATUS(status);
        }

        return EXIT_FAILURE;
    }
}
```
---
```bash
munmap(0x72dd064ff000, 24419)           = 0
unshare(CLONE_NEWNS)                    = 0          <------------------------
clone(child_stack=NULL, flags=CLONE_CHILD_CLEARTID|CLONE_CHILD_SETTID|SIGCHLD, child_tidptr=0x72dd064fca10) = 16024
```

However, when I ran `sudo strace unshare --mount bash`, I saw this immediately after the `unshare` syscall:

```bash
unshare(CLONE_NEWNS)                    = 0
mount("none", "/", NULL, MS_REC|MS_PRIVATE, NULL) = 0                                <------------------------
execve("/usr/local/sbin/bash", ["bash"], 0x7ffcf6e49a28 /* 13 vars */) = -1 ENOENT (No such file or directory)
```

### Code To fix to mimic exact behaviour of `unshare` 

```c
#define _GNU_SOURCE
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>
#include <sched.h>
#include <sys/wait.h>
#include <sys/mount.h>
#include <errno.h>

int main() {
    // 1. Unshare the mount namespace
    if (unshare(CLONE_NEWNS) != 0) {
        perror("unshare failed");
        return EXIT_FAILURE;
    }

    // 2. Make the root mount private (mimics unshare --mount behavior)
    // This prevents mount events from propagating between the host and this namespace.
    if (mount(NULL, "/", NULL, MS_REC | MS_PRIVATE, NULL) != 0) {
        perror("mount --make-rprivate / failed");
        return EXIT_FAILURE;
    }
    // ...all remaining codes are the same
```

### Why This Matters
This extra `mount` call is the secret of container isolation:
*   **MS_REC | MS_PRIVATE:** This changes the root mount (`/`) to "Private." By default, Linux mounts are "Shared," meaning if you mount a USB drive inside your container, the host would see it too. Setting this to `MS_PRIVATE` ensures that any mount changes you make inside your new namespace stay **completely isolated** from the host.
*   **The Missing Piece:** A basic `unshare()` call in C or Python doesn't do this automatically. If you want to build a true container from scratch, you have to manually perform this `mount` syscall to prevent "leaking" mount points between the host and the container.

---

## 🗺️ Visualizing the Interaction

```mermaid
flowchart TD
    subgraph Host System ["🖥️ Host System"]
        HostProc["Host /proc filesystem\n(Contains /proc/1, /proc/7890, etc.)"]
        HostShell["Host Shell\n(Actual PID 7890)"]
        HostShell -->|registered in| HostProc
    end

    subgraph Scenario A ["❌ Scenario A: unshare --pid (The Mismatch)"]
        NewNS_A["New PID Namespace"]
        Shell_A["New Shell\n(Thinks it is PID 1)"]
        
        NewNS_A --> Shell_A
        Shell_A -.->|Queries /proc/self| HostProc
        HostProc -.->|Returns| HostProc7890["Host's /proc/7890"]
        
        Shell_A -.->|Result| ErrorA["⚠️ MISMATCH: Shell expects PID 1,\nbut gets Host PID 7890.\nLeads to 'lookup self' error."]
    end

    subgraph Scenario B ["✅ Scenario B: unshare --pid --mount-proc (The Solution)"]
        NewNS_B["New PID Namespace"]
        NewMountNS["New Mount Namespace"]
        Shell_B["New Shell\n(Is PID 1)"]
        FreshProc["Fresh /proc filesystem\n(Contains only /proc/1, etc.)"]
        
        NewNS_B --> Shell_B
        NewMountNS --> FreshProc
        Shell_B -.->|Queries /proc/self| FreshProc
        FreshProc -.->|Returns| FreshProc1["New /proc/1"]
        
        Shell_B -.->|Result| Success["✅ MATCH: Shell is PID 1,\nand /proc/self correctly\nresolves to /proc/1."]
    end


```

---

## 💡 Key Takeaway
A new PID namespace is practically useless for process management tools unless it is paired with a new Mount namespace and a freshly mounted `proc` filesystem. **Always use `--mount-proc` when isolating PIDs!**

***


Here are the official references for the concepts covered in the article:

1.  **Linux Namespaces Overview**: `man 7 namespaces` – The primary manual page detailing the eight types of Linux namespaces, including PID and Mount namespaces.
2.  **PID Namespaces Specifics**: `man 7 pid_namespaces` – Detailed documentation on how process IDs are isolated and how the `/proc` filesystem interacts with PID namespaces.
3.  **The `unshare` Utility**: `man 1 unshare` – The manual for the command-line tool used to run programs with some namespaces unshared from the parent, including the critical `--mount-proc` flag.
4.  **Proc Filesystem**: `man 5 proc` – Documentation on the pseudo-filesystem that provides an interface to kernel data structures, specifically how it exposes process information via `/proc/[pid]`.
5.  **LWN.net - Mount namespaces and shared subtrees** : https://lwn.net/Articles/689856

