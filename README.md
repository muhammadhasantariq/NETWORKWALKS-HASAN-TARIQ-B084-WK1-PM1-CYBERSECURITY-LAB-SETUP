<div align="center">

# 🔐 Cybersecurity Lab Environment Setup

**An isolated virtual lab on Apple Silicon for ethical hacking and penetration-testing practice**
</div>

<p align="center">
  <img src="https://img.shields.io/badge/Skill-Cybersecurity-404040?style=flat-square&labelColor=C00000" />
  <img src="https://img.shields.io/badge/Ver-Virtualbox%20v7.2-0070C0?style=flat-square&labelColor=000000" />
  <img src="https://img.shields.io/badge/Kali%20Linux-v2026.3%20ARM64-E87500?style=flat-square&labelColor=000000&logo=kalilinux&logoColor=white" />
  <img src="https://img.shields.io/badge/Host-macOS%20Apple%20M4-404040?style=flat-square&labelColor=C00000&logo=apple&logoColor=white" />
  <img src="https://img.shields.io/badge/Network-10.0.0.0%2F24-238F89?style=flat-square&labelColor=000000" />
  <img src="https://img.shields.io/badge/Skill-Virtualization-404040?style=flat-square&labelColor=C00000" />
  <img src="https://img.shields.io/badge/GitHub-404040?style=flat-square&labelColor=0070C0&logo=github&logoColor=white" />
  <img src="https://img.shields.io/badge/NetworkWalks-404040?style=flat-square&labelColor=C00000" />
  <img src="https://img.shields.io/badge/Ethical%20Hacking-E87500?style=flat-square&labelColor=000000&logo=kalilinux&logoColor=white" />
</p>

---

## 📌 Project Overview

This is my Week 1 project for the Networkwalks Cybersecurity program. I built a **virtual cybersecurity testing lab** on my own MacBook Air (Apple M4) using VirtualBox, with **Kali Linux as the attacker machine**.

The lab sits on a private virtual network, so extra machines can be added later as targets for authorized practice. Everything in this project runs on my own device.

---

## 🎯 Objectives

- Install the latest VirtualBox for my host.
- Install Kali Linux as a virtual machine.
- Create a private **NAT Network** in the `10.0.0.0/24` subnet.
- Attach Kali to that network with the static IP `10.0.0.2/24`.
- Confirm Kali has internet access.
- Enable clipboard and drag/drop, and share the host `Downloads` folder.
- Take a snapshot so the VM can be restored.
- Document the process, including the problems I hit.

---

## 🏗️ Lab Architecture

```text
        MacBook Air (Apple M4, macOS)
        └── VirtualBox 7.2.22
            └── NAT Network "NatNetwork"  10.0.0.0/24
                ├── Kali Linux (attacker)   10.0.0.2/24
                └── Future target VMs       10.0.0.3 and up
```

---

## ⚙️ Lab Configuration

| 🧩 Component | ⚙️ Configuration |
| --- | --- |
| 🖥️ Host | MacBook Air, Apple M4 (macOS) |
| 🧰 Hypervisor | VirtualBox 7.2.22 (macOS / Apple Silicon) |
| 🐉 Security OS | Kali Linux 2026.3 (ARM64 installer) |
| 🧠 Kali RAM / CPUs | 4096 MB / 2 CPUs |
| 💾 Kali disk | 20 GB |
| 🌐 Virtual network | NAT Network `NatNetwork`, DHCP enabled |
| 📡 Network address | 10.0.0.0/24 |
| 🐧 Kali IP address | 10.0.0.2/24 |
| 🚪 Default gateway | 10.0.0.1 |
| 🌍 DNS server | 8.8.8.8 |
| 📂 Shared folder | Host `Downloads` folder (Full access, auto-mount) |

> **Apple Silicon note:** VirtualBox on an Apple Silicon Mac can only run **ARM64** guests. The pre-built Intel/AMD64 Kali VirtualBox image from the course slides does not run here, so I installed Kali from the **ARM64 installer ISO** instead.

---

# 🪜 Lab Setup Procedure

## Step 1. Install VirtualBox

I downloaded the **macOS / Apple Silicon** package from virtualbox.org, installed it, and allowed it in *System Settings → Privacy & Security* when macOS asked.

7-Zip is not needed on my setup because Kali comes as an ISO and not as a `.7z` archive.

---

## Step 2. Create the NAT Network

In VirtualBox: *Tools → Network → NAT Networks → Create*.

```text
Name:        NatNetwork
IPv4 Prefix: 10.0.0.0/24
DHCP:        Enabled
IPv6:        Disabled
```

![NAT Network settings](screenshots/01-nat-network.png)

A NAT Network lets all VMs on it talk to each other and still reach the internet, which suits a multi-machine lab.

---

## Step 3. Create the Kali Linux VM

