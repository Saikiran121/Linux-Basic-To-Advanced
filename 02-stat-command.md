# The `stat` Command in Linux

The `stat` command in Linux is a command-line utility used to display detailed information (status) about a file or a file system. While the `ls -l` command provides basic information like permissions, owner, and size, `stat` provides a much deeper look into a file's metadata.

## What Does `stat` Display?

When you run `stat` on a file, it outputs several key pieces of metadata:

*   **File:** The name of the file.
*   **Size:** The size of the file in bytes.
*   **Blocks:** The number of allocated blocks the file uses on the disk.
*   **IO Block:** The size of every block allocated for this file.
*   **File type:** Indicates whether it's a regular file, directory, symbolic link, socket, etc.
*   **Device:** The device number in hex and decimal.
*   **Inode:** The Inode number (the unique identifier for the file on the filesystem).
*   **Links:** The number of hard links pointing to the file.
*   **Access (Permissions):** The file permissions in both octal (e.g., `0644`) and symbolic (e.g., `-rw-r--r--`) formats, along with the User ID (UID) and Group ID (GID) of the owner.
*   **Timestamps:**
    *   **Access (atime):** The last time the file was read or accessed.
    *   **Modify (mtime):** The last time the file's contents were modified.
    *   **Change (ctime):** The last time the file's metadata (e.g., permissions, ownership) or contents were changed.
    *   **Birth (btime/crtime):** The time the file was created (not supported by all filesystems/OS combinations; may show as `-` if not supported).

## Basic Usage

The most common way to use `stat` is simply passing a filename as an argument:

```bash
stat filename.txt
```

**Example Output:**
```text
  File: filename.txt
  Size: 1024      	Blocks: 8          IO Block: 4096   regular file
Device: 801h/2049d	Inode: 1234567     Links: 1
Access: (0644/-rw-r--r--)  Uid: ( 1000/   user)   Gid: ( 1000/   user)
Access: 2023-10-27 10:00:00.000000000 +0000
Modify: 2023-10-27 09:30:00.000000000 +0000
Change: 2023-10-27 09:30:00.000000000 +0000
 Birth: -
```

## Useful Options and Flags

*   **`-f` or `--file-system`**: Instead of showing information about the file itself, it displays information about the file system where the file or directory resides. This is extremely useful for checking disk space and file system details.
    ```bash
    stat -f /home
    ```
    **Example Output:**
    ```text
      File: "/home"
        ID: 1a2b3c4d5e6f7g8h Namelen: 255     Type: ext2/ext3
    Block size: 4096       Fundamental block size: 4096
    Blocks: Total: 12345678   Free: 9876543    Available: 9123456
    Inodes: Total: 3145728    Free: 3000000
    ```
    *What the `-f` fields mean:*
    *   **ID:** The file system ID in hexadecimal.
    *   **Namelen:** The maximum length of filenames supported by the file system.
    *   **Type:** The type of file system (e.g., ext2/ext3/ext4, xfs, tmpfs).
    *   **Block size:** The optimal block size for transfers.
    *   **Fundamental block size:** The actual block size on disk.
    *   **Blocks:** 
        *   **Total:** Total data blocks in the file system.
        *   **Free:** Free blocks available in the file system.
        *   **Available:** Free blocks available to non-root users.
    *   **Inodes:** 
        *   **Total:** Total file entries (inodes) in the file system.
        *   **Free:** Free inodes available for new files.

*   **`-c` or `--format`**: Allows you to customize the output using specific format sequences. This is extremely useful for scripting when you only need a specific piece of information.
    ```bash
    # Print only the file size (%s) and the file name (%n)
    stat -c "%s %n" filename.txt
    
    # Print only the octal permissions (%a)
    stat -c "%a" filename.txt
    
    # Print the Inode number (%i)
    stat -c "%i" filename.txt
    ```

    **Example: Checking exact modification time with custom text**
    ```bash
    stat -c "Modified: %y" commands.txt
    ```
    **Example Output:**
    ```text
    Modified: 2026-05-25 22:12:03.457294612 +0530
    ```
    *How this command works:*
    *   `stat -c "Modified: %y" commands.txt`: Evaluates the file `commands.txt`. Notice that you can seamlessly mix regular text (`Modified: `) with the format sequence (`%y`). It outputs your custom string followed by the human-readable time of last data modification.
    
    *Real-World Scenario (Organization/Debugging):*
    *   **Verifying Configurations:** When troubleshooting a service outage, an engineer can use this command on a specific configuration file (like `nginx.conf` or `commands.txt`) to quickly verify exactly when it was last modified, without the clutter of other `stat` or `ls -l` output. This helps confirm if a recent config change caused the issue.
    
    **Advanced Example 1: Sorting files by modification time**
    ```bash
    stat -c "%y %n" *.txt | sort -n
    ```
    *How this command works:*
    *   `stat -c "%y %n" *.txt`: The `stat` command evaluates all `.txt` files in the current directory. The format string `"%y %n"` tells it to print the human-readable time of last data modification (`%y`, like `2023-10-27 10:00:00`) followed by the file name (`%n`) for each file.
    *   `|` (Pipe): Takes the output from the `stat` command and feeds it as standard input into the `sort` command.
    *   `sort -n`: Sorts the incoming lines numerically. Since the `%y` format starts with the year (e.g., `2023-10-27`), it effectively sorts the files chronologically from oldest to newest based on their modification date.
    
    *Real-World Scenario (Organization/Debugging):*
    *   **Log Rotation & Cleanup:** In a production environment, servers generate massive amounts of log files. A system administrator can use this to quickly identify the oldest log files that need to be archived or deleted to free up disk space.
    *   **Debugging Build Processes:** If a continuous integration (CI) pipeline fails, developers can sort the generated artifacts or debug logs by time to trace exactly which file was modified right before the failure occurred.

    **Advanced Example 2: Finding files modified today**
    ```bash
    stat -c "%y %n" *.txt | grep $(date +%Y-%m-%d)
    ```
    *How this command works:*
    *   `stat -c "%y %n" *.txt`: Retrieves the human-readable modification time and file name for all `.txt` files.
    *   `$(date +%Y-%m-%d)`: This is command substitution. It runs the `date` command and formats the output to just the current year, month, and day (e.g., `2023-10-27`).
    *   `grep ...`: The `grep` command searches the pipeline output from `stat` for any lines containing today's date string. This effectively filters the list to show only the `.txt` files modified today.

    *Real-World Scenario (Organization/Debugging):*
    *   **Security Incident Response:** If a security breach or unauthorized access is suspected on a server, incident responders can use this command to find all files (like scripts, configuration files, or binaries) that were maliciously modified today.
    *   **Daily Auditing:** An automated compliance script can run this daily to generate a report of all sensitive files changed by employees over the last 24 hours, ensuring proper auditing and change tracking.

*   **`-L` or `--dereference`**: If you run `stat` on a symbolic link, it shows info about the link itself. Using `-L` tells `stat` to follow the link and show information about the target file it points to.
    ```bash
    stat -L /path/to/symlink
    ```

*   **`--terse`**: Outputs the information in a concise, machine-readable format on a single line, making it easier to parse in scripts.
    ```bash
    stat --terse filename.txt
    ```

## `stat` vs `ls -l`
While `ls -l` is great for a quick glance at a directory's contents and basic permissions, `stat` is the go-to tool when you need precise timestamps, inode numbers, or need to extract specific metadata programmatically using custom formatting.
