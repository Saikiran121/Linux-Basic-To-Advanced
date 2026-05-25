# Linux Services: Understanding Background Processes

In Linux, a **service** (also known as a **daemon**) is a process that runs in the background, independent of any user session. Services providing core functionality like networking, printing, or web hosting.

---

## 1. What is a Service?
While a regular process (like `vim` or `ls`) is started by a user and usually ends when the user task is done, a **service**:
*   Starts automatically when the system boots.
*   Runs in the background without a terminal (TTY).
*   Wait for events to occur (like an incoming web request).

**Common Examples**:
*   `sshd`: Allows remote login via SSH.
*   `nginx` / `apache2`: Serves web pages.
*   `systemd-timesyncd`: Keeps your system clock in sync.
*   `docker`: Manages containers.

---

## 2. Managing Services with `systemd`
Most modern Linux distributions (Ubuntu, CentOS, Debian, Fedora) use **systemd** as the init system to manage services. The primary tool used is `systemctl`.

### The Basic Commands:
| Action | Command | Description |
| :--- | :--- | :--- |
| **Start** | `sudo systemctl start <service>` | Start the service immediately. |
| **Stop** | `sudo systemctl stop <service>` | Stop a running service. |
| **Restart** | `sudo systemctl restart <service>` | Stop and then start the service. |
| **Reload** | `sudo systemctl reload <service>` | Apply config changes without stopping the service. |
| **Status** | `systemctl status <service>` | Check if the service is running, errored, or dead. |

---

## 3. Persistent Behavior: Enable vs. Start
A common point of confusion for beginners is the difference between starting a service and enabling it.

*   **`systemctl start`**: Affects the **current** session. If you reboot, the service won't start automatically.
*   **`systemctl enable`**: Affects **future** boots. It creates a symbolic link so the system knows to start the service during the boot process.

**Summary Table**:
| Command | Running Now? | Running after Reboot? |
| :--- | :---: | :---: |
| `start` | Yes | No |
| `enable` | No (usually) | Yes |
| `enable --now` | Yes | Yes |

---

## 4. Checking All Services
To see a list of all active services and their status:
```bash
systemctl list-units --type=service
```
To see which services are set to start on boot:
```bash
systemctl list-unit-files --type=service
```

---

## 5. Summary Scenario: Deploying a Web Server
If you just installed Nginx and want it to run immediately and also start every time the computer turns on:
1.  **Check status**: `systemctl status nginx` (likely inactive).
2.  **Start it**: `sudo systemctl start nginx`.
3.  **Ensure it stays on**: `sudo systemctl enable nginx`.
4.  **Confirm**: `systemctl status nginx` (should show `active (running)` and `enabled`).
