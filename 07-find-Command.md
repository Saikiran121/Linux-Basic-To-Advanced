# Linux `find` Command: In-Depth Guide

The `find` command is one of the most powerful and flexible utilities in Linux. It is used to search for files and directories in a directory hierarchy based on a user-given expression and can perform user-specified actions on each matched file.

---

## 1. Basic Syntax
```bash
find [path] [options] [expression]
```
*   **`[path]`**: The starting directory for the search (e.g., `.`, `/`, `/var/log`).
*   **`[options]`**: Controls the behavior of the search (e.g., following symlinks).
*   **`[expression]`**: The criteria to search for (name, size, type, time, etc.) and actions to perform (`-delete`, `-exec`).

---

## 2. Searching by Name
The most common use case is finding a file when you know its name or part of its name.

*   **Exact Name**:
    ```bash
    find . -name "report.txt"
    ```
    *(Searches for `report.txt` in the current directory and subdirectories).*

*   **Case-Insensitive Name**:
    ```bash
    find /home -iname "Report.txt"
    ```
    *(Matches `report.txt`, `Report.txt`, `REPORT.TXT`, etc.)*

*   **Wildcards**:
    ```bash
    find /var/log -name "*.log"
    ```
    *(Finds all files ending in `.log`).*

---

## 3. Searching by Type
You can filter the search to only look for files, directories, or symbolic links.

*   **Find only files**: `-type f`
    ```bash
    find /etc -type f -name "*.conf"
    ```
*   **Find only directories**: `-type d`
    ```bash
    find /var -type d -name "log*"
    ```
*   **Find symbolic links**: `-type l`

---

## 4. Searching by Size
Useful for finding files taking up too much disk space.

*   **Larger than 100 Megabytes**:
    ```bash
    find / -type f -size +100M
    ```
*   **Smaller than 10 Kilobytes**:
    ```bash
    find . -type f -size -10k
    ```
*   **Exactly 1 Gigabyte**:
    ```bash
    find . -type f -size 1G
    ```
*(Units: `c` (bytes), `k` (kilobytes), `M` (megabytes), `G` (gigabytes)).*

---

## 5. Searching by Time (Modification, Access, Change)
Find files based on when they were last interacted with.

*   **`-mtime N`**: Modified exactly `N` days ago.
*   **`-mtime -N`**: Modified less than `N` days ago.
*   **`-mtime +N`**: Modified more than `N` days ago.

**Examples**:
*   Find files modified in the last 7 days:
    ```bash
    find /var/log -mtime -7
    ```
*   Find files modified **more than 30 days ago** (great for cleanup):
    ```bash
    find /backup -mtime +30
    ```
*   Find files modified in the **last 60 minutes** (`-mmin`):
    ```bash
    find . -mmin -60
    ```

---

## 6. Searching by Permissions and Ownership
Find files with incorrect security settings.

*   **By Permissions (`-perm`)**:
    ```bash
    find . -type f -perm 0777
    ```
    *(Finds all files with full read/write/execute permissions for everyone - a security risk!).*

*   **By User/Owner (`-user`)**:
    ```bash
    find /home -user saikiran
    ```

---

## 7. Taking Action: `-exec` and `-delete`
This is where `find` becomes incredibly powerful. You can run commands on the files it finds.

*   **Delete matching files**:
    ```bash
    find /tmp -type f -name "*.tmp" -delete
    ```

*   **Execute a command (`-exec`)**:
    The syntax for `-exec` is `-exec command {} \;`.
    *   `{}` is a placeholder for the current file found.
    *   `\;` marks the end of the command.

    **Example: Change permissions of all directories to 755**:
    ```bash
    find /var/www/html -type d -exec chmod 755 {} \;
    ```

    **Example: Move all `.jpg` files to an images folder**:
    ```bash
    find . -name "*.jpg" -exec mv {} /path/to/images/ \;
    ```

---

## 8. Practical Scenarios

### Scenario A: The "Disk Full" Cleanup
**The Problem**: A server is running out of disk space. You need to find log files older than 90 days and delete them to free up space.
**The Solution**:
```bash
find /var/log -type f -name "*.log" -mtime +90 -exec rm -f {} \;
# OR using the safer -delete flag:
find /var/log -type f -name "*.log" -mtime +90 -delete
```

### Scenario B: Fixing Broken Website Permissions
**The Problem**: A WordPress site is throwing "Forbidden" errors because file and directory permissions are messed up. Directories should be `755` and files should be `644`.
**The Solution**:
```bash
# Set all directories to 755
find /var/www/html/wordpress -type d -exec chmod 755 {} \;

# Set all files to 644
find /var/www/html/wordpress -type f -exec chmod 644 {} \;
```

### Scenario C: Finding Malware or Hacked Files
**The Problem**: A website was compromised, and you suspect malicious PHP files were uploaded in the last 24 hours.
**The Solution**:
```bash
find /var/www/html -type f -name "*.php" -mtime -1
```
*(This lists all `.php` files modified in the last 1 day, allowing you to review them for malicious code).*
