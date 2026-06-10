---
title:  Learning Notes - 🐧 Linux Process 
description: Linux Processes & Memory Management Notes 🐧
author: winhtaikaung 
date: 2026-06-06 11:33:00 +0800
categories: [Operating Systems, Linux]
tags: [Linux, OS, Process]
pin: true
math: true
mermaid: true
image:
 path: https://images.unsplash.com/photo-1704026437278-77f1db74859c
 alt: hdd-grayscale
author: win
---

# Preface
Luckily I have received some calls these days and as always I wasn't lucky enough to hook first opportunity. I have been switching my hats to Platform Reliability Engineer and System Administration path for almost 2 years. However I wasn't paying attention to learn details due to errands and family migration process. So Long story short I got interview for sysadmin and cloud engineering role. They are very happy with my cloud engineering skills ,TOIL reduction initiatives and that lasted before this question "Can you elaborate how a binary file from disk become a process". I froze at first, I understand only very high level yea but I only have limited understandings. However I don't like to bluff that I don't know. 

Like we play games , if you killed a hero you will get gold & xp,if you got killed you got xp anyways its gain for me. So as soon as I left the interview room I was in a shock that I have read some of the OS 3 Easypieces(trust me it is not easy book to read) however I couldn't explain(sad) clearly. One of my friend told me once "If you can't explain something to other person, it means you are not fluent it that subject." Thats why I wrote this just to explain myself and verify my understandings. Lets get our hands dirty.

## 1. Program vs. Process
First, It is crucial to understand the difference between a program and a process.

*   **Program:** A static file sitting on your hard drive (disk). It contains machine code and data, but it is **not** doing anything.
*   **Process:** An active, executing instance of a program in RAM. It is dynamic and has a current state (CPU registers, memory, open files).

```text
  [ Hard Drive ]                      [ RAM ]
  +------------+      OS Loader       +------------+
  |  Program   |  ---------------->   |  Process   |
  | (Static)   |      (Execution)     | (Dynamic)  |
  +------------+                      +------------+
```

## 2. Virtual Memory Layout
Linux gives every process its own "Virtual Memory"(I will write more about Virtual Memory Mappings). This means every process thinks it has the entire computer's memory to itself, isolated from other processes. 

Here is the standard layout of a process's virtual memory from the lowest address to the highest. Again this is visualized layouts and the way stack & heap grows was decided by Application Binary Interface.


### Layout
```text
High Memory Address
  +-------------------------+
  |  Command Line Args &    |
  |  Environment Variables  |
  +-------------------------+
  |         Stack           |  <-- Grows DOWNWARD (Local variables, function calls)
  |           ↓             |
  |           |             |
  |           |             |
  |           ↑             |
  |         Heap            |  <-- Grows UPWARD (Dynamic memory allocation, malloc)
  +-------------------------+
  |       .bss Segment      |  <-- Uninitialized global/static variables (Auto-filled with 0s)
  +-------------------------+
  |       .data Segment     |  <-- Initialized global/static variables
  +-------------------------+
  |       .text Segment     |  <-- Executable machine code (Read-Only)
  +-------------------------+
Low Memory Address
```

Quick Tips: The easist way to check whether your programs stack is growing downward or upwards by doing this 

```c

#include<stdio.h>
#include<stdlib.h>
int doSome(){
    int x = 1;
    printf("Address of x from F1 - doSome: %p \n",(void *)&x);// 0x7ffe45b54b14 This stack address is smaller
    return x;
}

int main(){
   int x = 1;

   printf("Address of x from F2 - main: %p \n",(void *)&x); // 0x7ffe45b54b34
   doSome();

    // Allocate memory for two pointers
    int *ptr1 = (int *)malloc(sizeof(int));
    int *ptr2 = (int *)malloc(sizeof(int));


   printf("--------------------------------HEAP Allocation--------------------------------\n");
    // Print the addresses of the allocated memory
    printf("Address of ptr1: %p\n", (void *)ptr1); 
    printf("Address of ptr2: %p\n", (void *)ptr2); // THis heap address is larger than ptr1

    // Free the allocated memory
    free(ptr1);
    free(ptr2);
   return 1;
}

```

### Output 
```
Address of x from F2 - main: 0x7ffe45b54b34
Address of x from F1 - doSome: 0x7ffe45b54b14
--------------------------------HEAP Allocation--------------------------------
Address of ptr1: 0x5a90991116b0
Address of ptr2: 0x5a90991116d0 <-- Larger
```

Based on the Output the main stack address of x `0x7ffe45b54b34 ` is larger than address of x inside doSome `0x7ffe45b54b14` it means the more stack grows, the smaller the address is. 

