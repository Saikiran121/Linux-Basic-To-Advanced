# Linux Commands: The `top` Command

The `top` (Table Of Processes) command is a real-time system monitor. It provides a dynamic, interactive view of the processes running on your system, along with critical resource usage data.

---

## 1. What is the `top` command?
Think of `top` as the **Task Manager** for Linux. While `ps` gives you a static snapshot (a photo), `top` gives you a live feed (a video). It updates every few seconds by default.

---

## 2. Understanding the Dashboard (The Header)
When you run `top`, the top 5 lines provide a summary of the whole system:

1.  **System Time & Uptime**: `top - 15:30:10 up 5 days, 2:10` (Current time, time since last boot).
2.  **Tasks**: `Tasks: 250 total, 1 running, 249 sleeping` (Total processes and their states).
3.  **CPU %**: `0.3 us, 0.7 sy` (User vs System CPU usage).
4.  **Memory (MiB)**: `8000 total, 5000 used, 3000 free` (Physical RAM usage).
5.  **Swap (MiB)**: `2000 total, 100 used` (Virtual memory usage on disk).

---

## 3. Interactive Keybindings (Live Controls)
The power of `top` is that you can control it while it's running:

*   **`q`**: Quit `top`.
*   **`M`**: Sort by **Memory** usage (find RAM-hungry apps).
*   **`P`**: Sort by **CPU** usage (find apps slowing down the PC).
*   **`k`**: **Kill** a process. It will ask for the PID and then the signal (e.g., 9 for SIGKILL).
*   **`r`**: **Renice** a process. Change the priority of a running process.
*   **`1`**: Toggle view to show individual CPU cores (if you have multiple).
*   **`h`**: Show the Help screen with all available keys.

---

## 4. Troubleshooting Scenario: The Rogue Script
**Scenario**: Your laptop fan is spinning loudly, and your web browser is laggy. You suspect a background script is stuck.

1.  **Open Monitoring**: Type `top` in your terminal.
2.  **Sort by CPU**: Press `P` to ensure the most CPU-intensive apps are at the top.
3.  **Identify**: You see a process named `backup.sh` using **98.5% CPU**.
4.  **Take Action**:
    *   Note its PID (e.g., `4521`).
    *   Press `k`.
    *   Type `4521` and hit Enter.
    *   Hit Enter again to send the default signal (`15` / SIGTERM).
5.  **Result**: The process disappears, your fan slows down, and the system becomes responsive again.

---

## 6. The `htop` Command (Modern Alternative)

`htop` is an interactive system-monitor process-viewer and manager. It is designed to be a more user-friendly and visually appealing version of `top`.

### Key Features:
*   **Visual Bars**: Uses horizontal bars to show CPU, Memory, and Swap usage at a glance.
*   **Mouse Support**: You can use your mouse to click on processes, sort columns, and click menu buttons.
*   **Color-Coded**: Helps you quickly distinguish between different process types (e.g., green for user processes, red for kernel).
*   **Functional Menu**: The bottom bar lists functional keys (F1-F10) for common actions.

### Essential Functional Keys:
| Key | Action | Use Case |
| :--- | :--- | :--- |
| **F2** | **Setup** | Customize colors, columns, and display options. |
| **F3** | **Search** | Find a process by typing its name. |
| **F4** | **Filter** | Only show processes that match a specific string (e.g., "apache"). |
| **F5** | **Tree** | Shows the parent-child relationship in a tree view. |
| **F6** | **SortBy** | Open a menu to select which column to sort by. |
| **F9** | **Kill** | Open a menu of all Linux signals (SIGTERM, SIGKILL, etc.) to send to a process. |
| **F10** | **Quit** | Exit `htop`. |

---

## 7. Troubleshooting Scenario: Finding a Hidden Service
**Scenario**: You know a web server is running, but you can't find it in the long list of hundreds of processes in `top`.

1.  **Open htop**: Launch it by typing `htop`.
2.  **Filter Results**: Press **F4** and type `nginx`.
3.  **The Result**: All other processes disappear, and only the Nginx master and worker processes remain on the screen.
4.  **Check Relationships**: Press **F5** to see exactly which worker process belongs to which master process.
5.  **Examine details**: Use the arrow keys to select a process and see its full command path.

---

## 8. Summary: `top` vs `htop`
*   **Use `top`**: When you are on a minimal server or a system where you cannot install new software. It is always there.
*   **Use `htop`**: For day-to-day development and troubleshooting on your own workstation or servers where you have control. It is much more efficient for human eyes and hands.