I downloaded the Kali **ARM64 installer ISO** from kali.org and created a VM in VirtualBox with 4096 MB RAM, 2 CPUs and a 20 GB disk, then ran the graphical installer.

---

## Step 4. Configure the VM network adapter

```text
Adapter 1
Attached to:      NAT Network
Name:             NatNetwork
Adapter Type:     Intel PRO/1000 MT Desktop (82540EM)
Promiscuous Mode: Allow All
```

![Kali VM network settings](screenshots/02-vm-network.png)

---

## Step 5. Clipboard, drag and drop, and shared folder

- **Settings → General → Advanced:** Shared Clipboard and Drag'n'Drop set to **Bidirectional**.
- **Settings → Shared Folders:** my Mac's `Downloads` folder added with **Full** access and **Auto Mount** on.

![Shared folder settings](screenshots/03-shared-folders.png)

---

## Step 6. Set the static IP in Kali

The panel had no network icon, so I configured the connection with NetworkManager in the terminal:

```bash
sudo nmcli connection modify "Wired connection 1" ipv4.method manual ipv4.addresses 10.0.0.2/24 ipv4.gateway 10.0.0.1 ipv4.dns 8.8.8.8
sudo nmcli connection modify "Wired connection 1" ipv4.dad-timeout 0
sudo nmcli connection down "Wired connection 1"
sudo nmcli connection up "Wired connection 1"
```

The `ipv4.dad-timeout 0` line is the workaround from the course guide for internet problems on newer VirtualBox and Kali versions.

---

## Step 7. Verify the IP address and internet access

```bash
ip a
ping -c 4 google.com
```

Result: `eth0` has **10.0.0.2/24**, and the ping got **4 of 4 replies with 0% packet loss**.

![IP address and ping result](screenshots/04-ip-and-ping.png)

---

## Step 8. Take a snapshot

I took a VirtualBox snapshot called **"my fresh kali linux"** so I can restore the VM if a later exercise breaks something.

![Snapshot list](screenshots/05-snapshot.png)

---

# 🔎 Lab Verification

| ✅ Test | 🧾 Command | 🎯 Result |
| --- | --- | --- |
| 🌐 Check IP address | `ip a` | `eth0` shows 10.0.0.2/24 |
| 🌍 Test internet and DNS | `ping -c 4 google.com` | 4 of 4 replies, 0% loss |
| 🔄 Snapshot | VirtualBox Snapshots tab | Snapshot listed |

---

# 🐞 Problems Encountered & Solutions

## Problem 1. The Intel Kali image does not run on an Apple Silicon Mac

The course slides use the Intel/AMD64 Kali VirtualBox image, but VirtualBox on an M-series Mac only runs ARM64 guests.

**Solution:** I used Kali's **ARM64 installer ISO** with VirtualBox's macOS / Apple Silicon build.

## Problem 2. "No application set to open" for the Extension Pack

I downloaded the VirtualBox Extension Pack before installing VirtualBox, so macOS had nothing to open it with.

**Solution:** I installed the main **macOS / Apple Silicon** package first. The Extension Pack was not needed for this lab.

## Problem 3. macOS permission prompts

When the VM started, macOS asked for permissions such as Accessibility and Input Monitoring.

**Solution:** I allowed VirtualBox in *System Settings → Privacy & Security*.

## Problem 4. No network icon in the Kali panel

The usual *Edit Connections* method from the slides was not available.

**Solution:** I set the static IP with `nmcli` in the terminal (see Step 6).

---

# 💡 What I Learned

1. **CPU architecture matters in virtualization.** An Apple Silicon Mac runs ARM64 virtual machines, so I had to pick the ARM64 build of Kali instead of the Intel one.
2. **NAT vs NAT Network.** A NAT Network lets VMs reach each other and the internet, which is what a multi-VM lab needs.
3. **Static IP with NetworkManager.** I can configure and check the address, gateway and DNS from the terminal with `nmcli` and `ip a`.
4. **Snapshots protect my work.** A snapshot of a known-good state makes it safe to experiment later.
5. **Documenting problems is part of the job.** Writing down what failed and how I fixed it makes the lab reproducible.

---

# 🔐 Security & Ethical Use

This lab is for education only. It runs on my own device and virtual network. I will only test systems that I own or have written permission to test.

---

# 🔗 Tools & Resources

- **VirtualBox:** [https://virtualbox.org/wiki/Downloads](https://virtualbox.org/wiki/Downloads)
- **Kali Linux:** [https://kali.org/get-kali](https://kali.org/get-kali)

---

# 👤 Author

**Muhammad Hasan Tariq**\
Cybersecurity Intern, Batch [YOUR BATCH]

---

## 📌 Project Information

**Program:** Cybersecurity at Networkwalks | **Week:** 01 | **Project:** WK1-PM1 Cybersecurity Lab Setup | **Repository:** GitHub
