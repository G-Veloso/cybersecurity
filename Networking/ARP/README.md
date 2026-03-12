# Address Resolution Protocol (ARP) | Technical Documentation

##  Overview
The **Address Resolution Protocol (ARP)** is a fundamental Layer 2 protocol used to map a dynamic **IP address** (Network Layer) to a physical **MAC address** (Data Link Layer). 

In an Ethernet network, devices use IP addresses to identify each other logically, but the actual delivery of data frames requires the hardware address of the network interface card (NIC).

---

##  Operational Workflow (The 4 Steps)

ARP operates through a simple request-and-response mechanism:

1.  **ARP Cache Lookup**: Before sending data, the host checks its internal `ARP Cache` table. If the mapping exists, it skips to step 4.
2.  **ARP Request (Broadcast)**: If the MAC is unknown, the host sends a broadcast frame to `FF:FF:FF:FF:FF:FF`.
    * *Message:* "Who has IP `192.168.1.5`? Tell `192.168.1.2`."
3.  **ARP Reply (Unicast)**: The device with the target IP responds directly to the requester.
    * *Message:* "I am `192.168.1.5`, and my MAC is `AA:BB:CC:DD:EE:FF`."
4.  **Cache Update**: The requester stores the mapping in its cache and proceeds to encapsulate the IP packet into an Ethernet frame.

---

##  Network Boundaries
* **Local Scope**: ARP is a non-routable protocol. It only works within a single broadcast domain (LAN).
* **Gateway Interaction**: To reach a destination outside the local network, a host performs an ARP request for its **Default Gateway** (the router), not the final destination IP.

---

##  Security & SOC Perspective
Since ARP lacks native authentication, it is vulnerable to several Layer 2 attacks:

* **ARP Spoofing / Poisoning**: An attacker sends falsified ARP messages to link their MAC address with the IP address of a legitimate server or gateway.
* **Man-in-the-Middle (MitM)**: Attackers intercept traffic between two hosts to steal credentials or sensitive data.
* **Mitigation**:
    * **DAI (Dynamic ARP Inspection)**: Validates ARP packets on switches.
    * **Static ARP**: Manual mapping for high-security servers.

---

##  Hands-on Commands

### Checking and Managing ARP Table

```bash
Display the ARP cache table

arp -a

Delete a specific entry (Troubleshooting)

arp -d 192.168.1.1

Add a static entry (Security)

 Windows:
arp -s <IP_Address> <MAC_Address>

 Linux:
ip neigh add <IP_Address> lladdr <MAC_Address> dev <interface>

Packet Analysis (Wireshark Filters)

arp                # Show all ARP traffic
arp.opcode == 1    # Filter by ARP Requests
arp.opcode == 2    # Filter by ARP Replies
