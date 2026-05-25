# The `ip` Command in Linux

The `ip` command is a powerful and versatile networking tool in Linux used for routing, assigning IP addresses, and managing network interfaces. It is a part of the `iproute2` package and serves as the modern replacement for older, deprecated networking commands like `ifconfig`, `route`, and `netstat`.

## Basic Syntax

The general syntax of the `ip` command is:

```bash
ip [OPTIONS] OBJECT [COMMAND]
```

*   **OBJECT:** Specifies what you want to manage. The most common objects are `addr` (addresses), `link` (interfaces), and `route` (routing tables).
*   **COMMAND:** The action to perform on the object (e.g., `show`, `add`, `del`, `up`, `down`). If left blank, `show` is usually the default.

You can often abbreviate objects and commands (e.g., `ip a` instead of `ip addr show`).

---

## 1. Managing IP Addresses (`ip addr` or `ip a`)

The `addr` object is used to view and modify IP addresses associated with your network interfaces.

*   **Show all IP addresses:**
    ```bash
    ip addr show
    # OR simply:
    ip a
    ```
    This displays detailed information about all interfaces, including their MAC addresses, state (UP/DOWN), and IPv4/IPv6 addresses.

*   **Show IPs for a specific interface:**
    ```bash
    ip addr show dev wlo1
    # Note: 'wlo1' is typically a wireless interface name in modern Linux. 
    # 'dev' specifies the device, though it is often optional (e.g., `ip addr show wlo1`).
    ```

*   **Add an IP address to an interface:**
    Syntax: `sudo ip addr add [IP_ADDRESS]/[SUBNET_MASK] dev [INTERFACE_NAME]`
    ```bash
    sudo ip addr add 192.168.1.50/24 dev wlo1
    ```
    *(Note: This change is temporary and will be lost after a system reboot. To make it permanent, you usually need to edit your network manager configuration files, such as Netplan or NetworkManager.)*

*   **Remove an IP address:**
    ```bash
    sudo ip addr del 192.168.1.50/24 dev eth0
    ```

---

## 2. Managing Network Interfaces (`ip link` or `ip l`)

The `link` object operates at the physical/data-link layer. It is used to view interface states and bring them up or down.

*   **Show all network interfaces:**
    ```bash
    ip link show
    # OR simply:
    ip l
    ```

*   **Bring an interface UP (enable it):**
    ```bash
    sudo ip link set eth0 up
    ```

*   **Bring an interface DOWN (disable it):**
    ```bash
    sudo ip link set eth0 down
    ```

---

## 3. Managing Routing Tables (`ip route` or `ip r`)

The `route` object is used to view and manipulate the kernel's IP routing tables, which dictate how traffic leaves your machine.

*   **View the routing table:**
    ```bash
    ip route show
    # OR simply:
    ip r
    ```
    This shows the default gateway and routes to specific subnets.

*   **Add a static route:**
    ```bash
    sudo ip route add 10.0.0.0/8 via 192.168.1.1
    ```
    *(Traffic destined for the 10.x.x.x network will be sent through the 192.168.1.1 gateway.)*

*   **Change the default gateway:**
    ```bash
    sudo ip route add default via 192.168.1.254
    ```

---

## Useful Options and Formatting

The `ip` command provides flags to make the output much easier to read:

*   **`-c` (Color):** Colorizes the output, making IP addresses, interface names, and states (like UP/DOWN) stand out clearly.
    ```bash
    ip -c a
    ```

*   **`-br` (Brief):** Prints a highly condensed, tabular overview. This is fantastic for a quick glance at your interfaces.
    ```bash
    ip -br a
    ```
    **Example Output:**
    ```text
    lo               UNKNOWN        127.0.0.1/8 ::1/128 
    eth0             UP             192.168.1.10/24 fe80::1a2b:3c4d:5e6f:7g8h/64
    ```

*   **Combine them (`-c -br`):**
    ```bash
    ip -c -br a
    ```
    *(Provides a beautiful, colorized, table-format summary of all your IP addresses!)*