However when it comes to heap allocation the heap address of ptr2 `0x5a90991116d0` is larger than heap address of ptr1 `0x5a90991116b0`. So the more the heap grows. the larger the address is.


## 3. The `.bss` Segment & The `size` Command
When you compile a C program, you can use the `size` command to see how much memory the different segments take up.

**The C Program**
```c
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>

char globBuf[65536];            /* Uninitialized data segment (.bss) */
int primes[] = { 2, 3, 5, 7 };  /* Initialized data segment (.data) */

static int square(int x) {
    int result;
    result = x * x;
    return result;
}

static void doCalc(int val) {
    printf("The square of %d is %d\n", val, square(val));
    if (val < 1000) {
        int t;
        t = val * val * val;
        printf("The cube of %d is %d\n", val, t);
    }
}

int main(int argc, char *argv[]) {
    static int key = 9973;              /* Initialized data segment (.data) */
    static char mbuf[10240000];         /* Uninitialized data segment (.bss) - 10 MB! */
    char *p;                            /* Allocated in frame for main() (Stack) */

    p = malloc(1024);                   /* Points to memory in heap segment */
    doCalc(key);
    sleep(1000);
    exit(EXIT_SUCCESS);
}
```

**Example:**
```bash
$ size ex1_init_uninit
   text    data     bss     dec     hex filename
   1918     644 10305568        10308130         9d4a22 ex1_init_uninit
```
*   `text`: The actual code.
*   `data`: Initialized global variables.
*   `bss`: Uninitialized global variables (takes 0 bytes on disk!).

**What is `.bss`?**
*   **BSS** stands for *Block Started by Symbol*.
*   It holds **uninitialized** (or explicitly zero-initialized) global and static variables.
*   **The Magic Trick:** The `.bss` segment **does not take up any space on your hard drive**. Instead of writing millions of zeros to your disk, the executable file just contains a tiny instruction telling the OS: *"When you load this program into RAM, allocate this much memory and fill it with zeros."*


## 4. Inspecting Memory with `/proc/PID/maps`
Linux exposes the virtual memory layout of any running process via a special virtual file: `/proc/<PID>/maps`. 

When you run `cat /proc/<PID>/maps`, you get a text-based map of the process's memory. Here is how to read the columns:

```text
Address Range          Perms  Offset   Dev   Inode   Pathname/Tag
[Start]-[End]          [rwxp] [Offset] [Dev] [Inode] [Mapped File]
```

**Column Breakdown:**
1.  **Address Range:** The starting and ending virtual memory addresses for this block.
2.  **Permissions (`rwxp`):** 
    *   `r` = read, `w` = write, `x` = execute. 
    *   `p` = private (copy-on-write), `s` = shared.
3.  **Offset:** If this memory is mapped to a file on disk, this is the offset (where in the file this memory chunk starts).
4.  **Device:** The major and minor device numbers of the disk where the file lives.
5.  **Inode:** The inode number of the file on the disk.
6.  **Pathname:** The file that is mapped into memory. If it's not a file, it will show a tag like `[heap]`, `[stack]`, or `[vdso]`.

