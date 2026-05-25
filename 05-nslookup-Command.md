# The `nslookup` Command in Linux

The `nslookup` (Name Server Lookup) command is a network administration tool used for querying the Domain Name System (DNS) to obtain domain name or IP address mapping, as well as other specific DNS records. It is an essential tool for troubleshooting DNS-related issues.

## Two Modes of Operation

`nslookup` can be run in two different modes:
1.  **Non-Interactive Mode:** Used when you just need to query a single piece of data quickly. You run the command with arguments, and it returns the result immediately.
2.  **Interactive Mode:** Used when you need to run multiple queries consecutively. You launch it by just typing `nslookup`, which drops you into a special prompt (`>`) where you can type various domains or configuration commands.

---

## Non-Interactive Mode (Quick Queries)

### 1. Basic DNS Lookup (Forward Lookup)
The most common use case is finding the IP address associated with a domain name.

```bash
nslookup google.com
```
**Example Output:**
```text
Server:		127.0.0.53
Address:	127.0.0.53#53

Non-authoritative answer:
Name:	google.com
Address: 142.250.190.46
Name:	google.com
Address: 2607:f8b0:4009:815::200e
```
*Note: The `Server` at the top is the local DNS resolver your machine is using to make the query. The `Non-authoritative answer` means the answer came from a cache, not directly from the domain's primary name server.*

### 2. Reverse DNS Lookup
You can also do the opposite: provide an IP address to find out what domain name is associated with it.

```bash
nslookup 142.250.190.46
```

### 3. Querying Specific DNS Record Types
By default, `nslookup` queries for `A` (IPv4) and `AAAA` (IPv6) records. You can use the `-type=` (or `-query=`) flag to look up other specific DNS records.

*   **Find Mail Servers (MX Records):**
    ```bash
    nslookup -type=mx google.com
    ```
    *(Useful for troubleshooting email delivery issues).*

*   **Find Name Servers (NS Records):**
    ```bash
    nslookup -type=ns google.com
    ```
    *(Useful for finding out which servers are currently managing the domain's DNS).*

*   **Find Text Records (TXT Records):**
    ```bash
    nslookup -type=txt google.com
    ```
    *(Commonly used to verify domain ownership or check SPF/DKIM records for email security).*

### 4. Specifying a Custom DNS Server
By default, `nslookup` uses your system's configured DNS server (often provided by your ISP or router). You can force it to query a specific DNS server (like Google's `8.8.8.8` or Cloudflare's `1.1.1.1`) to see if they are returning different results, which is a key troubleshooting step.

Syntax: `nslookup [DOMAIN] [DNS_SERVER]`

```bash
nslookup example.com 8.8.8.8
```

### 5. Measuring DNS Resolution Time
You can prepend the standard Linux `time` command to `nslookup` to see exactly how long a DNS query takes to complete. This is extremely useful for diagnosing slow network performance or comparing the speed of different DNS servers.

```bash
time nslookup saikbiradar.in
```
**Example Output:**
```text
Server:		127.0.0.53
Address:	127.0.0.53#53

Non-authoritative answer:
Name:	saikbiradar.in
Address: 104.21.84.148

real	0m0.035s
user	0m0.004s
sys	0m0.005s
```
*Note: The `real` time output at the bottom indicates the total wall-clock time it took to send the query and receive the response from the DNS server.*

---

## Interactive Mode

To enter interactive mode, simply type the command by itself:
```bash
nslookup
```
You will see a `>` prompt. From here, you can change settings before querying:

```text
> set type=mx        # Set the query type to Mail Exchange (MX)
> google.com         # Query google.com with the new type
> server 1.1.1.1     # Change the DNS server to Cloudflare
> example.com        # Query example.com using Cloudflare's server
> exit               # Leave interactive mode
```

## `nslookup` vs `dig`
While `nslookup` is widely used and available on almost all operating systems (including Windows), many Linux network administrators prefer the `dig` command. `dig` is considered more modern, provides more detailed and cleaner output, and behaves more consistently. However, `nslookup` remains a universally understood standard for quick, straightforward DNS troubleshooting.
