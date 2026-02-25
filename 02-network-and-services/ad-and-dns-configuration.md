# Windows Server Active Directory and DNS Installation and Confiuration (WS2019-DC01)

## Summary

This document outlines the installation, promotion and verifiaction of Active Directory on the Windows Server DC (WS2019-DC01).

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

## Installation, Configuration and Verifiaction

### 1) Initial Verification

Before installing Active Directory, The following values were verified using `ipconfig /all`:

```

IP Address: 192.168.68.10  
Subnet Mask: 255.255.255.0  
Default Gateway: 192.168.68.1  
Preferred DNS: 192.168.68.10 (self)

```

- Confirmed gateway connectivity using:
  - `ping 192.168.68.1`

- Confirmed internet connectivity using:
  - `ping 8.8.8.8`

These checks ensured the server had stable network connectivity before role installation.

---

### 2) Active Directory Installation

1. Open **Server Manager**
2. Click **Manage** (top right)
3. Select **Add Roles and Features**
4. Click **Next** 
5. Select **Role-based or feature-based installation**
6. Click **Next**
7. Select a server from the server pool (WS2019-DC01)
8. Click **Next**
9. Under **Server Roles**, check:
   - Active Directory Domain Services
10. Click **Add Features** when prompted
11. Click **Next**
12. Leave Features as default -> Click **Next**
13. Click **Next** (AD DS information page)
14. Click **Install**

Wait for installation to complete.

Do NOT close Server Manager yet. Do NOT reboot the PC yet.

---

### 3) Promote Server to Domain Controller and Configure

After installation finishes:

1. In Server Manager (top right), click the **flag notification icon**
2. Click **Promote this server to a domain controller**
3. Select:
   - Add a new forest
4. Enter your root domain name  
   Example:
   `lab.local`
5. Click **Next**
6. Leave defaults selected:
   - Domain Name System (DNS) server
   - Global Catalog (GC)
7. Set a **Directory Services Restore Mode (DSRM) password**
8. Click **Next**
9. Ignore the delegation warning (normal in a lab)
10. Click **Next**
11. Confirm NetBIOS name (usually auto-filled)
12. Click **Next**
15. Review configuration
16. Click **Next**
17. Wait for prerequisites check to complete
18. Click **Install**

The server will automatically reboot.

---

### 4) Post-Installation Verification

After reboot:

1. Log in using:
   `LAB\Administrator`  
   (Replace LAB with your domain name if different)

2. Open **Server Manager**
3. Confirm on the left hand side:
   - AD DS role is installed
   - DNS role is installed

4. Open:
   - **Tools -> Active Directory Users and Computers**
   - **Tools -> DNS**

Verify your domain appears and forward lookup zone is created.

5. open **CMD** as administrator and run `dcdiag`
	
	We are looking for:
	- All tests passed
	- no critical failures.
	
---
	
## Result

WS2019-DC01 is now:
- A Domain Controller
- Running Active Directory Domain Services
- Running DNS
- Hosting a new forest domain (lab.local)

This server now handles authentication and identity management for the lab environment.

To view next steps, view the [File System configuration documentation](fs-and-dhcp-configuration.md)