**Visualizing the Output:**
```bash
08048000-0804a000 r-xp 00000000 08:05 12345  /bin/my_program  <-- .text (Code)
0804a000-0804b000 rw-p 00002000 08:05 12345  /bin/my_program  <-- .data/.bss
b7e00000-b7e21000 rw-p 00000000 00:00 0      [heap]           <-- Heap
bffeb000-bffff000 rw-p 00000000 00:00 0      [stack]          <-- Stack
---------------------------------------------------------------------------------------------------------------------------------

$ cat /proc/<YourProgramID>/maps
5c4a47894000-5c4a47895000 r--p 00000000 08:01 525302                     /home/ubuntu/Documents/ex1_init_uninit <-- .text (Code)
5c4a47895000-5c4a47896000 r-xp 00001000 08:01 525302                     /home/ubuntu/Documents/ex1_init_uninit <-- .text (Code)
5c4a47896000-5c4a47897000 r--p 00002000 08:01 525302                     /home/ubuntu/Documents/ex1_init_uninit <-- .text (Code)
5c4a47897000-5c4a47898000 r--p 00002000 08:01 525302                     /home/ubuntu/Documents/ex1_init_uninit <-- .text (Code)
5c4a47898000-5c4a47899000 rw-p 00003000 08:01 525302                     /home/ubuntu/Documents/ex1_init_uninit <-- .text (Code)
5c4a47899000-5c4a4826d000 rw-p 00000000 00:00 0
-----------------------------------------------------------------------------------------------------------------------------------
5c4a5ec11000-5c4a5ec32000 rw-p 00000000 00:00 0                          [heap] <-- Heap
-----------------------------------------------------------------------------------------------------------------------------------
7f4acf600000-7f4acf628000 r--p 00000000 08:01 4041                       /usr/lib/x86_64-linux-gnu/libc.so.6  <-- These Are the shared libraries like libc.so they can be seen in other processs as well. 
7f4acf628000-7f4acf7b0000 r-xp 00028000 08:01 4041                       /usr/lib/x86_64-linux-gnu/libc.so.6    
7f4acf7b0000-7f4acf7ff000 r--p 001b0000 08:01 4041                       /usr/lib/x86_64-linux-gnu/libc.so.6
7f4acf7ff000-7f4acf803000 r--p 001fe000 08:01 4041                       /usr/lib/x86_64-linux-gnu/libc.so.6
7f4acf803000-7f4acf805000 rw-p 00202000 08:01 4041                       /usr/lib/x86_64-linux-gnu/libc.so.6
7f4acf805000-7f4acf812000 rw-p 00000000 00:00 0
-----------------------------------------------------------------------------------------------------------------------------------
7f4acf96a000-7f4acf96d000 rw-p 00000000 00:00 0
-----------------------------------------------------------------------------------------------------------------------------------
7f4acf973000-7f4acf975000 rw-p 00000000 00:00 0                           <-- Dynamic Linkers 
7f4acf975000-7f4acf976000 r--p 00000000 08:01 4038                       /usr/lib/x86_64-linux-gnu/ld-linux-x86-64.so.2
7f4acf976000-7f4acf9a1000 r-xp 00001000 08:01 4038                       /usr/lib/x86_64-linux-gnu/ld-linux-x86-64.so.2
7f4acf9a1000-7f4acf9ab000 r--p 0002c000 08:01 4038                       /usr/lib/x86_64-linux-gnu/ld-linux-x86-64.so.2
7f4acf9ab000-7f4acf9ad000 r--p 00036000 08:01 4038                       /usr/lib/x86_64-linux-gnu/ld-linux-x86-64.so.2
7f4acf9ad000-7f4acf9af000 rw-p 00038000 08:01 4038                       /usr/lib/x86_64-linux-gnu/ld-linux-x86-64.so.2  
-----------------------------------------------------------------------------------------------------------------------------------
7ffc5f104000-7ffc5f125000 rw-p 00000000 00:00 0                          [stack] <-- Stack
-----------------------------------------------------------------------------------------------------------------------------------
7ffc5f1dc000-7ffc5f1e0000 r--p 00000000 00:00 0                          [vvar] <-- Virtual vDSO Data
7ffc5f1e0000-7ffc5f1e2000 r-xp 00000000 00:00 0                          [vdso] <-- Vitual VDSO 
ffffffffff600000-ffffffffff601000 --xp 00000000 00:00 0                  [vsyscall] <-- Important vSyscall link to fix page of Kernel Code into userspace that only has permission to execute.
```

### The Stack and Kernel Virtual Mappings
```text
[heap]:  The heap is used for dynamic memory allocation (e.g., via malloc()). It is an anonymous mapping (no file, offset 00000000, device 00:00) and is marked rw-p.It is an anonymous mapping (no file, offset 00000000, device 00:00) and is marked rw-p.

[stack]: The user-space Stack. Used for function call frames, local variables, and function arguments. It grows downwards.

[vvar] (Virtual vDSO data): Read-only data provided by the kernel to user space. It contains things like the current time and timezone information.

[vdso] (Virtual Dynamically-linked Shared Object): This is a brilliant optimization. It is a tiny shared library injected by the kernel into every user process. It allows user-space programs to execute certain kernel functions (like gettimeofday() or clock_gettime()) directly in user space without the massive performance overhead of a context switch (trapping into kernel mode).

[vsyscall]: A legacy mechanism (mostly superseded by vdso on modern kernels) that mapped a fixed page of kernel code into user space to speed up specific system calls. Notice it has execute permissions (--xp) but no read/write permissions.
```

***

### 💡 Tip 
Whenever debugging a memory leak or a segmentation fault in C, remember the **Heap** and the **Stack**. 
*   If you forget to `free()` memory, the **Heap** grows until the OS kills your program.
*   If you create too many local variables or infinite recursion, the **Stack** grows downward until it crashes into the Heap (Stack Overflow / Segfault).

