<p align="center">
<img src="https://i.imgur.com/Ua7udoS.png" alt="Traffic Examination"/>
</p>

# Network Security Groups (NSGs) and Inspecting Traffic Between Azure Virtual Machines

## Overview
In this tutorial, we analyze network traffic between **Azure Virtual Machines** using **Wireshark** and experiment with **Network Security Groups (NSGs)** to control traffic flow effectively.

---

## Environments and Technologies Used
- **Microsoft Azure** (Virtual Machines/Compute)
- **Remote Desktop**
- **Various Command-Line Tools**
- **Network Protocols** (SSH, RDP, DNS, HTTP/S, ICMP)
- **Wireshark** (Protocol Analyzer)

## Operating Systems Used
- **Windows 10 (21H2)**
- **Ubuntu Server 20.04**

---

## High-Level Steps
1. **Preface**
2. **Observing ICMP Traffic**
3. **Configuring a Firewall (NSG)**
4. **Observing SSH Traffic**
5. **Observing DHCP Traffic**
6. **Observing DNS Traffic**
7. **Observing RDP Traffic**

---

## Preface
### Setting Up Wireshark on Windows VM
1. **Download and Install Wireshark** ([Get it here](https://www.wireshark.org))
2. **Launch Wireshark** and double-click on "Ethernet"

![Wireshark Ethernet](https://github.com/user-attachments/assets/ceddd3f1-7a22-43b0-a7fd-0e74cb27c9ee)

This allows real-time network traffic capture.

---

## Observing ICMP Traffic
### Filtering ICMP Traffic
1. Open **Wireshark** and filter for `icmp` packets.
2. Retrieve your **Ubuntu Server's private IP address** from network settings.
3. Open **PowerShell** and ping the Ubuntu server.

![ICMP Traffic](https://github.com/user-attachments/assets/05884ca3-b1ad-4c59-8122-31f757c0be53)

**Insight:** A successful ping confirms network connectivity between devices.

---

## Configuring a Firewall (NSG)
### Blocking ICMP Traffic
1. Start a **continuous ping** in PowerShell:
   ```powershell
   ping <private-IP-address> -t
   ```
2. Create an **NSG Inbound Rule**:
   - **Protocol:** ICMPv4
   - **Action:** Deny
   - **Priority:** 290

![NSG Rule](https://github.com/user-attachments/assets/bbe03e9e-5a3c-4f15-9984-73e4e086296f)

3. Observe **"Request timed out"** messages, confirming traffic is blocked.

![image](https://github.com/user-attachments/assets/92150cec-06d5-495a-ad50-4ec597784d65)
   
4. **Remove the NSG rule** and confirm the pings are going through again.

![image](https://github.com/user-attachments/assets/5559ce10-66a8-4b1d-9503-3320a11f73ad)

5. Now lets **stop** the ping: **Cntrl + C**

---

## Observing SSH Traffic
### Establishing an SSH Connection
1. Filter Wireshark for **SSH packets** (`ssh`).
2. Open **PowerShell** and run:
   ```powershell
   ssh <username>@<private-IP>
   ```
3. Enter the password and observe SSH traffic in Wireshark.

![SSH Traffic](https://github.com/user-attachments/assets/84bf3764-8e78-427d-a118-b232431d2852)

4. You've sucessfully established a connection between the two Virtual machines using SSH. From here, everything you do through the connection will be shown as encrypted packets in Wireshark until you disconnect: ***(exit > Enter within PowerShell)**

---

## Observing DHCP Traffic
### Requesting a New IP Address
1. **Restart Wireshark capture** and filter for `dhcp` traffic.
2. Open **PowerShell (as an Admin)**
3. Open **Notepad** and enter create and save this exact note, same as me:

![image](https://github.com/user-attachments/assets/64aff7b9-aec0-48ec-9173-5cbdeefc2e48)

(This is an executable file we will use to run a script (to release our IP address and request a new one) while we observe the traffic in Wireshark)

4. Back in Powershell enter the following command line:
 ```powershell
  cd c:\programdata
```

5. ```ls```
   *Here you can see the new file we added*
   
6. **Run it using the command: “.\new-ip.bat” > “enter”**

![image](https://github.com/user-attachments/assets/6ee9e31e-0749-4009-9b5b-e3783fc4e2a7)

* Results:
    * If you take a look at the Wireshark window you’ll see the entire process of the computer releasing the IP address > sending out a request for a new one > receiving an offer > accepting the offer for one
 
![image](https://github.com/user-attachments/assets/730128a7-c87c-42be-956f-7b4fd7ba7d65)

    * You’ll notice the IP is still the same, and that’s just because I’m on a Virtual Machine. If you did this on normal desktop for example, it would actually be a new IP address.

* Close Powershell and reopen it for the next section.

   
---

## Observing DNS Traffic
### Performing a DNS Lookup
1. **Filter Wireshark for DNS traffic (`dns`).**
2. Run in PowerShell:
   ```powershell
   nslookup google.com
   ```
3. Observe DNS queries and responses.

![DNS Traffic](https://github.com/user-attachments/assets/59c13ae6-7c53-4a99-88cd-54d860f26c5f)

---

## Observing RDP Traffic
### Monitoring RDP Activity
1. **Filter Wireshark for RDP traffic**:
   ```
   tcp.port == 3389
   ```
2. Observe **continuous traffic**, confirming Remote Desktop activity (which I've been using this whole time).

![RDP Traffic](https://github.com/user-attachments/assets/ed7f9a92-7a01-4882-9b82-c273ea4f458d)

---

## Conclusion
By completing this tutorial, you’ve learned how to:
- Monitor **ICMP, SSH, DHCP, DNS, and RDP** traffic in real-time.
- Set up an **NSG firewall** to manage inbound network traffic.
- Use **Wireshark** to analyze network packets efficiently.

