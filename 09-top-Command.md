# Linux `top` Command: In-Depth Guide

The `top` (Table Of Processes) command is an essential, pre-installed utility in almost all Linux distributions. It provides a real-time, dynamic view of the running system, including CPU usage, memory consumption, and a list of all processes currently being handled by the Linux kernel.

If you ever experience server slowness or a crashed application, `top` is usually the first command you run.

---

## 1. Understanding the Header (System Summary)
When you run `top`, the top 5 lines provide a crucial high-level summary of the entire system's health.

### Line 1: Uptime and Load Average
`top - 14:22:15 up 15 days,  4:32,  2 users,  load average: 0.75, 0.80, 0.65`
*   **14:22:15**: Current system time.
*   **up 15 days...**: How long the system has been running since the last reboot.
*   **load average**: Shows the system load over the last **1 minute**, **5 minutes**, and **15 minutes**. 
    *   *Rule of Thumb*: A load average of `1.0` on a single-core CPU means it is at 100% capacity. If you have 4 cores, `4.0` means 100% capacity.

### Line 2: Tasks
`Tasks: 120 total,   1 running, 119 sleeping,   0 stopped,   0 zombie`
*   **running**: Currently executing on the CPU.
*   **sleeping**: Waiting for an event to wake them up (normal state for most apps).
*   **stopped**: Paused (often by a user sending a stop signal).
*   **zombie**: A process that has finished execution but hasn't been cleaned up by its parent process.

### Line 3: CPU States
`%Cpu(s):  5.2 us,  2.1 sy,  0.0 ni, 92.5 id,  0.2 wa,  0.0 hi,  0.0 si,  0.0 st`
This is arguably the most important line. It shows what the CPU is spending its time doing:
*   **us (User)**: Time spent running normal user applications (e.g., node, python).
*   **sy (System)**: Time spent running kernel-level operations.
*   **id (Idle)**: Time spent doing absolutely nothing (You want this to be high!).
*   **wa (I/O Wait)**: Time the CPU spends waiting for the hard drive/disk to finish reading or writing. *High `wa` means you have a disk bottleneck.*
*   **st (Steal Time)**: Relevant for virtual machines (VMs). It's the time the hypervisor "stole" from this VM to give to another VM.

### Lines 4 & 5: Memory and Swap
`MiB Mem :   7950.2 total,    500.1 free,   4000.5 used,   3449.6 buff/cache`
`MiB Swap:   2048.0 total,   2048.0 free,      0.0 used.   3500.2 avail Mem `
*   **buff/cache**: Memory the kernel uses to cache files for faster disk access. This is automatically freed if an app needs RAM.
*   **avail Mem**: The actual amount of RAM available for new applications to start without swapping.

---

## 2. Understanding the Task Area (The Columns)
Below the header is the list of processes.

*   **PID**: Process ID (Unique number).
*   **USER**: The user who started the process.
*   **PR / NI**: Priority and Nice value. Lower numbers mean higher priority.
*   **VIRT**: Virtual Memory. Total memory the process *claims* it might need.
*   **RES**: Resident Memory. The actual physical RAM the process is consuming right now. *(This is the most important memory metric).*
*   **SHR**: Shared memory used by the process.
*   **S**: Process State (R=Running, S=Sleeping, Z=Zombie).
*   **%CPU**: Percentage of CPU time the process is using.
*   **%MEM**: Percentage of physical RAM (RES) the process is using.
*   **TIME+**: Total CPU time the process has used since it started.
*   **COMMAND**: The name of the program or script.

---

## 3. Interactive Keybindings
The true power of `top` is that you can control it while it's running. Simply press these keys:

### Sorting and Display
*   **`P`** (Capital P): Sort processes by **CPU usage** (Default).
*   **`M`** (Capital M): Sort processes by **Memory usage** (RES).
*   **`T`** (Capital T): Sort processes by **Time** running.
*   **`1`** (Number one): Toggle between seeing an average of all CPUs or seeing each individual CPU core.
*   **`c`**: Toggle showing the full absolute path of the `COMMAND` instead of just the program name.
*   **`V`**: Toggle "Forest View" (shows parent-child relationships).
*   **`z`**: Toggle color highlights (makes the active rows red).

### Filtering
*   **`u`**: Filter by a specific user. It will prompt you to type a username (e.g., `root`). Press Enter.

### Taking Action
*   **`k` (Kill)**: Press `k`, type a PID, press Enter, then type `9` (for SIGKILL) and press Enter to instantly terminate a rogue process.
*   **`r` (Renice)**: Change the priority of a running process.
*   **`W` (Write)**: Save your current layout/sorting preferences to `~/.toprc` so `top` looks exactly the same next time you open it.

---

## 4. Practical Troubleshooting Scenarios

### Scenario A: The CPU Hog
**The Problem**: The server is responding very slowly.
1.  Run `top`.
2.  Look at the `%Cpu(s)` line. You see `us` is at **99.0%**.
3.  Press **`P`** to ensure it's sorted by CPU.
4.  You see a process named `ffmpeg` at the top using 98% CPU.
5.  **The Fix**: You press **`k`**, type the PID of `ffmpeg`, and hit Enter twice to gracefully shut it down.

### Scenario B: The "Silent" Slowness (I/O Wait)
**The Problem**: The server is slow, but CPU usage is low.
1.  Run `top`.
2.  Look at the `%Cpu(s)` line. You see `us` is at 5%, but **`wa` (Wait)** is at **80.0%**.
3.  **The Diagnosis**: Your CPU is incredibly fast, but it spends 80% of its time doing nothing, waiting for your slow hard drive to finish reading/writing files.
4.  **The Fix**: This is an IO bottleneck. You cannot fix this with `top`. You need to use `iotop` to find the process thrashing the disk, or upgrade to an SSD.

### Scenario C: The Memory Leak
**The Problem**: An application keeps crashing every 3 days.
1.  Run `top`.
2.  Press **`M`** to sort by Memory.
3.  You observe a Java application where the **RES** column is slowly growing every few seconds, but never going down.
4.  **The Diagnosis**: The application has a memory leak. It is claiming physical RAM and never releasing it until the OS is forced to kill it (OOM Killer).
