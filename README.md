<div align="center">

# 🔐 Cybersecurity Lab Environment Setup

**Building an isolated virtual lab for penetration testing and ethical hacking practice**
</div>


## 📌 Project Overview

This project focuses on setting up a **virtual cybersecurity and penetration-testing laboratory** using VirtualBox and Kali Linux.

The purpose of the lab is to create a controlled environment where cybersecurity tools, network scanning, reconnaissance, vulnerability assessment, and other security-testing activities can be performed safely and repeatedly.

The lab is configured on a private virtual network so that additional machines can be added later and used as targets for authorized security testing.

---


## 🎯 Objectives

The main objectives of this project are to:

- Install and configure VirtualBox.
- Install/import Kali Linux as a virtual machine.
- Create a private **NAT Network** for the cybersecurity lab.
- Configure network connectivity for Kali Linux.
- Assign a consistent IP address to the Kali VM.
- Verify network connectivity and DNS resolution.
- Take a clean VM snapshot for recovery.
- Document the complete setup process.
- Prepare the environment for future cybersecurity projects.

---

## 🛡️ Purpose of the Lab

The lab provides an isolated and controlled environment for cybersecurity learning and authorized security testing.

It can be used for activities such as:

- Network reconnaissance
- Port scanning
- Vulnerability assessment
- Packet analysis
- Web security testing
- Exploitation practice
- Security-tool experimentation

⚠️ **Important:** This laboratory must only be used for systems that you own or have explicit permission to test. Do not use the lab or its tools to attack unauthorized systems.

---

## 🏗️ Lab Architecture

![](1-screenshot-title-image.png)


Additional target machines can be added to the same virtual network in future projects.

---

## ⚙️ Lab Configuration

| 🧩 Component       | ⚙️ Configuration   |
| ------------------ | ------------------  |
| 🖥️ Host OS         | Windows 10         |
| 🧠 Host RAM        | 8 GB               |
| ⚡ Processor       | Intel Core i7      |
| 🧰 Hypervisor      | VirtualBox 7.2  |
| 🐉 Security OS     | Kali Linux 2026.2  |
| 🧠 Kali RAM        | 2048 MB            |
| 🌐 Virtual Network | NAT Network        |
| 📡 Network Address | 10.0.0.0/24        |
| 🐧 Kali IP Address | 10.0.0.2/24        |
| 🚪 Default Gateway | 10.0.0.1           |
| 🌍 DNS Server      | 8.8.8.8            |
| 🔮 Future VM Range | 10.0.0.3–10.0.0.99 |

---

# 🪜 Lab Setup Procedure

## Step 1. Install 7-Zip

7-Zip was installed to extract the Kali Linux virtual-machine package, which may be distributed as a `.7z` archive.

**Tool:** 7-Zip

---

## Step 2. Install VirtualBox

VirtualBox was installed as the hypervisor.

---

## Step 3. Create the NAT Network

A dedicated NAT Network was created in VirtualBox.

Configuration:
Network Name: NatNetwork
IPv4 Prefix:  10.0.0.0/24
DHCP:         Enabled
IPv6:         Disabled

![](2-screenshot-network-settings-1.png)

A **NAT Network** was selected because multiple virtual machines connected to the same NAT Network can communicate with one another while also having outbound network connectivity.

This will allow future attacker and target VMs to communicate within the lab.


---

## Step 4. Import Kali Linux

The Kali Linux virtual machine was downloaded from the official Kali Linux website and imported into VirtualBox.

The VM network adapter was configured as follows:

```text
Adapter 1
Attached to: NAT Network
Network:     NatNetwork
Adapter Type: Intel PRO/1000 MT Desktop
```

The VM was allocated:

```text
RAM: 2048 MB
```
![](3-screenshot-kali-linux.png)
A shared folder was also configured for transferring required files between the host operating system and the Kali VM.



---

## Step 5. Configure the Kali Linux Network

The Kali Linux network configuration was checked and configured with a consistent IPv4 address.

Example configuration:

```text
IP Address: 10.0.0.2
Subnet Mask: 255.255.255.0
Gateway: 10.0.0.1
DNS: 8.8.8.8
```

A consistent IP address makes it easier to document the lab and reference the Kali machine in future exercises.

![](4-screenshot-kali-network-settings.png)

---

## Step 6. Create a Clean VM Snapshot

After completing the initial configuration, a VirtualBox snapshot was created.

Example snapshot name:

```text
Clean Kali - Network Setup
```

The snapshot represents the clean baseline of the laboratory.

If a future exercise changes or damages the VM configuration, the machine can be restored to this baseline.


---

# 🔎 Lab Verification

