# Phase 2 — IP Plan + Core Services (AD/DNS, DHCP, File, Web Service, Print, Apache)

## Goal

The objective of Phase 2 is to transform standalone virtual machines into an enterprise-style lab within ESXi. Core services including Active Directory, DNS, DHCP, file services, domain user management, and access control through NTFS permissions are implemented to simulate a centralized infrastructure environment.

This phase will tackle:

- Proper IP assignment (Static vs DHCP)
- Firewall configuration for inter-server communication
- Server role designation
- Active Directory and DNS configuration
- File Server and DHCP configuration
- Ubuntu Server assigned static IP (Web service in phase 3)
- Joining a Windows 11 client to the domain and testing authentication
- Creating domain users and organizational structure within Active Directory
- Configuring shared folders and implementing NTFS permissions
- Validating access control and file share permissions from a domain-joined client

The final environment will simulate a small enterprise network where:

- WS2019-DC01 provides Active Directory and DNS
- WS2019-FS01 provides File Services, DHCP, Web Service, and Print Services
- A Windows 11 client joins and authenticates against the domain
- Ubuntu-SRV01 provides Apache web services and SSH administration (Web service in Phase 3)

---

## What was built

In Progress

---

## Order of Documentation

1. [IP Assignment](ip-assignment.md)
2. [Active Directory and DNS (WS2019-DC01)](ad-and-dns-configuration.md)
3. [File Server and DHCP (WS2019-FS01)](fs-and-dhcp-configuration.md)
4. [Windows 11 Client (W11-CL01)](windows-client.md)
5. [AD User Creation, File share and NTFS Permissions](ad-users-and-ntfs-permissions.md) 

---

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
| W11-CL01     | Windows 11 Pro      | 2    | 6GB  | 60GB | Thin        | Client machine|

**Note:** WS2019-DC01 was provisioned as Thick by default. This does not negatively impact lab functionality and was intentionally left unchanged.

---

## IP Addressing Plan

For detailed configuration steps, see the [IP Assignment Documentation](ip-assignment.md).

The lab environment operates on an isolated internal subnet using a dedicated vSwitch with no physical uplink.

```
Network: 192.168.11.0/24
Subnet Mask: 255.255.255.0
Default Gateway: None (isolated network)
```

This design ensures:
- No dependency on the home router
- No external DHCP interference
- Full control over internal IP allocation

---

### Static Infrastructure Assignments

| Device Name   | Role                          | IP Address       | Subnet Mask      | Default Gateway | DNS Server       |
|---------------|------------------------------|-----------------|-----------------|----------------|-----------------|
| WS2019-DC01   | Domain Controller / DNS      | 192.168.11.10   | 255.255.255.0   | —              | 192.168.11.10   |
| WS2019-FS01   | File Server / DHCP           | 192.168.11.20   | 255.255.255.0   | —              | 192.168.11.10   |
| Ubuntu-SRV01  | Linux Server                 | 192.168.11.30   | 255.255.255.0   | —              | 192.168.11.10   |

Infrastructure servers use static IP assignments to ensure consistent service availability within the lab network.

### DHCP

Client machines (Windows 11) will receive IP addresses dynamically from the DHCP server.  
The DHCP scope will not overlap with static infrastructure addresses.

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

In this phase, WS2019-FS01 was configured to provide centralized file sharing and with DHCP services to lease IPs to joining clients.

This setup prepares the lab for:
- Domain-based file permissions using security groups
- Centralized shared folders with controlled NTFS and share permissions
- Automatic IP address assignment for client machines
- Testing real-world small business network infrastructure scenarios

---

## Windows 11 Pro Client

For actual steps on creating a windows 11 client VM and joining it to the `lab.local` domain , view the [Windows client documentation](windows-client.md).

The Windows 11 Pro virtual machine was created to simulate a real-world domain-joined workstation within the lab environment. It serves as a validation point for Active Directory, DNS, DHCP, and file services by acting as an endpoint that authenticates against the Domain Controller and receives its configuration dynamically from the infrastructure servers.

This client allows testing of domain logins, name resolution, IP lease assignment, and overall communication between client and server systems within the isolated 192.168.11.0/24 network.


## AD User Creation, File Share and NTFS Permissions

For actual steps, view the view the [AD user creation, file share and NTFS permissions documentation](ad-users-and-ntfs-permissions.md)

This section focuses on creating domain users within Active Directory and configuring shared folders on the File Server with proper NTFS permissions. The goal is to simulate a real-world environment where access to resources is centrally managed and controlled through user accounts and security groups.

These configurations allow us to validate authentication, authorization, and file access behavior from a domain-joined client, ensuring that identity and access control are functioning correctly within the lab infrastructure.


