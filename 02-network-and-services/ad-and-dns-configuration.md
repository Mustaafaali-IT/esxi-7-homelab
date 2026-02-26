# Windows Server Active Directory and DNS Installation and Confiuration (WS2019-DC01)

## Summary

This document outlines the installation, promotion and verification of Active Directory and DNS on the Windows Server DC (WS2019-DC01).

This Windows Server will act as the "brain" of the network and centrally manage authentication, users, groups, and policies for any machines that join the domain.

---

## Prerequisites

### Static IP Assignment

Before installing Active Directory you must set a static IP address.

See the [IP assignment documentation](ip-assignment.md) for steps on completion.

---

### Windows Hostname Change

Running the `hostname` command in the CLI showed that the Windows servers were assigned random default names.

To align everything with the lab naming convention, the following PowerShell command was used:

`Rename-Computer -NewName "Computer-Name" -Restart`

This ensured each server followed the proper naming structure and prevented confusion later when managing domain roles and services.

---

## Active Directory Deployment (WS2019-DC01)

### Objective

Deploy a Windows Server 2019 Domain Controller to provide centralized authentication, directory services, and DNS resolution for the lab environment.

---

## Initial Network Verification (Isolated Lab Network)

Before installing Active Directory, network configuration was verified to ensure the server was properly attached to the isolated lab vSwitch.

The lab network operates on:

Network: 192.168.11.0/24  
Role: Internal isolated subnet (no physical uplink)

Using `ipconfig /all`, the following values were confirmed:

```
IP Address: 192.168.11.10
Subnet Mask: 255.255.255.0
Default Gateway: (None – isolated network)
Preferred DNS: 192.168.11.10 (self)
```

### Verification Performed

- Confirmed IP configuration matched planned static assignment
- Verified network adapter was attached to the LAB-ISOLATED port group
- Confirmed no external gateway was present (expected for isolated design)

At this stage, internet connectivity is intentionally unavailable.  
All communication is restricted to internal lab systems.

These checks ensured the server had stable internal network configuration before proceeding with AD DS installation.

---

## AD DS Role Installation

The **Active Directory Domain Services (AD DS)** role was installed on `WS2019-DC01` using a role-based installation.

Installed components:
- Active Directory Domain Services
- DNS Server (automatically included during promotion)

No additional features were manually selected beyond the defaults required for AD DS.

---

## Domain Controller Promotion & Forest Creation

After installing the AD DS role, the server was promoted to a Domain Controller.

Configuration details:

- Deployment type: **Add a new forest**
- Root domain name: `lab.local`
- DNS Server: Enabled
- Global Catalog (GC): Enabled
- Forest functional level: Windows Server 2016 (default)
- Domain functional level: Windows Server 2016 (default)
- DSRM (Directory Services Restore Mode) password: Configured

The DNS delegation warning was expected and ignored, as this is a standalone lab environment with no parent DNS zone.

After prerequisite checks completed successfully, the server was promoted and automatically rebooted.

---

## Post-Installation Verification

After reboot, the server was logged into using:

`LAB\Administrator`

Verification steps performed:

- Confirmed AD DS and DNS roles were active in Server Manager
- Opened **Active Directory Users and Computers** to confirm domain structure
- Opened **DNS Manager** to verify:
  - Forward Lookup Zone for `lab.local` was created
  - Necessary SRV records were automatically registered

Ran diagnostic testing:

---
	
## Result

WS2019-DC01 is now:
- A Domain Controller
- Running Active Directory Domain Services
- Running DNS
- Hosting a new forest domain (lab.local)

This server now handles authentication and identity management for the lab environment.

To view next steps, view the [File System configuration documentation](fs-and-dhcp-configuration.md)
