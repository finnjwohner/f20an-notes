# Application Layer Security

F20AN Week 5, Finn Wohner's Notes

### Outline

- Overview of the application layer
- DNS structure
- DNS queries
- DNS attacks & countermeasures
- DNSSEC

---

Some examples of application-layer protocols include;
- HTTP: Hypertext Transfer Protocol.
- FTP: File Transfer Protocol.
- SMTP: Simple Mail Transfer Protocol.
- Telnet: Terminal Evaluation Protocol, used for logging on remotely to network hosts.

Some application-layer protocols that are used to manage TCP/IP networks are;
- DNS: Domain Name System.
- RIP: Routing Information Protocol, used to exchange routing information between routers.
- SNMP: Simple Network Management Protocol, used to collect and exchange network management information between a console and network devices.

## The Domain Name System

DNS is an application-layer protocol for mapping domain names to IP addresses. DNS provides a distribute database over the internet that stores various resource records, including:
- Address (A) Record: IP addresses associated with a host name.
- Mail Exchange (MX) Record: Mail server of a domain.
- Name Server (NS) Record: Authoritative server for a domain.

### Name Servers

Domain names are two or more labels separated by dots (e.g., cs166.net). The rightmost label is the top-level domain.

### Authoritative Name Servers

Authoritative name servers contain all details about a specific domain, including its subdomains (A records) or references to other name servers (NS records).

The authoritative name server hierarchy matches the domain hierarchy: root servers point to DNS servers for TLDs, etc.

Root servers, and servers for TLDs change infrequently.

DNS servers refer to other DNS servers by name, not by IP.
    - Sometimes must bootstrap by providing an IP along with a name, called a glue record.

Authoritative name servers can designate other authoritative name servers for subdomains.

#### Primary and Secondary

ANs can be primary or secondary, a primary server contains original zone file whereas secondary servers are replicas, automatically updating.

This makes DNS fault tolerant, automatically distributing the load.

### Namespace Management

ICANN has the overall responsibility for managing DNS, they control the root domain, delegating control over each top level domain to a domain name registry.

ICANN is the governing body for all generic top-level domains (gTLD).
    - Generic TLDs are a category of all TLDs, consisting of .com, .net, .org, etc.,

Every country has its own TLD (cTLD) controlled by the government.

Until 1999 all .com, .net and .org registries were handled by Network Solutions Incorporated, after Nov 1999 ICANN and NSI had to allow for a shared registration system and there are currently over 500 registrars in the market.

Also since 1999, ICANN has created additional gTLDs including some which are sponsored by consortiums or groups of companies.

### DNS Zones

A zone is a contiguous, non overlapping set of domains that are managed by one authority. Every zone has one or more authoritative name servers.

### DNS Root Servers

There are 13 root DNS servers. They are the starting point of a DNS query and thus a critical infrastructure. They are replicated and globally distributed using a root zone file.

### Local DNS Files

`/etc/host`

Stores IP addresses for some hostnames. This file is consulted before the local DNS server is queried.

`etc/resolv.conf`

Stores the IP address of the local DNS server.

### DNS Response

There are 4 types of sections in a DNS response.
- Question Section: Describes a question to a nameserver.
- Answer Section: Records that answer the question.
- Authority Section: Records that point toward authoritative nameservers.
- Additional Section: Records that are related to the query.

### DNS Caching

There would be too much network traffic if a path in the DNS tree would be traversed for each query.
    - Root zone would be rapidly overloaded.

DNS Servers cache results for a specified amount of time.
    - Specified by ANS reply's time-to-live field.

Operating systems and browsers also maintain resolvers and DNS caches.
    - Windows: `ipconfig/displaydns`.

DNS queries are typically issued over UDP on port 53.
    - A 16 bit request identifier in payload.

### DNS Pharming Attack

Maps a domain name to a malicious server, which then performs a fishing attack by delivering a web page that looks the same as the real one.

### DNS Attacks on Compromised Machines

If attackers have root privileges on a machine they can;
- Modify /etc/resolv.conf, use a malicious DNS server as the machine's local DNS server.
- Modify /etc/hosts, add new records to the file providing the IP address for some selected domains, for example modify www.bank.com to lead to the attacker's machine.

### DNS Cache Poisoning

Basic idea is to give DNS servers false records and get it cached. DNS uses a 16-bit request identifier to pair queries with answers. Cache may be poisoned when a name server;
- Disregards identifiers.
- Has predictable IDs.
- Accepts unsolicited DNS records.

#### Countermeasures

- Use random identifiers for queries (2^16, 65556 combinations).
- Use random ports for DNS requests (2^16, 65556 combinations).
- Always check identifiers.
- Deploy DNSSEC.

### DNS Security (DNSSEC)

Guarantees;
- Authenticity of DNS answer origin.
- Integrity of reply.
- Authenticity of DoS.

DNSSEC accomplishes this by signing DNS replies at each step of the way using public-key cryptography to sign responses. Typically use trust anchors, entries in the OS to bootstrap the process.