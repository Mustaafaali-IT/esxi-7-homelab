# ESXi 7 Homelab

Enterprise-style VMware ESXi 7 homelab built on physical hardware for hands-on virtualization, infrastructure, and systems administration practice.

This lab is designed to simulate a small business environment using real-world infrastructure components including Active Directory, file services, DHCP, Exchange, Windows clients, and Linux servers.

---

## 🎯 Lab Objectives

- Deploy VMware ESXi 7 on bare metal
- Design and manage VM storage architecture
- Build a multi-server Active Directory environment
- Configure core infrastructure services (DNS, DHCP, File Server)
- Deploy and manage Windows and Linux virtual machines
- Practice enterprise-style resource allocation and capacity planning
- Prepare for real-world systems administration and virtualization roles

---

## 🖥️ Current Hardware Configuration

**Host Machine**
- Intel i5-8600K (6 cores / 6 threads)
- 32GB RAM
- 500GB Seagate HDD (ESXi installation)
- 256GB Samsung 850 EVO SSD (VM datastore)
- Additional SSD storage planned for future expansion

---

## 🗂️ Storage Design

- ESXi installed on dedicated 500GB HDD
- 256GB SSD used exclusively for VM storage (VMFS datastore)
- Future expansion: Additional 512GB–1TB SSD for increased VM capacity

---

## 🏗️ Planned Virtual Infrastructure

Planned VM deployment (phased build):

| Role | OS | RAM Allocation |
|------|----|----------------|
| Domain Controller | Windows Server 2022 | 6GB |
| File Server | Windows Server 2022 | 6GB |
| DHCP Server | Windows Server 2022 | 4GB |
| Exchange Server | Windows Server 2022 | 8GB |
| Windows Client | Windows 11 | 6GB |
| Linux Server | Ubuntu 24.04 LTS | 4GB |

Initial phase will begin with:
- 1 Domain Controller
- 1 Windows 11 client
- 1 Ubuntu server

Additional services will be deployed incrementally.

---

## 📁 Repository Structure

```
esxi-7-homelab/
│
├── 01-planning/
│   ├── hardware-overview.md
│   ├── network-design.md
│   └── project-scope.md
│
├── 02-installation/
│   ├── bios-configuration.md
│   ├── esxi-installation.md
│   └── initial-setup.md
│
├── 03-networking/
│   ├── vSwitch-configuration.md
│   ├── port-groups.md
│   └── vlan-setup.md
│
├── 04-storage/
│   ├── datastore-configuration.md
│   └── disk-layout.md
│
├── 05-virtual-machines/
│   ├── vm-creation.md
│   ├── templates.md
│   └── snapshots.md
│
├── 06-troubleshooting/
│   └── issues-and-fixes.md
│
├── diagrams/
│   └── network-topology.png
│
└── README.md
```

Each directory contains step-by-step documentation of the lab build process.

---

## 🚀 Long-Term Goals

- Implement vCenter Server (future phase)
- Expand storage capacity
- Introduce VLAN segmentation
- Practice high-availability concepts
- Build a realistic enterprise-style virtual infrastructure
- Use lab as portfolio evidence for IT roles
