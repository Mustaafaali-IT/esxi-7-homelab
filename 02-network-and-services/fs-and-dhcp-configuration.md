# Windows Server File Server and DHCP Configuration

## Summary

This document outlines the configuration of WS2019-FS01 and the steps taken to join it to the `lab.local` domain and set up DHCP services.

This will allow the file server to authenticate users through Active Directory and operate as part of the lab’s centralized domain environment.

DHCP services will be used to provide IPs to joining client devices.

---

## Prerequisites

### Active Directory and DNS Configuration

Before configuring File Server and DHCP you must have configured Active directory and DNS.

See the [Active Directory documentation](ad-and-dns-configuration.md) for details.

---

### Windows Hostname Change

Running the `hostname` command in the CLI showed that the Windows servers were assigned random default names.

To align everything with the lab naming convention, the following PowerShell command was used:

`Rename-Computer -NewName "Computer-Name" -Restart`

This ensured each server followed the proper naming structure and prevented confusion later when managing domain roles and services.

---

## File Service Installation, Configuration and Verification

### 1) Initial Verification

Before joining the file server to the domain, internal network and DNS validation was performed to ensure proper communication with the Domain Controller.

The following values were verified using `ipconfig /all`:

```
IP Address: 192.168.11.20
Subnet Mask: 255.255.255.0
Default Gateway: (None – isolated network)
DNS Server: 192.168.11.10 (Domain Controller)
```

Validation performed:

- Successful ping to Domain Controller (192.168.11.10)
- Confirmed correct DNS server assignment

Name resolution testing:

- `ping WS2019-DC01`

Successful resolution confirmed that DNS was functioning correctly and that the server could locate the domain services required for domain join.

---

### 2) Join FS01 to the Domain (lab.local)

FS01 must be joined to the domain before installing and authorizing roles such as DHCP.

On **WS2019-FS01**:

1. Open **Server Manager**
2. Navigate to **Local Server**
3. Select the current **Workgroup** value to open System Properties
4. Click **Change**
5. Select **Domain**, then enter:
   - `lab.local`
6. When prompted, provide domain administrator credentials:
   - `LAB\Administrator`
7. Confirm the success message and reboot the server

After reboot, log in using a domain account and verify domain membership: `echo %USERDOMAIN%`

Expected output: LAB

This confirms successful domain join.

---

### 3) File Server Role Installation

After domain join, the File Server role was installed on WS2019-FS01.

Installed components:

- File and Storage Services
- File and iSCSI Services
- File Server role service

Installation was completed using a role-based installation via Server Manager.

Verification:

- Confirmed the File Server role appears under **Server Manager -> Roles**
- No installation errors were reported

---

## DHCP Installation, Configuration and Verification

In an Active Directory environment, DHCP servers must be authorized in AD before they can issue leases.

---

### 1) Install DHCP Server Role

On **WS2019-FS01**:

1. Open **Server Manager**
2. Select **Add Roles and Features**
3. Choose **Role-based or feature-based installation**
4. Select WS2019-FS01 from the server pool
5. Enable:
   - **DHCP Server**
6. Complete installation

After installation, the post-deployment configuration wizard was used to authorize the server in Active Directory.

Authorization was completed using: `LAB\Administrator`

---

### 2) Verification

To verify successful authorization:

1. Open **Tools -> DHCP**
2. Expand the server name

A green arrow next to the server confirms it has been successfully authorized in Active Directory.

If a DHCP server is not authorized in a domain environment, it will not issue leases. This mechanism ensures IP management remains controlled within the directory infrastructure.

---

### 3) DHCP Scope

A DHCP scope must be created and configured to define the pool of IP addresses that will be distributed to client machines.

