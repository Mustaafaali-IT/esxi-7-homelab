# Phase 2 — IP Plan + Core Services (AD/DNS, DHCP, File, Web Service, Print, Apache)

## Goal

The objective of phase 2 is to tranform standalone virtual machines into an enterprise style lab within ESXI. Core services including Active directory, DNS, DHCP, file services, web services, print services and linux-based Apache hosting.

This phase will tackle:

- Proper IP assignment (Static vs DHCP)
- Firewall configuration for inter-server communication
- Server role designation
- DNS configuration for Active Directoy
- File Server configuration to handle multiple roles (DHCP, web services, and print services)
- Deploying a Linux Apache Web server with SSH access
- Joining a Windows 11 client to the domain and testing authentication.

The final environment will simulate a small enterprise network where:

- WS2019-DC01 provides Active Directory and DNS
- WS2019-FS01 provides File Services, DHCP, Web Service, and Print Services
- Ubuntu-SRV01 provides Apache web services and SSH administration
- A Windows 11 client joins and authenticates against the domain

---

## What was built

In Progress

## Architecture snapshot

### Host

- Hypervisor: VMware ESXi 7.0U3
- Boot disk: 500GB Seagate HDD (ESXi installation)
- VM datastore: 256GB Samsung EVO SSD (`datastore-ssd01`, VMFS6)
- Primary NIC: Intel PRO/1000 GT Desktop Adapter
- Onboard Realtek NIC: Disabled in BIOS

---

### VMs

| VM Name         | Operating System        | vCPU | RAM | Disk  | Provisioning | Assigned Roles                 |
|-----------------|------------------------|------|-----|-------|-------------|--------------------------------|
| WS2019-DC01     | Windows Server 2019    | 2    | 6GB | 40GB  | Thick       | Active Directory, DNS         |
| WS2019-FS01     | Windows Server 2019    | 2    | 6GB | 40GB  | Thin        | File Services, DHCP, Web Service, Print |
| Ubuntu-SRV01    | Ubuntu Server LTS      | 2    | 4GB | 30GB  | Thin        | Apache Web Server, OpenSSH    |

**Note:** WS2019-DC01 was provisioned as Thick by default. This does not negatively impact lab functionality and was intentionally left unchanged.

---

## IP addressing plan

For actual steps on IP assignment view the [IP assignment documentation](ip-assignment.md) 

The lab environment is deployed on the home network subnet:

```

Network: 192.168.68.0/24  
Subnet Mask: 255.255.255.0  
Default Gateway: 192.168.68.1 
 
```

The home router DHCP pool is configured as:

```
192.168.68.100 – 192.168.68.250  

```


To avoid IP conflicts, static IP addresses for infrastructure servers are assigned outside the DHCP range (below .100).

---

### Static Assignments


| Device Name        | Role              | IP Address        | Subnet Mask       | Default Gateway   | DNS Server        |
|--------------------|------------------|------------------|------------------|------------------|------------------|
| WS2019-DC01        | Domain Controller / DNS | 192.168.68.10   | 255.255.255.0    | 192.168.68.1     | 192.168.68.10     |
| WS2019-FS01        | File Server / DHCP      | 192.168.68.20   | 255.255.255.0    | 192.168.68.1     | 192.168.68.10     |
| Ubuntu-SRV01       | Linux Server            | 192.168.68.30   | 255.255.255.0    | 192.168.68.1     | 192.168.68.10     |

---

### DHCP

Client machines (Windows 11) will receive IP addresses dynamically from the DHCP server once deployed.  
The DHCP scope will not overlap with static infrastructure addresses.

---

## Windows Hostname Change

Running the `hostname` command in the CLI showed that the Windows servers were assigned random default names.

To align everything with the lab naming convention, the following PowerShell command was used:

`Rename-Computer -NewName "Computer-Name" -Restart`

This ensured each server followed the proper naming structure and prevented confusion later when managing domain roles and services.

---

## Windows Server Active Directory and DNS

For actual steps on Active Directory and DNS installation, configuration and verification, view the [Active Directory documentation](ad-and-dns-configuration.md).

In this phase of the lab, a Windows Server 2019 virtual machine (WS2019-DC01) was promoted to a Domain Controller and configured with Active Directory Domain Services (AD DS) and DNS.

This setup simulates a small enterprise domain where authentication, users, groups, and policies are all managed centrally. The domain controller serves as the core identity system for the lab, handling DNS and authentication for any machines that join the domain.

This setup prepares the lab for:
- Domain-joined servers and clients
- Group Policy configuration
- Role-based access control
- File server permissions and shared resource management

---

## Windows Server File Service and DHCP

For actual steps on File Service and DHCP installation, configuration, and verification, view the [File Service documentation](fs-and-dhcp-configuration.md).

In this phase, WS2019-FS01 was configured to provide centralized file shari

This setup prepares the lab for:
- Domain-based file permissions using security groups
- Centralized shared folders with controlled NTFS and share permissions
- Automatic IP address assignment for client machines
- Testing real-world small business network infrastructure scenarios