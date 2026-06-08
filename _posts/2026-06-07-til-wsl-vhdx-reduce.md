---
title:  TIL - Reclaiming 160 GB from WSL2's Hidden Disk File
description: Today I Learned that deleting files inside WSL2 doesn't shrink your Windows disk usage — and one PowerShell command fixes it
author: winhtaikaung 
date: 2026-06-06 11:33:00 +0800
categories: [Windows 11, TroubleShooting]
tags: [WSL2, Windows 11, Hyper-V t,Disk Management]
pin: false
math: true
mermaid: true
image:
 path: https://images.unsplash.com/photo-1562873516-80bd6aa6e810
  # path: https://images.unsplash.com/photo-1718306201865-cae4a08311fe 
 alt: man-walking-inside-building-going-on-escalator
 
---


# TIL: Reclaiming 160 GB from WSL2's Hidden Disk File

> **Today I Learned** that deleting files inside WSL2 doesn't shrink your Windows disk usage  and one PowerShell command fixes it.


## The Story

It started with a Windows notification: **low disk space**. My SSD was nearly full and I had no idea why.

**Step 1  Cleaned the obvious suspects.** As a JavaScript developer, I went straight for the usual culprits: Docker images, volumes, and mountains of `node_modules`. Cleared them all. The free space barely moved.

**Step 2  Brought in a disk sniffer.** Tools like [WinDirStat](https://windirstat.net/) or [TreeSize Free](https://www.jam-software.com/treesize_free) visualize exactly what's eating your disk. That's when I found it  a single `ext4.vhdx` file sitting quietly inside `%LOCALAPPDATA%\Packages\...`, consuming **240 GB**.

**Step 3  One PowerShell command later.** The VHDX compacted from **240 GB → 80 GB**. That's 160 GB reclaimed in minutes.

---

## What Is a VHDX?

A **VHDX (Virtual Hard Disk v2)** is a virtual disk format used by Hyper-V on Windows. WSL2 runs inside a lightweight Hyper-V VM, and it stores your entire Linux filesystem  every file, permission, symlink, and inode  inside a single `ext4.vhdx` file on your Windows drive.

**The problem:** the VHDX grows as you create files inside WSL, but **it does not shrink automatically** when you delete them. The space is returned to the Linux filesystem, but the Windows-side file stays the same size  like a balloon that never deflates on its own.

---

## The Fix

### Prerequisites

- Windows 11 with WSL2 installed
- Hyper-V feature enabled (Pro/Enterprise, or Home with workaround)
- PowerShell running as **Administrator**
- A disk sniffer (WinDirStat, TreeSize) to locate your `ext4.vhdx` path

### Step 1  Shut down WSL

The VHDX must not be in use during compaction.

```powershell
wsl --shutdown
```

### Step 2  Navigate to the VHDX

The file lives inside your distro's package folder. Replace the `...` with your actual distro path.

*Important* Do not even hover that file from your explorer lol. IMO its better you restart your machine first and straight open powershell with administrator access.
```powershell
cd ~\AppData\Local\Packages\CanonicalGroupLimited.Ubuntu..._...\LocalState
```

### Step 3  Compact the disk

```powershell
Optimize-VHD -Path .\ext4.vhdx -Mode Full
```

`-Mode Full` removes all unused blocks  the most thorough option. This is the command that dropped my VHDX from 240 GB to 80 GB.

> **Note:** If `Optimize-VHD` is not recognized, Hyper-V may not be enabled. Run the following in an elevated PowerShell, then restart:
> ```powershell
> Enable-WindowsOptionalFeature -Online -FeatureName Microsoft-Hyper-V-All
> ```

---

## Results

| | Before | After |
|---|---|---|
| `ext4.vhdx` size | 240 GB | 80 GB |
| Space reclaimed |  | **160 GB (67% reduction)** |

---

## Key Takeaway

WSL2 silently accumulates disk usage over time  especially for JavaScript developers cycling through `node_modules` and Docker layers.

**Deleting files inside WSL frees Linux disk space, but not the Windows VHDX.**

Run `Optimize-VHD -Mode Full` periodically, or whenever your SSD looks suspiciously full, to keep it honest.

---

## Quick Reference

```powershell
# 1. Shut down WSL
wsl --shutdown

# 2. Go to your distro's LocalState folder
cd ~\AppData\Local\Packages\CanonicalGroupLimited.Ubuntu..._...\LocalState
cd ~\AppData\Local\Docker\wsl\data\

# 3. Compact the virtual disk
Optimize-VHD -Path .\ext4.vhdx -Mode Full
```

---

*Tested on Windows 11 with Ubuntu 22.04 on WSL2.*
