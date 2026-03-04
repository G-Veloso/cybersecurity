# A Technical Deep Dive into the 5-Layer Model (Kurose)


## 📡 The 5-Layer Stack (Kurose Model)

| Layer | PDU (Data Name) | Core Function | Example Protocol |
| :--- | :--- | :--- | :--- |
| **5. Application** | Message | Network application support | HTTP, DNS, SMTP, FTP |
| **4. Transport** | Segment | Process-to-process data transfer | TCP, UDP |
| **3. Network** | Datagram | Host-to-host routing from source to destination | IP, ICMP, BGP |
| **2. Data Link** | Frame | Data transfer between network neighbors | Ethernet, Wi-Fi (802.11) |
| **1. Physical** | Bits | Physical transmission of bits over the medium | Cables, Radio Waves |

---

## 🔍 Detailed Breakdown by Layer

### **Layer 5: Application**
* **Focus:** The content of the conversation.
* **What is it?** Where data is generated and end-user protocols (HTTP, DNS, SMTP) operate.
* **In summary:** Most well-known malwares enter through the application layer. Whether via malicious websites (HTTP, HTTPS), infected downloads, email attachments, or phishing.
* **Where is the danger?** It is the most "exposed" layer. This is where Phishing, SQL Injection, Ransomware, and attacks exploiting human error or website code flaws reside. It interacts directly with users, making it extremely dangerous.
* **How the attacker acts:** They send data that looks legitimate but hides a malicious payload (e.g., a PDF with a virus or a fake link).
* **Common Attacks:** * Phishing (malicious links and emails)
    * Malware and Ransomware distributed via downloads
    * SQL Injection and other injection attacks
    * XSS (Cross-Site Scripting)
    * DNS Tunneling (abusive use of the DNS protocol)
* **Blue Team Defense:** Operates with EDR/Antivirus (detects execution at the endpoint), WAF (blocks attack patterns in HTTP requests), Email Filters, and application log/traffic monitoring.

### **Layer 4: Transport (The Mail for the Distribution Center)**
* **Focus:** How the conversation is delivered (Reliable or Fast). Uses Ports.
* **What is it?** Responsible for delivery (TCP for reliability, UDP for speed).
* **Where is the danger?** The danger lies in Exhaustion.
* **Common Attacks:** SYN Flood (resource exhaustion/DDoS) and Port Scanning (mapping open ports).
* **How the attacker acts:** They send thousands of connection requests (SYN) but never complete them, making the server "lose its breath" trying to respond to everyone.
* **Blue Team Defense:** Configures IPS/IDS to detect scans and uses SYN Cookies to mitigate flood attacks.

### **Layer 3: Network (IP - The House Address)**
* **Concept:** The "smartest" layer in the middle of the stack. It makes the Internet a global network. This is where IP Addresses and Routers reside.
* **The Analogy:** It is the GPS and the Postal Addressing System. The IP is your full address. The router is the distribution center that reads the ZIP code and directs the package.
* **Technical Foundation:**
    * **Routing vs. Forwarding:** Routing is planning the trip (the map); Forwarding is moving the packet from the router's input to its output.
    * **IP (Internet Protocol):** An "unreliable" protocol. It makes a best effort to deliver, but the layer above (Transport/TCP) is responsible for notifying if something goes missing.
* **Security Risk:** IP Spoofing. Like sending a letter with a fake return address so the reply (or the blame) goes to someone else.

### **Layer 2: Data Link (The Truck and the License Plate)**
* **Concept:** Solves how two neighboring devices (on the same "wire" or Wi-Fi) talk without crashing into each other. Organizes bits into Frames.
* **The Analogy:** The local delivery truck. It has a plate (MAC) and knows how to go from "Garage A" to "Garage B." It only knows how to take the packet to the next stop.
* **Technical Foundation:**
    * **MAC Address:** A 48-bit address, unique in the world, burned into the network card.
    * **Flow Control and Error Detection:** Prevents the receiver from being "drowned" and discards corrupted packets.
* **Security Risk:** ARP Spoofing. Like a malicious neighbor painting their truck's license plate with your number to receive your deliveries.

### **Layer 1: Physical (The Road and the Fuel)**
* **Concept:** Doesn't understand "sites" or "IPs." It only understands energy and the conversion of bits into signals (voltage, light, or radio waves).
* **The Analogy:** The paving of the road. It doesn't matter if the truck carries gold or trash; if the road is full of potholes (noise/interference) or if the bridge collapses (cut cable), nothing arrives.
* **Technical Foundation:** Bandwidth (road capacity) and Attenuation (weakening of the signal over distance).
* **Security Risk:** Jamming. Like throwing dense smoke on the road so the driver cannot see the path.

---

## 📦 Summary for Fixation (The Key)

1. **Physical:** "Is there a signal?" (Energy)
2. **Data Link:** "Who is my neighbor?" (MAC Address)
3. **Network:** "Where is the final destination in the world?" (IP Address)
4. **Transport:** "Did the message arrive whole and at the right app?" (Port/TCP)
5. **Application:** "What should the app do with this message?" (HTTP/DNS/Data)

In the **SOC**, when a server goes down, you investigate from **bottom to top**. If the alert says "SYN Flood," check Layer 4. If it says "SQL Injection," check Layer 5. If it says "ARP Poisoning," check Layer 2.

---

## 🔄 Encapsulation and Decapsulation

1. **Application:** Data is "pure" (Message).
2. **Transport:** Adds the "Port" (e.g., Watch App vs. Shoes App). Now it is a **Segment**.
3. **Network:** Adds the IP for the post office (Routers). Now it is a **Datagram**.
4. **Link:** Adds the MAC for the local "hop." Now it is a **Frame**.
5. **Physical:** Transforms everything into **Bits** (0 and 1).

**Decapsulation (The reverse process upon arrival):**
1. The doorman checks the MAC and receives the container (**Data Link**).
2. The manager checks the IP on the envelope and says "it's for this building" (**Network**).
3. The resident checks the Port on the box and delivers it to the recipient (**Transport**).
4. The friend opens the box and reads the Card (**Application**).
