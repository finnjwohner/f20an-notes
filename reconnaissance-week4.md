# Reconnaissance

F20AN Week 4, Finn Wohner's Notes

### Outline

- Information Gathering
    - Google Hacking
    - Email Tracking
    - Host Enumeration
- Social Engineering
    - Human-based
    - Computer-based

## Information Gathering

Information gathering is unearthing initial information, for example;
- Contact Information
- Phone Numbers
- DNS Servers
- Email Servers
- System Architecture
- IDS
- ...

It can be used to create a blueprint of the system / network, and typically uses passive / non-intrusive techniques.

### Google Hacking

Google hacking is finding information available freely from Google.
- Job Positions offered by an organization.
    - Provide useful information about its software/hardware infrastructure.

Google hacking is passive information gathering, the target is not aware of it going on. It could become even more passive if google cached the targeted pages.

Collected information could be sensitive information and/or security vulnerabilities.

#### Basics

- `site:` searches within a site.
- `filetype:` returns only results of the given type.
- `intitle:` searches within the title of a document.
- `inurl:` searches within the URL itself.
- `-` excludes keywords.
- `@twitter`  to search results from twitter.
- `inalltext:` searches text content.

#### Dorking

Google dorking is using carefully crafted queries to reveal sensitive information / vulnerabilities. It could give all sorts of information such as;
- SQL Injection Weaknesses
- Firewall Logs
- Passwords

##### Examples

[Some examples from exploit-db.](https://www.exploit-db.com/google-hacking-database)

- `intitle:"PHP Web Stat - Sysinfo" intext:php inurl:stat/sysinfo.php`
    - Public / exposed sysinfo.php
- `intitle:"Index Of" intext:sftp-config.json`
    - The JSON file contains passwords.

### Web Spiders

Web spiders parse web pages to locate particular information, such as email addresses.

A countermeasure to this is to place a robots.txt file in the root folder which asks robots not to look at certain pages, however this can just be ignored.

### Email Tracking

Email tracking provides information to the email sender about the different actions done by the receiver. Extra information includes the IP address of the receiver. Can be done with tools such as MailTracking or eMailTrackerPro.

### Harvesting Emails

The Harvester, included in Kali Linux gets email, IPs, subdomains, URLs, employees, etc.
- `theHarvester -d hw.ac.uk -| 500 -b google`
    - Queries Google, can query other sources.
- `theHarvester -d hw.ac.uk -| -b google -s`
    - Queries Shodan for found hosts.
    - Requires the configuration of Shodan's API key in `/etc/theHarvester/api-keys.yaml`

https://github.com/laramies/theHarvester

### Futher Public Information

Metagoofil, gets metadata from several types of files, such as doc, pdf, etc.

### Host Enumeration

The Internet Corporation for Assigned Names and Numbers (ICANN) coordinates IPv4 and IPv6 address spaces, and is responsible for the root domain name. It is also responsible for Top-Level domains (TLD) (all 1511 of them).

ICANN is also responsible for DNS policy and assigns address blocks to regional internet registries (RIRs).

#### Whois

TLDs can be queried to get information about a particular domain.
- Contact information of the domain admin.
- Physical address of the organization.
- Authoritative name servers of the domain.

TLDs are queried using the whois tool, originally in UNIX but now available in more operating systems.

##### Whois to query RIR

RIRs maintain important (objects) about address blocks.
- IP Address Blocks.
- Technical Staff.
- Physical Addresses.

Could be found at \<name of RIR\>.net. Or Unix command `whois -h <domain> whois<name of RIR>.net`.

#### Border Gateway Protocol

BGP routes messages between networks. This could be queried to find out useful information about an Autonomous System (AS). An AS is a big network that together with multiple other ASs make up the internet. They are controlled by large organizations such as an enterprise tech company, universities or governments.

#### DNS Enumeration

Explores the DNS records of an organization and extract their information. This can provide information about;
- Employees; e.g., web admin.
- Computer Names; e.g., internal DNS servers.
- IP Addresses of Potential Targets; e.g., email server.

Tools include NSLookup, host and dig.

##### NSLookup

NSLookup provides technical information about a domain
- A (Address): Maps a hostname to an IP address.
- SOA (Start of Authority): Identifies the DNS server responsible for the domain information.
- CNAME (Canonical Name): Provides additional names or aliases for the address record.
- MX (Mail Exchange): Identifies the mail server for the domain.
- SRV (Service): Identifies services such as directory services.
- PTR (Pointer): Maps IP addresses to hostnames.
- NS (Name Server): Identifies other name servers for the domain.

##### Traceroute

Traceroute works by sending ICMP queries with increasing TTL values (3 packets for each TTL).

Tools to do this include VisualRoute, VisualTrace, NeoTrace, VisualLookout.

##### SMTP Probing

not really note worthy stuff on the slides.

## Social Engineering

Users are the weakest link in the security chain. You can convince a user to reveal sensitive information or behave in a way that is against the security policy.

This bypasses all security measures / software / hardware.

It relies on the human nature to;
- Be helpful.
- Trust people.
- Fear punishment.

### Human Based SE Techniques

Some techniques involving human to human interaction include;
- Calling a user and pretending to be a system admin asking for their password.
- Impersonating an existing user by pretending to be an employee, contractor, etc. to gain physical access.
- Pretending to be an important user to intimidate a medium or low user.
- Using a third person i.e., pretend to represent an authorized entity to access the system.

You may also call technical assistance and help desk, who are always looking to help. Shoulder surfing is also an option, i.e., the use of cameras, as well as dumpster diving "trashing" to find written pieces of information.

Hanging around the ToE buildings to spy on work conversations, or target cigarette smokers, or follow employees into buildings ("piggy-backing").

Reverse social engineering works, such as creating a fake person such as a made up system admin that users will contact later for help.

Identity theft is using stolen information (e.g., from dumpster diving) to create fake ID badges.

### Computer Based SE Techniques

- Phishing attacks: Send an email to victim pretending to be bank, admin, website etc., and asking for confidential information to do some action, e.g., avoid deletion of account.
- Online Scams: Mail attachments can include malware.
- URL Obfuscation: Hide the true URL in the address of a browser to make the user think it is a different trustworthy one.
    - 172.217.23.196 is google's IP address, in hex it is AC.D9.17.C4, in decimal that gives 2899908548. http://2899908548 takes you to Google.com.

### Countermeasures

- Security Policies.
- Getting employees involved in applying the policies through training, talks, etc.
- Clear policies on who can access what and under what circumstances.
- Destruction of paper documents.
- Management teams must be involved in the security policies.