---

## 9. The `vmstat` Command (System Statistics)

`vmstat` (Virtual Memory Statistics) provides a condensed view of system processes, memory, paging, block IO, traps, and CPU activity. It is excellent for identifying deep system bottlenecks.

### Basic Usage:
```bash
vmstat 1 5
```
This runs `vmstat` every **1 second**, for a total of **5 times**. This is much more useful than a single snapshot because it shows trends.

### Understanding the Key Columns:
*   **procs / r**: Processes waiting for runtime. If this is consistently higher than your CPU core count, your CPU is overwhelmed.
*   **memory / free**: Amount of idle memory.
*   **swap / si & so**: Amount of memory swapped **in** from disk (`si`) or swapped **out** to disk (`so`). Non-zero values here often mean you've run out of RAM.
*   **io / bi & bo**: Blocks received **in** (`bi`) and sent **out** (`bo`) to a block device (disk).
*   **cpu / wa**: **IO Wait**. This is the percentage of time the CPU was idle while waiting for an IO request to complete.

---

## 10. Troubleshooting Scenario: The "Silent" Slowness
**Scenario**: Your web server is responding slowly. You check `top`, and the `%CPU` usage is only **5%**. The system is not "busy," but it's still slow.

1.  **Run vmstat**: Type `vmstat 1`.
2.  **Observe the columns**:
    *   You notice **`wa` (IO Wait)** is at **60%**.
    *   You see **`bo` (Blocks Out)** is very high.
3.  **The Diagnosis**: The CPU is fast and bored, but it's "waiting" for the disk to finish writing data.
4.  **The Root Cause**: A background backup script or a database heavy-write operation is saturating the disk bandwidth.
5.  **Solution**: Identify the process doing heavy IO (using `iotop`) and either stop it or lower its priority.

---

## 11. Final Summary of Monitoring Commands
| Command | Best For... |
| :--- | :--- |
| **top** | Standard real-time dashboard (pre-installed). |
| **htop** | Improved real-time dashboard (interactive/visual). |
| **vmstat** | Detecting IO Wait and Memory Swapping trends. |
| **iotop** | Finding which specific process is hogging the disk. |
| **ps aux** | One-time snapshot to search/filter processes. |
| **free -m** | Quick overview of RAM and Swap usage. |

---

## 12. The `free` Command (Memory Overview)

The `free` command provides a clean summary of the system's used and available physical (RAM) and swap memory.

### Understanding the Columns:
*   **total**: Total physical RAM installed.
*   **used**: Memory currently used by processes.
*   **free**: Memory that is completely idle and doing nothing.
*   **buff/cache**: Memory used by the kernel for temporary storage to speed up disk access. **This is good!** Linux will give this back to applications if they need it.
*   **available**: **The most important number.** This is how much RAM is actually available for new applications to start without causing the system to slow down (swap).

### Usage and Units (Flags):
*   **`free -m`**: Displays memory in **Megabytes** (standard for medium systems).
*   **`free -h`**: Displays memory in **human-readable** format. It automatically scales the units (K, M, G, T) so you don't have to count zeros.

---

## 13. Scenario A: The "False" Out-of-Memory Scare
**Scenario**: You are a junior admin. You run `free -m` and see that `free` is only **50 MB** on a 16 GB server. You panic, thinking the server is about to crash.

1.  **Analyze the output**:
    *   **Total**: 16,000 MB
    *   **Used**: 4,000 MB
    *   **Free**: 50 MB
    *   **Buff/Cache**: 11,950 MB
    *   **Available**: 11,500 MB
2.  **The Diagnosis**: You are **not** out of memory!
3.  **The Explanation**: Linux is using almost all the "free" memory for **Cache** to make the system fast. However, it is ready to give that 11.5 GB back to any app that asks for it.
4.  **The Rule of Thumb**: Don't look at `free`; always look at **`available`**.

---

## 14. Scenario B: The "Big Server" Analysis (`free -h`)
**The Problem**: You are managing a high-performance database server with **512 GB of RAM**. If you run `free -m`, the numbers are massive and hard to read.

*   **Output with `free -m`**:
    ```text
    Mem:         524288      102400      421888
    ```
*   **Output with `free -h`**:
    ```text
    Mem:           512G        100G        412G           400G
    ```

**The Benefit**:
*   **Instant Understanding**: You immediately see `512G` instead of a 6-digit number.
*   **Reporting**: It is much easier to copy-paste `free -h` output into a status report or an email.

---

## 15. The `nproc` Command (CPU Core Detection)