| ✅ Test                        | 🧾 Command                      | 🎯 Expected Result              |
| ----------------------------- | ------------------------------- | ------------------------------- |
| 🌐 Check IP address           | `ip a`                          | Correct Kali IP displayed       |
| 📡 Test gateway               | `ping 10.0.0.1`                 | Successful replies              |
| 🌍 Test Internet connectivity | `ping 8.8.8.8`                  | Successful replies              |
| 🔎 Test DNS resolution        | `nslookup networkwalks.com`     | Domain resolves                 |
| 🧰 Verify Nmap                | `nmap --version`                | Nmap version displayed          |
| 🔄 Verify snapshot            | Restore snapshot and run `ip a` | Baseline configuration restored |

### Example Results

```text
IP Address:
10.0.0.2/24

Gateway:
10.0.0.1

DNS:
8.8.8.8
```
# Problems Encountered & Solutions

During the setup and configuration of my Kali Linux virtual machine using VirtualBox, I encountered several technical problems. These challenges helped me understand virtualization, network configuration, and troubleshooting.

## Problem 1: Internet Connectivity Failure After Static IP Configuration

While configuring the IPv4 settings of my Kali Linux virtual machine, I manually assigned a static IP address, subnet mask, default gateway, and DNS server.

However, after saving the configuration, I discovered that the internet connection was not working properly.

### Investigation

I used the following Linux commands to investigate the problem:

```bash
ip route
```

```bash
ip addr
```

The commands revealed that my network interface (`eth0`) had two IPv4 addresses:

- Static IP address: `10.0.0.2/24`
- DHCP-assigned IP address: `10.0.2.15/24`
- Default gateway: `10.0.0.1`

This indicated that both static and DHCP addressing were active on the same network interface, with addresses belonging to different subnets.

### Solution

One workaround used during the troubleshooting process was to execute:

```bash
sudo nmcli connection modify "Wired connection 1" ipv4.dad-timeout 0
```

After applying the configuration, I restarted the network connection and tested internet connectivity again.

**Important:** The actual NetworkManager connection name may differ. I must identify the correct connection name before executing the command.

I also learned that static IP configuration must match the actual VirtualBox network mode, subnet, and gateway.

---

## Problem 2: VirtualBox VT-x / Hardware Virtualization Error

When I initially attempted to start my Kali Linux virtual machine, VirtualBox displayed an error indicating that hardware virtualization was unavailable or disabled.

### Cause

Hardware virtualization (Intel VT-x) was disabled in my computer's BIOS/UEFI firmware.

### Solution

I resolved the problem by following these steps:

1. Restarted my computer.
2. Entered the BIOS/UEFI settings.
3. Located the virtualization settings.
4. Enabled Intel VT-x (hardware virtualization).
5. Saved the changes and restarted the computer.
6. Opened VirtualBox and started the Kali Linux virtual machine again.

After enabling hardware virtualization, the virtual machine started successfully.

---

# What I Learned

Through troubleshooting these problems, I gained practical knowledge of virtualization and network configuration.

### 1. NAT vs NAT Network

I learned that standard NAT and NAT Network are different VirtualBox networking modes.

NAT provides internet access to a virtual machine, while NAT Network allows multiple virtual machines connected to the same virtual network to communicate with one another and access external networks.

### 2. Virtual Machine Networking

I learned how VirtualBox network adapters connect virtual machines to different types of networks and how the selected network mode affects communication.

### 3. Static IP Configuration

I gained practical experience configuring IPv4 addresses, subnet masks, default gateways, and DNS servers in Kali Linux.

### 4. Network Troubleshooting

I learned how to use Linux networking commands such as `ip addr`, `ip route`, and `nmcli` to inspect network configurations and troubleshoot connectivity problems.

### 5. VM Snapshots

I learned the importance of creating a clean virtual machine snapshot before performing risky experiments or making major configuration changes.

Snapshots provide a recovery point that allows me to restore the virtual machine to a previous working state.

### Conclusion

Although I encountered technical challenges during the setup process, troubleshooting them improved my understanding of Linux networking, VirtualBox configuration, and virtualization. I also learned the importance of documenting errors, investigating their causes, and testing solutions rather than making random configuration changes.


# 🔐 Security & Ethical Use

This laboratory is intended strictly for education purposes only.

---

# 🔗 Tools & Resources

- **7-Zip:** [https://7-zip.org/download.html](https://7-zip.org/download.html)
- **VirtualBox:** [https://virtualbox.org/wiki/Downloads](https://virtualbox.org/wiki/Downloads)
- **Kali Linux:** [https://kali.org/get-kali](https://kali.org/get-kali)

LinkedIn:www.linkedin.com/in/obi-chukwuemerie-samuel-357807406



---

## 📌 Project Information

**Program Name:** Cybersecurity Lab Setup | **Project:** Cybersecurity & Pentesting Lab Setup | **Repository:** GitHub
