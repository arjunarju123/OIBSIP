
# Task 8 — Capture Network Traffic with Wireshark

## Objective
Capture live network traffic using Wireshark, apply filters to isolate specific protocols, analyse packet contents, and document findings with security observations.

## Tech Stack / Tools
Wireshark, local network interface (Kali Linux VM capturing its own traffic on a test network)

## Environment
- **Capture machine:** Kali Linux (VirtualBox)
- **Target for traffic generation:** Metasploitable2 (10.50.236.64) — DVWA login page
- Both VMs on the same VirtualBox NAT Network / Host-only Adapter

## Installation & Permissions
Wireshark comes pre-installed on Kali Linux. To capture packets without running as root every time, the current user must be added to the `wireshark` group:
```bash
sudo usermod -aG wireshark $USER
# log out and back in for the group change to take effect
```
Without this, Wireshark either needs to be run with `sudo` or will be unable to access the network interface in promiscuous mode.

## Capture Process
1. Opened Wireshark and selected the active network interface
2. Started the capture and generated traffic by browsing `http://10.50.236.64/` and logging into DVWA at `http://10.50.236.64/dvwa/login.php` for 2+ minutes
3. Stopped the capture once sufficient traffic was recorded
4. Exported the full capture: File → Export Specified Packets → `wireshark_capture.pcap`

## Display Filter: HTTP Traffic
Filter: `http`

*Screenshot: `screenshots/http_filter.png`*

## Display Filter: DNS Traffic
Filter: `dns`

*Screenshot: `screenshots/dns_filter.png`*

## Display Filter: TCP Traffic — 3-Way Handshake Analysis
Filter: `tcp.port == 47460 && ip.addr == 10.50.236.64`

| Step | Frame | Flags | Description |
|------|-------|-------|-------------|
| 1 | 132 | SYN | Client (10.50.236.30) requests connection to server (10.50.236.64:80) |
| 2 | 135 | SYN, ACK | Server acknowledges and sends its own SYN |
| 3 | 136 | ACK | Client acknowledges — connection established |

*Screenshot: `screenshots/tcp_handshake.png`*

## Unencrypted Data Identified
Frame **494** contains an HTTP **POST** request to `/dvwa/login.php` on `10.50.236.64`. Because DVWA runs over plain HTTP (no TLS), the login form data is fully visible in the packet payload:

username=abcd
password=1234
Login=Login

*Screenshot: `screenshots/http_login_credentials.png`*

This confirms that anyone intercepting traffic on the same network segment — e.g., a shared Wi-Fi network or a compromised switch — could read these credentials directly, with no need to break any encryption.

## Why Unencrypted HTTP Traffic Is Dangerous
HTTP sends all data — including login credentials, session cookies, and form submissions — in plain text. Anyone with access to the network path (a shared router, a compromised switch, or a malicious actor on public Wi-Fi) can capture and read this data using a tool as simple as Wireshark, exactly as demonstrated above.

## How HTTPS Prevents This
HTTPS wraps HTTP traffic in TLS (Transport Layer Security) encryption. Even if an attacker captures the packets, the payload is encrypted and unreadable without the private decryption key, which only the legitimate server holds. This protects credentials and sensitive data in transit, even over untrusted networks.

## Glossary
- **Packet:** A single unit of data transmitted across a network, containing both control information (headers) and the actual data (payload).
- **Protocol:** An agreed-upon set of rules that defines how devices communicate (e.g., HTTP, DNS, TCP).
- **Port:** A numbered endpoint on a device that identifies which service or application traffic is intended for (e.g., port 80 for HTTP).
- **Payload:** The actual data being carried inside a packet, separate from the header/control information.
- **Handshake:** The initial exchange of packets (SYN, SYN-ACK, ACK in TCP) used to establish a reliable connection between two devices before data transfer begins.

## Ethics Note
This capture was performed only on traffic between machines I own and administer (Kali Linux and Metasploitable2 in a local VirtualBox lab). No traffic was captured on public Wi-Fi, university networks, or any network without explicit authorisation.

## Files in this folder
- `wireshark_capture.pcap` — exported packet capture
- `screenshots/` — HTTP filter, DNS filter, annotated TCP handshake, and plaintext login credential screenshots