The `nproc` command is a simple utility that prints the number of processing units (CPU cores) available to the current process.

### Usage:
*   `nproc`: Prints the number of available processing units.
*   `nproc --all`: Prints the number of installed processors (even if some are offline).

---

## 16. Scenario: Speeding Up Software Installation
**The Problem**: You are installing a large software package from source code using the `make` command. By default, `make` uses only **1 CPU core**, even if your server has **32 cores**. This makes the installation take 30 minutes instead of 1 minute.

1.  **Check your cores**: Run `nproc`.
    *   Result: `16`
2.  **Optimize the build**: Run the installation with the `-j` (jobs) flag, using the output of `nproc` as the argument.
    ```bash
    make -j$(nproc)
    ```
3.  **The Result**: The `make` command starts **16 parallel processes**, using every bit of your CPU's power.
4.  **The Benefit**: The installation finishes **16 times faster**.

---

## 17. Final Summary of Monitoring Commands
| Command | Best For... |
| :--- | :--- |
| **top / htop** | Real-time interactive dashboard. |
| **vmstat** | Spotting IO Wait and Memory Swapping trends. |
| **free -h** | Quick, readable memory check. |
| **nproc** | Detecting CPU cores for script/build optimization. |
| **df -h** | Checking disk space and identifying full partitions. |
| **du -sh** | Finding which specific folders/files are using space. |

---

## 20. The `du -sh` Command (Disk Usage)

While `df` tells you which **partition** is full, `du` tells you which **folder** or **file** is the culprit.

### Understanding the Flags:
*   **`-s` (Summary)**: Only shows the total size for each argument. Without this, `du` will list every single sub-folder and file, which can be thousands of lines.
*   **`-h` (Human-readable)**: Converts sizes into **K**, **M**, and **G**.

### Difference: `df` vs `du`
*   **`df` (Disk Free)**: Checks the filesystem metadata. It's fast and shows total partition health.
*   **`du` (Disk Usage)**: Scans the actual files. It's slower but tells you exactly where the space went.

---

## 21. Troubleshooting Scenario: Finding the Space Hog
**Scenario**: You ran `df -h` and saw that your `/home` partition is **98% full**. You have 10 different users on the server. You need to find out who is using the space.

1.  **Check all user folders**: Run `sudo du -sh /home/*`.
2.  **Analyze the output**:
    ```text
    4.0K    /home/alex
    12G     /home/bob
    500M    /home/charlie
    ```
    (Clearly, **Bob** is the one using the most space).
3.  **Dig deeper into Bob's folder**:
    ```bash
    sudo du -sh /home/bob/* | sort -h
    ```
4.  **The Result**:
    ```text
    100M    /home/bob/projects
    11.9G   /home/bob/Downloads
    ```
5.  **The Fix**: You ask Bob to clean up his `Downloads` folder, and the disk space issue is resolved!

---

## 18. The `df -h` Command (Disk Free)

The `df` command reports the amount of disk space used and available on your Linux file systems.

### The Power of `-h`:
By default, `df` shows sizes in 1-kilobyte blocks, which is impossible to read quickly. The **`-h`** (human-readable) flag converts these into **Gigabytes (G)** and **Megabytes (M)**.

### Understanding the Columns:
*   **Filesystem**: The actual disk partition or virtual drive.
*   **Size**: Total capacity of that partition.
*   **Used**: How much data is currently stored.
*   **Avail**: How much space is left for new files.
*   **Use%**: The most important column for monitoring. If this is **>90%**, you need to take action.
*   **Mounted on**: The folder where this disk is attached (e.g., `/` is root, `/home` is user data).

---

## 19. Troubleshooting Scenario: The "Device Full" Disaster
**Scenario**: You are a developer trying to save a new database dump, but you get the error: `No space left on device`.

1.  **Check disk usage**: Run `df -h`.
2.  **Analyze the output**:
    ```text
    Filesystem      Size  Used Avail Use% Mounted on
    /dev/sda1        50G   49G  500M  99% /
    /dev/sdb1       200G   20G  180G  10% /data
    ```
3.  **The Diagnosis**: Your root partition (`/`) is almost completely full (**99%**), even though your `/data` drive has plenty of space.
4.  **The Fix**:
    *   Find large files in the root partition: `sudo du -sh /* | sort -h`.
    *   Notice that `/var/log` is using 30GB.
    *   Clean up old logs: `sudo journalctl --vacuum-time=7d`.
5.  **Result**: `Use%` drops to 40%, and you can save your files again.
