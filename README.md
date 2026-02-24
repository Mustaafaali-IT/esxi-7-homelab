# ESXi 7 Homelab

Enterprise-style VMware ESXi 7 homelab built on physical hardware for hands-on virtualization, infrastructure, and systems administration practice.

This lab is designed to simulate a small business environment using real-world infrastructure components including Active Directory, file services, DHCP, Exchange, Windows clients, and Linux servers.

---

## Lab Objectives

- Deploy VMware ESXi 7 on bare metal
- Design and manage VM storage architecture
- Build a multi-server Active Directory environment
- Configure core infrastructure services (DNS, DHCP, File Server)
- Deploy and manage Windows and Linux virtual machines
- Practice enterprise-style resource allocation and capacity planning
- Prepare for real-world systems administration and virtualization roles

---

## Project Phasing Model

This homelab is organized and documented in structured build phases.

Each phase represents a defined milestone in the infrastructure deployment process.  
Phases build on one another and simulate how real-world infrastructure projects are executed incrementally.

The repository will be progressively reorganized into clear phase-based documentation:

- [Phase 1 – ESXi Installation & Core VM Deployment](01-esxi-and-vm/esxi-and-vm-configuration.md)
- [Phase 2 – Network Design & Core Services (AD, DNS, DHCP, File Services)](02-network-and-services/network-and-services-configuration.md)


Each phase will contain:
- Goals and objectives
- Infrastructure design decisions
- Step-by-step implementation notes
- Concept explanations
- Validation testing
- Issues encountered and resolutions

---

## Current Hardware Configuration

**Host Machine**
- Intel i5-8600K (6 cores / 6 threads)
- 32GB RAM
- 500GB Seagate HDD (ESXi installation)
- 256GB Samsung 850 EVO SSD (VM datastore)
- Additional SSD storage planned for future expansion

---

## Storage Design

- ESXi installed on dedicated 500GB HDD
- 256GB SSD used exclusively for VM storage (VMFS datastore)
- Future expansion: Additional 512GB–1TB SSD for increased VM capacity

---

## Planned Virtual Infrastructure

Planned VM deployment (phased build):

| Role | OS | RAM Allocation |
|------|----|----------------|
| Domain Controller | Windows Server 2019 | 6GB |
| File Server | Windows Server 2019 | 6GB |
| DHCP Server | Windows Server 2019 | 4GB |
| Exchange Server | Windows Server 2019 | 8GB |
| Windows Client | Windows 11 | 6GB |
| Linux Server | Ubuntu 24.04 LTS | 4GB |

Initial phase will begin with:
- 1 Domain Controller
- 1 Windows 11 client
- 1 Ubuntu server

Additional services will be deployed incrementally.

---

## Repository Structure

```
esxi-7-homelab/
│
├── 01-esxi-and-vm/
│   ├── esxi-and-vm-configuration.md
│   ├── bios-configuration.md
│   └── screenshots/
│
├── 02-network-and-services/
│   ├── network-and-services-configuration.md
│   ├── ip-assignment.md
│   └── screenshots/
│
├── troubleshooting.md
│
└── README.md
```

Each directory contains step-by-step documentation of the lab build process.

---

## Long-Term Goals

- Implement vCenter Server (future phase)
- Expand storage capacity
- Introduce VLAN segmentation
- Practice high-availability concepts
- Build a realistic enterprise-style virtual infrastructure
- Use lab as portfolio evidence for IT roles
