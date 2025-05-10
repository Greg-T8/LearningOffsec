# Notes from "The Ultimate Kali Linux Book" by Glen D. Singh

<img src="images/1746092403966.png" alt="The Ultimate Kali Linux Book" width="350"/>

<!-- omit in toc -->
## Contents

- [Chapter 4. Passive Reconnaissance](#chapter-4-passive-reconnaissance)
  - [Exploring Passive Reconnaissance](#exploring-passive-reconnaissance)
  - [Creating a Sock Puppet](#creating-a-sock-puppet)
  - [Anonymizing Internet-Based Traffic](#anonymizing-internet-based-traffic)
    - [Proxychains](#proxychains)


## Chapter 4. Passive Reconnaissance

Types of information that may be exploited:
- System information
  - Live hosts on a network
  - Hostnames of devices
  - Operating system type and version
  - Running services
  - Open service ports
  - Unauthenticated network shares
  - Usernames and passwords
- Network information
  - DNS records
  - Domain names
  - Sub-domain names
  - Firewall rules
  - IP addresses and network blocks
  - Network protocols and services
- Organizational information
  - Employees' details and contact information
  - Geo-location of the organization and its remote offices
  - Employees' roles and profiles

Both Lockeheed Martin's Cyber Kill Chain and the MITRE ATT&CK framework list reconnaissance as the first step in the attack lifecycle. 

Reconnaissance is broken down into two types:
- Passive reconnaissance: 
  - Information gathering without direct interaction with the target
  - Examples: WHOIS lookups, DNS queries, social media searches
- Active reconnaissance
  - Information gathering with direct interaction with the target
  - Examples: Port scanning, vulnerability scanning, network mapping

Reconnaissance includes a process called **footprinting**, which involves obtaining specific information about a target system or network. 

### Exploring Passive Reconnaissance

- Internet Archive (Wayback Machine): https://archive.org/web/
  - Allows you to view archived versions of web pages
  - Useful for finding old content or changes to a website

- WHOIS Lookup: https://whois.domaintools.com/
  - Provides information about domain ownership and registration details

<img src="images/1746522635065.png" alt="WHOIS Lookup" width="750"/>


### Creating a Sock Puppet

A sock puppet is a fake online identity used to gather information or interact with others without revealing your true identity.

The following reosurces can be used to create sock puppets:
- Fake Name Generator: https://www.fakenamegenerator.com/
  - Generates random names, addresses, and other personal information
- This Person Does Not Exist: https://thispersondoesnotexist.com/
  - Generates random images of people using AI
- Proxy credit card: https://privacy.com/
  - Provides virtual credit card numbers for online transactions

### Anonymizing Internet-Based Traffic

When using a VPN, ensure your DNS traffic is not leaking outside the VPN tunnel. Use tools like DNS Leak Test  to check for leaks:

- https://www.dnsleaktest.com/

#### Proxychains

Penetration testers use proxychains to route their traffic through multiple proxies, making it difficult to trace back to the original source. Proxychains allow a penetration tester to configure various types of proxies:
- HTTP
- HTTPS
- SOCKS4
- SOCKS5

<img src="images/1746522974463.png" alt="Proxychains" width="550"/>

**Finding Free Proxy Servers**: The following website provides a list of free proxy servers:

- https://spys.one/en/

This site uses the following proxy protocols:

| Feature                    | SOCKS5 Proxy                                                  | HTTP Proxy                                                        | HTTPS Proxy                                                                                       |
| :------------------------- | :------------------------------------------------------------ | :---------------------------------------------------------------- | :------------------------------------------------------------------------------------------------ |
| **Purpose**                | General-purpose proxy; works with any traffic type (TCP, UDP) | Web traffic only (HTTP)                                           | Secure web traffic (HTTPS)                                                                        |
| **Protocol Awareness**     | Not aware of application protocols like HTTP/HTTPS            | Understands HTTP (can interpret, modify, or filter HTTP requests) | Understands HTTPS (can recognize encrypted traffic but typically can't see inside)                |
| **Encryption**             | No built-in encryption (just relays data)                     | No encryption                                                     | Depends: encrypts *after* the proxy (client ↔ proxy is usually plain unless using CONNECT method) |
| **Speed**                  | Very fast (minimal processing)                                | Fast, with potential overhead                                     | Slightly slower due to encryption negotiations                                                    |
| **Typical Use Cases**      | Torrenting, gaming, SSH relays, general non-web traffic       | Web browsing without SSL (old websites, simple proxying)          | Secure browsing, accessing HTTPS websites through a proxy                                         |
| **Authentication Support** | Yes (username/password optional)                              | Sometimes                                                         | Sometimes                                                                                         |

**In short:**
- **SOCKS5** is a **raw tunnel**:
  It doesn't care if you're browsing the web, transferring a file, or gaming. It just blindly forwards whatever you send through it. Think of it like a dumb messenger that carries sealed envelopes without peeking inside.
- **HTTP** is specifically for **websites that use regular HTTP (port 80)**:
  The proxy *understands* web traffic, meaning it can modify, cache, or log your web requests. But it's *not secure* because it's not encrypted.
- **HTTPS** proxies are designed for **secure websites (port 443)**:
  They forward encrypted traffic to HTTPS sites. They usually use a "CONNECT" method to establish a tunnel. Your browser does encryption, not the proxy itself, so the proxy doesn't see inside the encrypted communication (unless it's doing "SSL interception," but that's more advanced and rare for public proxies).

To get started with proxychains, (assuming you have already installed it), update the configuration file located at `/etc/proxychains4.conf` to use `dynamic_chain`.

<img src="images/1746523341324.png" alt="Proxychains Configuration" width="450"/>

Then scroll down to the section labeled `[ProxyList]`. Comment out the `socks4` line and insert each proxy server you want to use in the following format:  

```bash
[ProxyList]
socks5  23.81.207.111   5256
socks5  98.175.31.195   4145
socks5  67.201.39.14    4145
socks5  104.168.13.108  1080
```
Before using `proxychains`, retrieve your real public IP address:
```bash
curl ifconfig.co
```

Then run a command through `proxychains` to see if your IP address changes:
```bash
proxychains4 -f /etc/proxychains4.conf curl ifconfig.co
```

You can launch other applications through `proxychains` as well:
```bash
proxychains4 -f /etc/proxychains4.conf firefox
```
<img src="images/1746876109281.png" alt="Proxychains Firefox" width="750"/>

**Note:** The public IP address reflects the last IP address used in the proxy list.
