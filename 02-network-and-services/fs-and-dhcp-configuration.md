# Windows Server File Server and DHCP Configuration

## Summary

This document outlines the configuration of WS2019-FS01 and the steps taken to join it to the `lab.local` domain and set up DHCP services.

This will allow the file server to authenticate users through Active Directory and operate as part of the lab’s centralized domain environment.

DHCP services will be used to provide IP to joining client devices.

---

## Prerequisites

### Static IP Assignment

Before configuring the File Server and DHCP you must have set a static IP address.

See the [IP assignment documentation](ip-assignment.md) for steps on completion.

---

### Active Directory and DNS Configuration

Before configuring File Server and DHCP you must have configured Active directory and DNS.

See the [Active Directory documentation](ad-and-dns-configuration.md) for steps on completion.

---

### Windows Hostname Change

Running the `hostname` command in the CLI showed that the Windows servers were assigned random default names.

To align everything with the lab naming convention, the following PowerShell command was used:

`Rename-Computer -NewName "Computer-Name" -Restart`

This ensured each server followed the proper naming structure and prevented confusion later when managing domain roles and services.

---

## File Service Installation, Configuration and Verification

### 1) Initial Verification

Before joining the file server to the domain, initial network and DNS validation was performed to ensure proper communication with the Domain Controller.

The following values were verified using `ipconfig /all`:

```

IP Address: 192.168.68.20  
Subnet Mask: 255.255.255.0  
Default Gateway: 192.168.68.1  
DNS Server: 192.168.68.10 (Domain Controller)

```

Basic network connectivity was validated:

- Successful ping to gateway (192.168.68.1)
- Successful ping to Domain Controller (192.168.68.10)

Name resolution was tested to confirm domain awareness:

- `ping WS2019-DC01`
- `nslookup lab.local`

Successful resolution confirms that DNS is functioning correctly and the server can locate the domain services required for domain join.

---

### 2) Join FS01 to the Domain (lab.local)

FS01 must be joined to the domain before installing and authorizing roles like DHCP.

1. On **WS2019-FS01**, open **Server Manager**.
2. Click **Local Server**.
3. Click the **Workgroup** value (ex. `WORKGROUP`) to open System Properties.
4. Click **Change…**
5. Select **Domain**, then enter:
   - `lab.local`
6. When prompted for credentials, enter a domain admin account:
   - `LAB\Administrator`
7. Confirm the success message (**Welcome to the lab.local domain**) and reboot FS01.
8. Log in using a domain account (LAB\Administrator), then open Command Prompt and run:

	`echo %USERDOMAIN%`
	
	The output should be LAB

---

### 3) File Server Role Installation 

1. Open **Server Manager**
2. Click **Manage** -> **Add Roles and Features**
3. Select **Role-based or feature-based installation**
4. Select a server from the server pool (WS2019-DC01)
5. Navigate and enable:
   - **File and Storage Services**
   - **File and iSCSI Services**
6. Ensure **File Server** role is selected
7. Proceed through wizard and install.
8. Confirm File Server role appears under **Server Manager -> Roles**

---

## DHCP Installation, Configuration and Verification

DHCP in a domain environement must be authroized in AD in order to serve leases to joining clients.

### 1) Install DHCP Server Role

1. Open **Server Manager**
2. Click **Manage** -> **Add Roles and Features**
3. Select **Role-based or feature-based installation**
4. Select a server from the server pool (WS2019-FS01)
5. Under **Server Roles**, select:
   - **DHCP Server**
6. Click **Add Features**
7. Continue through the wizard and click **Install**

After installation, a notification flag appears in Server Manager.

8. Click the **notification flag**
9. Select **Complete DHCP configuration**
10. When prompted for credentials, use:
	
	`LAB\Administrator`
	
4. Click **Commit**
5. Click **Close**

### 2) Verification

1. Open **Server Manager**
2. Navigate to **Tools** -> **DHCP**
3. Expand the server name

A green arrow next to the server confirms successful authorization in Active Directory.


**Note:** In a domain environment, a DHCP server must be authorized in Active Directory before it can start handing out IP addresses. If it is not authorized, the DHCP service will not issue any leases. This prevents unauthorized DHCP servers from distributing IPs and helps keep IP management controlled within the domain.

### 3) DHCP Scope

We must create and configure the DHCP Scope so that the service has a pool of IP's to distrubute.