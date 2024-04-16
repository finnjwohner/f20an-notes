# Link Layer Security

F20AN Week 2, Finn Wohner's Notes.

### Outline

- Overview of the [link layer](#the-link-layer) and [Ethernet](#ethernet)
- [ARP Protocol](#arp)
- [ARP Spoofing](#arp-spoofing)
- [ARP Spoofing Countermeasures](#arp-spoofing-countermeasures)
- [Packet Sniffing](#packet-sniffing)

### The Link Layer

Computers connect to a network via one or more network interface devices, for example an ethernet card or Wi-Fi adapter. These devices are what packets are transmitted between.

Most local area networks (LANs) broadcast frames. When a device is in regular mode each network interface gets the frames intended for it, however these devices can be configured to be in promiscuous mode, in which they may read all frames. This allows for traffic sniffing.

### Ethernet

Ethernet refers to both the physical medium used (typically cable), as well as the link-layer protocol (IEEE 802.3). Frames are transmitted via an ethernet cable and received by other machines on the same logical connection (network segment) of the LAN.

#### Collisions

Having multiple machines on the same segment can lead to collisions, resulting in discarding and retransmitting frames. Ethernet deals with collision events using a random-wait strategy (on the order of microseconds).

#### Ethernet Frame Format

| Layer | Preamble | Star frame delimiter (SFD) | MAC Destination | MAC Source | 802.1Q Tag (optional) | EtherType (Ethernet II) or Length (IEEE 802.3) | Payload | Frame check sequence (32-bit CRC) | Inter packet Gap (IPG) |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Length (octets) | 7 | 1 | 6 | 6 | (4) | 2 | 42-1500 | 4 | 12 |
| Position (bits) | 0 to 55 | 56 to 63 | 64 to 111 | 112 to 159 | ? | 160 to 175 | 176 to 543+ | 543+ to 575+ | 575+ to 671+ |
| Frame Area | Header | Header | Header | Header | Header | Header | Payload | Footer | Footer |
| Example | 10101010... | 10101011 | ... | ... | ... | ... | ... | ... | ... |

##### Physical Layer

###### Preamble

An ethernet packet starts with a seven octet (56 bit) preamble. The Preamble bit values alternate 1 and 0, allowing receivers to synchronize their clock at the bit level with the transmitter.

###### Start Frame Delimiter (SFD)

The preamble is followed by the SFD which ends with a 1 instead of a 0 to break the bit pattern of the preamble and signal the start of the frame.

##### Link Layer

###### Destination and Source MAC Addresses

These addresses are self explanatory.

###### Optional IEEE 802.1Q Tag

If present, is a four octet field that indicates virtual lan (VLAN) membership and IEEE 802.1p priority. The first two octets are the **T**ag **P**rotocol **ID**entifier (TPID) and double as the EtherType field indicating that the frame is either 802.1Q or 802.1ad tagged.

###### EtherType

Two octets long and can be used for two different purposes. Values of 1500 and below mean that it is used to indicate the size of the payload in octets, while values of 1536 and above indicate that it is used as an EtherType, to indicate which protocol is encapsulated in the payload of the frame. When used as EtherType, the length of the frame is determined by the location of the inter packet gap and valid frame check sequence (FCS).

###### Payload

Payload is a variable length field. Its minimum size is governed by a requirement for a minimum frame transmission of 64 octets (bytes). With header and FCS taken into account, the minimum payload is 42 octets when an 802.1Q tag is present, and 46 octets when absent. When the payload is less than the minimum, padding octets are added accordingly. IEEE standards specify a maximum payload of 1500 octets.

###### Frame Check Sequence (FCS)

Four octet cyclic redundancy check (CRC) that allows for detection of corrupted data within the entire frame as received on the receiver side. According to the standard, the FCS value is computed as a function of the protected MAC frame fields: source and destination address, length/type field, MAC client data and padding (that is, all fields except the FCS).

Per the standard, this computation is done using the left shifting CRC-32 algorithm. The standard states that data is transmitted least significant bit (bit 0) first, while the FCS is transmitted most significant bit (bit 31) first. An alternative is the right shifting CRC-32 algorithm, which will result in a CRC that is a bit reversal of the FCS, and transmit both data and the CRC least significant bit first, resulting in identical transmissions.

The standard states that the receiver should calculate a new FCS as data is received and then compare the received FCS with the FCS the receiver has calculated. An alternative is to calculate a CRC on both the received data and the FCS, which will result in a fixed non-zero "verify" value. (The result is non-zero because the CRC is post complemented during CRC generation). Since the data is received least significant bit first, and to avoid having to buffer octets of data, the receiver typically uses the right shifting CRC-32. This makes the "verify" value (sometimes called "magic check").

##### Physical Layer

###### End of Frame

The end of a frame is usually indicated by the end-of-data stream symbol at the physical layer or by loss of the carrier symbol. Early physical layers detect end of transmission by loss of the carrier, an example is 10BASE-T. Later physical layers use an explicit end of data or end of stream symbol or sequence to avoid ambiguity, especially where the carrier is continually sent frames; an example is Gigabit Ethernet with its 8b/10b encoding scheme that uses special symbols which are transmitted before and after a frame is transmitted.

###### Inter packet Gap

Idle time between packets. After a packet has been sent, transmitters are required to transmit a minimum of 96 bits (12 octets) of idle line state before transmitting the next packet.

#### Hub vs Switch

Machines on a LAN can be connected with either a hub or switch. Switches are predominately used today, though it's important to understand the distinction, especially as todays switches may sometimes act like hubs.

##### Hubs

Hubs logically connect multiple devices, allowing them to act as a single network segment. Hubs typically forward all frames to all attached devices.

In terms of effectiveness, this creates unnecessary bandwidth for the segment, and increases the likelihood of collisions. In terms of security, hubs can have a negative impact.

However hubs don't require overhead for proper addressing of frames.

##### Switches

A switch forwards each frame it receives along the cable it knows is connected to the destination for that frame. However switches may sometimes act like hubs:
- When devices are first connected to a switch until it learns the addresses of the machines that are connected to its various ports.
- If a frame is designated as one that should be broadcast.
- With a MAC flooding attack, an attacker can flood the switch with MAC addresses (causing denial of service). The switch defaults to fail open mode and acts like a hub.

Even when a switch behaves like a switch, there are still security risks (e.g., ARP spoofing).

#### Mac Addresses

Most network interfaces come with a predefined media access control (MAC) address. A mac address is a 48 bit (6 octet) number usually represented in hex. For example 00-1A-92-D4-B4-86.

The first three octets of any MAC address are IEEE assigned Organizationally Unique Numbers, e.g., Cisco is 00-1A-A.

The next three can be assigned by organizations as they please, with uniqueness being the only constraint. Organizations can utilize MAC addresses to identify computers on their network.

MAC addresses can be reconfigured by network interface driver software.

##### Viewing and Changing MAC Addresses

To view MAC addresses of the interfaces of a machine:
- Linux: `ip addr` (was `ifconfig`)
- Mac: `ifconfig`
- Windows: `ipconfig /all` 

Changing a MAC address in Linux:
1. Stop the networking service: `/etc/init.d/network stop`
2. Change the MAC address: `ifconfig eth0 hw ether <MAC-address>`
3. Start the networking service: `/etc/init.d/network start`

Changing a MAC address in MAC:
1. Disconnect from the internet.
2. Change the MAC address: `ifconfig eth0 ether <MAC-address>`
3. Reconnect to the internet.

Changing a MAC address in Windows:
1. Open Network Connections.
2. Access the properties for the network interface.
3. Click configure.
4. In the advanced tab, change the network address to the desired value.

Changing a MAC address requires admin privileges.

### ARP

The address resolution protocol (ARP) connects the network layer to the data link layer by converting IP addresses to MAC addresses. ARP works by broadcasting requests and caching responses for future use.

The protocol begins with a computer broadcasting a message of the form `who has <IP address1> tell <IP address2>`.

When the machine with \<IP address1\> or an ARP server receives this message, it broadcasts the response `<IP address1> is <MAC address>`.


#### Example 1

Data arrives from an external network at the gateway, and it must be forwarded to the IP address 192.168.1.19, however, IP addresses are not used to find nodes inside a LAN. Thus, the gateway has two options.
1. It already has the MAC address of this node thanks to an ARP announcement earlier.
2. It sends a request "who has this IP address" and the node in question replies with its MAC address.

#### Example 2

Computer A wants to communicate HTTP data to computer B. It needs B's MAC address to send on the same network segment. Assuming A has B's URL, from which a domain name can be recovered, A can use the DNS protocol to get B's IP address.

A checks if B's MAC address is in A's ARP table, if not, A sends an ARP request broadcast on the network segment asking for a MAC address corresponding to B's IP address. If a response is received, A caches the MAC address in the ARP table (with B's IP address) and sends an Ethernet frame.

#### Viewing ARP Table

The Linux command `ip neigh` (was `arp -a`, also works on Windows) displays the ARP table on your present device. You can also find the other devices on your network segment with `sudo arp-scan --interface=eth1 --localnet`.
- Your specific interface can be obtained from `ip addr`.
- You may need to use `sudo apt-get install arp-scan`.

#### ARP Packet and Announcements

| Layer | Hardware Type | Protocol Type | HLEN | PLEN | Operation | Sender Hardware Address | Sender Protocol Address | Target Hardware Address | Target Protocol Address |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Length (octets) | 2 | 2 | 1 | 1 | 2 | 6 | 4 | 6 | 2 |
| Positions (bits) | 0 to 15 | 16 to 31 | 32 to 39 | 40 to 47 | 48 to 63 | 64 to 111 | 112 to 143 | 144 to 191 | 192 to 207 |

###### Hardware Type (HTYPE)

The hardware type field specifies the network link protocol type. [A few examples are](https://www.iana.org/assignments/arp-parameters/arp-parameters.xhtml):
- Reserved: 0
- Ethernet (10Mb): 1
- Experimental Ethernet (3Mb): 2
- Amateur Radio AX.25: 3
- Proteon ProNET Token Ring: 4
- So on...

###### Protocol Type (PTYPE)

This field specifies the internetwork protocol for which the ARP request is intended. For IPv4, this has the value 0x0800 (2048 in decimal). The permitted values are the same as those for [EtherType](https://en.wikipedia.org/wiki/EtherType).

###### Hardware Length (HLEN)

Length in octets of hardware address. Ethernet MAC address length is 6.

###### Protocol Length (PLEN)

Length in octets of internetwork addresses. The internetwork protocol is specified in PTYPE. For example the IPv4 address length (in octets) is 4.

###### Operation

Specifies the operation that the sender is performing.
- Request: 1
- Reply: 2

###### Sender Hardware Address (SHA)

MAC address of the sender. This has two uses depending on what operation the ARP packet is performing.
- ARP Request: Used to indicate the address of the host sending the request.
- ARP Reply: Used to indicate the address of the host that the request was looking for.

###### Sender Protocol Address (SPA)

Internetwork address of the sender.

###### Target Hardware Address (THA)

MAC address of the intended receiver. This can be different depending on what operation the ARP packet is performing.
- ARP Request: This field is ignored.
- ARP Reply: This field is used to indicate the address of the host that originated the ARP request.

###### Target Protocol Address (TPA)

Internetwork address of the receiver.

##### ARP Announcements (Gratuitous ARP Message)

An ARP announcement (or gratuitous message) is an ARP reply that was not prompted by an ARP request. The gratuitous ARP is sent as a broadcast as a way for a node to announce or update its IP to MAC mapping to the entire network.

###### Packet Structure

In the ethernet header, the destination MAC address is addressed to ff:ff:ff:ff:ff:ff, or in binary, all 1s. This is the broadcast address for Ethernet.

The Hardware and Protocol Type, as well as the Hardware and Protocol Size fields, serve the same purpose as they do in traditional ARP packets. So they may be set to Ethernet, IPv4, 6 and 4 respectively in most scenarios.

The operation field (Opcode) is set to 2 indicating a response (Despite the fact that this packet was not actually prompted by a response).

The sender MAC and Sender Protocol Address contain the ARP mapping the initiator is advertising. These two are the important part of the ARP packet. They are the reason the ARP packet exists.

The Target MAC address is ff:ff:ff:ff:ff:ff, a reflection of the Destination MAC address in the Ethernet frame. In reality this field is irrelevant, they are ignored in a gratuitous ARP packet. Some implementations will use 00:00:00:00:00:00 in this field.

The Target Protocol Address once again confirms the protocol address that this particular ARP mapping is being created for, and is the same as the Sender Protocol Address.

#### ARP Spoofing

The ARP protocol is simply built, and susceptible to a spoofing or poisoning attack. According to the standard:
- ARP is a stateless protocol.
- The ARP Table is updated whenever an ARP response is received. Even if no ARP request has been sent!
- ARP announcements are not authenticated.

Machines trust each other. A rogue machine can spoof a machine by sending gratuitous ARP replies to "poison" other machines' ARP cache.

If the attacker associates the IP address of the gateway to her MAC address, she can:
- Sniff the traffic before forwarding it to the real gateway, i.e., passive attack.
- Modify the traffic before forwarding it, i.e., active attack, man-in-the-middle attack.

If the attacker associates the IP address of the gateway to a nonexistent MAC address:
- Outgoing traffic of the network is sent to this MAC address, denial of service attack.

Tools to do this:
- Arpoison
- Ettercap
- Arpspoof
- ...

#### ARP Security Issues

To summarize security issues presented thus far for the link layer:
- Eavesdropping of hub or switch communications.
- ARP spoofing for the purpose of eavesdropping.
- ARP spoofing for the purpose of denial of service.

There are other possible attacks too, such as MAC flooding. This can be used to not only cause a switch to behave like a hub (for purposes of eavesdropping), but also for continuing to flood the device for a denial of service attack.

#### ARP Spoofing Countermeasures

1. Use static ARP table entries.
    - Solves the problem but is almost impossible to manage.
2. DHCP Snooping
    - Keep a record of the MAC address of the machine connected on each port of the network device (e.g., switch).
    - Any received ARP announcement is checked before being forwarded to the network.
    - Implemented by CISCO, D-Link, Allied Telesis.
3. Looking for cloned MAC addresses
    - Inverse ARP could be used to return the IP address associated with a MAC address.
    - If more than one address is returned this indicates an ARP spoof.

### Packet Sniffing

Packet sniffers read information traversing a network by intercepting network packets, possibly using ARP cache poisoning.

Packet sniffing can also be used as a legitimate tool to analyze a network:
- Monitor network usage.
- Filter network traffic.
- Analyze network problems.

However it can also be used maliciously, such as to steal information (passwords, conversations, etc.) or to analyze network information to prepare for an attack.

Packet sniffers can be either software or hardware based.

#### Detecting Packet Sniffers

Sniffers are almost always passive, they simply collect data and do not attempt any "entry". This can make them extremely hard to detect, most detection methods require some suspicion already that sniffing is occurring. This leads to some sort of "ping" of the sniffer, i.e., some broadcast that will cause a response only from a sniffer.

Another solution on switched hubs is ARP watch. An ARP watch monitors the ARP cache for duplicate entries of a machine, if such duplicates appear, raise an alarm. However there may be false alarms, specifically, DHCP networks can have multiple entries for a single machine.

#### Stopping Packet Sniffing

A good approach for protection is to encrypt packets securely. Sniffers can capture the packets but they are meaningless. SSH is also a much more secure method of connection, as it uses private/public key pairs making sniffing virtually useless.

On switched networks, almost all attacks will be via ARP spoofing.
1. Add machines to a permanent store in the cache.
2. This store cannot be modified via a broadcast reply.
3. Thus, a sniffer cannot redirect an address to itself.

The best security is to not let them in your network in the first place. Sniffers need to be on your subnet in a switched hub to start sniffing. All sniffers need to somehow access root at some point to start themselves up.

#### Wireshark

Wireshark is a packet sniffer and protocol analyzer, it captures and analyses frame. Usually it is required to be ran with admin privileges. Setting the network interface in promiscuous mode captures traffic across the entire LAN segment and not just frames addressed to the machine.

http://www.wireshark.org/