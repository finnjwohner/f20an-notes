# Ethical Hacking

Week 4, Finn Wohner's Notes

---

A hacker is someone who can hijack a computer process and make it do something for them. Skills involved include:
- Programming
- Networking
- OS

Ethical hackers can form a tiger team, in which each member is specialized in one area.

Ethical hackers are not necessarily very knowledgeable about countermeasures.

## Penetration Testing

The objective of pen-testing is to identify security risks and vulnerabilities.

This allows putting countermeasures that will neutralize or confine the attacks into place.

A pen-test report is the results of various tests with logs file, screens shots, etc.

## Types of Hacking

A hacker is not necessarily a bad person, a cracker is!

There are three types of hackers.
- Black Hat: Create and propagate malware (viruses, worms, trojans etc.,).
- White Hat: Use their skills to protect systems.
- Gray Hats.
- Hacktivists.

## Terminology

- Threat: A situation, context, entity that possibly led to a security breach. (A cracker is a threat).
- Vulnerability: A flaw in the design, implementation or use of a system.
- Exploit: Software that takes advantage of a vulnerability.
    - Local: Requires physical access to the target system.
    - Remote: Exploits come from an external host.
- Attack: Makes use vulnerabilities via exploits.
- Target of Evaluation (ToE): Is the computer system, network, or software which is the target of security attacks.

## Sources of Vulnerability

- Operating System: Default settings, weak design.
- Applications: Forgotten backdoors, security not in mind when designed.
- "Shrink-wrap" codes: Features that the average user is not aware of.
- Misconfiguration: Could be intentional to ease the use of the system, e.g., firewall disabled.

## Entry Points

- Remote Network: Check the network from outside, i.e., router, proxy, firewall vulnerabilities.
- LAN: Become easier thanks to WLAN.
- Stolen Equipment: Gives information on security settings and encryption algorithms used. Can be used as an entry point to the network. "Need to know" policies could limit losses.
- Social Engineering: Examine the human factor.
- Physical Entry: The hacker can steal physical documents, plant software/hardware.
- ...

## Knowledge LEvel

There are three levels of prior knowledge/
- Black Box: A hacker from outside who does not know anything about the ToE. (Most Expensive)
- White Box: The attacker is ideally provided with full information about the system (OS, applications, network map, etc.).
- Grey Box: The hacker has the same access as an employee (Can be particularly useful).

## How to Be Ethical?

Agree with the client on the ToE, type of hack (knowledge level), type of attack (DoS, buffer overflow, SQL injection, etc.). You don't want to be blamed for crashing their server during a DoS.

Have written authorization and sign an NDA. Optionally organize a tiger team.

Schedule for testing, perform tests and analyze results, write and present a pen-test report.

## Five Phases of Ethical Hacking

An EC-Council Certified Ethical Hacker uses:
1. Reconnaissance.
2. Scanning.
3. Gaining Access.
4. Maintaining Access.
5. Covering Tracks.

### Reconnaissance

This involves acquiring basic information about the ToE.
- Sniffing network traffic.
- Watching a building to check when the security administrator leaves work.
- Get information about OS and Web Server for example.

Passive reconnaissance is undetected by the victim.
- Google search/hacking.
- Sniffing the net: get host name conventions.
- Some protocols exchange passwords as clear text (e.g., FTP, POP3).

Aggressive reconnaissance (Rattling the door handle).
- Discovering security measures.
- E.g., try to get inside a building, check if a host has port 80 open.

### Scanning

In depth examination of the ToE. Tools include;
- Ping Sweepers.
- Port Scanners.
- Vulnerability Scanners.

Once finished, the hacker has all the information they need. Information from phases 1 and 2 are provided to the hacker if performing a white box hack. Phases 1 and 2 could take up to 90% of the time of a black box pen-test.

### Gaining Access

Use vulnerabilities to gain access "own" the system.

Use exploit DBs;
- Exploit DB.
- Rapid7.
- Vulnerability Lab.
- ...

Tools and techniques include:
- Malware: viruses, trojans, etc.,
- Stack buffer overflow.
- SQL injection.
- Session hijacking.

### Maintaining Access

Keep access to the machine.
- Zombie, member of a botnet.

Install malware.
- Rootkit.
- Backdoor.

Not an objective of this course.

### Covering Tracks

To avoid detection (e.g., by a security audit).
- Alter log files.
- Remove IDS alarms.
- Tunnelling and stenography techniques could be used by the hacker to covertly communicate with the victim machine.

Not an objective of this course.