If you Interested more about Shared Library , you can see the process map of `sleep`
```bash
ubuntu@linux-lab-1-pce:~/Documents$ sleep 1000 &
[1] 36440
ubuntu@linux-lab-1-pce:~/Documents$ PID=$!
ubuntu@linux-lab-1-pce:~/Documents$ cat /proc/$PID/maps
58293593c000-58293593e000 r--p 00000000 08:01 1985                       /usr/bin/sleep
58293593e000-582935942000 r-xp 00002000 08:01 1985                       /usr/bin/sleep
582935942000-582935943000 r--p 00006000 08:01 1985                       /usr/bin/sleep
582935943000-582935944000 r--p 00007000 08:01 1985                       /usr/bin/sleep
582935944000-582935945000 rw-p 00008000 08:01 1985                       /usr/bin/sleep
582966ce7000-582966d08000 rw-p 00000000 00:00 0                          [heap]
784b45600000-784b458eb000 r--p 00000000 08:01 41681                      /usr/lib/locale/locale-archive
784b45a00000-784b45a28000 r--p 00000000 08:01 4041                       /usr/lib/x86_64-linux-gnu/libc.so.6
784b45a28000-784b45bb0000 r-xp 00028000 08:01 4041                       /usr/lib/x86_64-linux-gnu/libc.so.6
784b45bb0000-784b45bff000 r--p 001b0000 08:01 4041                       /usr/lib/x86_64-linux-gnu/libc.so.6
784b45bff000-784b45c03000 r--p 001fe000 08:01 4041                       /usr/lib/x86_64-linux-gnu/libc.so.6
784b45c03000-784b45c05000 rw-p 00202000 08:01 4041                       /usr/lib/x86_64-linux-gnu/libc.so.6
784b45c05000-784b45c12000 rw-p 00000000 00:00 0
784b45c17000-784b45c70000 r--p 00000000 08:01 4081                       /usr/lib/locale/C.utf8/LC_CTYPE
784b45c70000-784b45c71000 r--p 00000000 08:01 4087                       /usr/lib/locale/C.utf8/LC_NUMERIC
784b45c71000-784b45c72000 r--p 00000000 08:01 4121                       /usr/lib/locale/C.utf8/LC_TIME
784b45c72000-784b45c73000 r--p 00000000 08:01 4080                       /usr/lib/locale/C.utf8/LC_COLLATE
784b45c73000-784b45c74000 r--p 00000000 08:01 4085                       /usr/lib/locale/C.utf8/LC_MONETARY
784b45c74000-784b45c75000 r--p 00000000 08:01 4084                       /usr/lib/locale/C.utf8/LC_MESSAGES/SYS_LC_MESSAGES
784b45c75000-784b45c7c000 r--s 00000000 08:01 4029                       /usr/lib/x86_64-linux-gnu/gconv/gconv-modules.cache
784b45c7c000-784b45c7f000 rw-p 00000000 00:00 0
784b45c7f000-784b45c80000 r--p 00000000 08:01 4119                       /usr/lib/locale/C.utf8/LC_PAPER
784b45c80000-784b45c81000 r--p 00000000 08:01 4086                       /usr/lib/locale/C.utf8/LC_NAME
784b45c81000-784b45c82000 r--p 00000000 08:01 4079                       /usr/lib/locale/C.utf8/LC_ADDRESS
784b45c82000-784b45c83000 r--p 00000000 08:01 4120                       /usr/lib/locale/C.utf8/LC_TELEPHONE
784b45c83000-784b45c84000 r--p 00000000 08:01 4083                       /usr/lib/locale/C.utf8/LC_MEASUREMENT
784b45c84000-784b45c85000 r--p 00000000 08:01 4082                       /usr/lib/locale/C.utf8/LC_IDENTIFICATION
784b45c85000-784b45c87000 rw-p 00000000 00:00 0
784b45c87000-784b45c88000 r--p 00000000 08:01 4038                       /usr/lib/x86_64-linux-gnu/ld-linux-x86-64.so.2
784b45c88000-784b45cb3000 r-xp 00001000 08:01 4038                       /usr/lib/x86_64-linux-gnu/ld-linux-x86-64.so.2
784b45cb3000-784b45cbd000 r--p 0002c000 08:01 4038                       /usr/lib/x86_64-linux-gnu/ld-linux-x86-64.so.2
784b45cbd000-784b45cbf000 r--p 00036000 08:01 4038                       /usr/lib/x86_64-linux-gnu/ld-linux-x86-64.so.2
784b45cbf000-784b45cc1000 rw-p 00038000 08:01 4038                       /usr/lib/x86_64-linux-gnu/ld-linux-x86-64.so.2
7ffed59f2000-7ffed5a13000 rw-p 00000000 00:00 0                          [stack]
```
***





