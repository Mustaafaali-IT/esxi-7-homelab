# Phase 3 — Server Roles & Application Services (User Creation, Roles, NTFS, File Share, Apache, Web Services, Print Services)

## Goal

The objective of Phase 3 is to build on the core infrastructure established in Phase 2 by implementing practical server roles and real-world resource access control.

In this phase, the lab moves beyond basic domain functionality and into applied infrastructure design. This includes creating domain users and security groups, configuring shared folders with proper NTFS and share-level permissions, and validating access from a domain-joined client. This simulates real business file server behavior where identity and access control are centrally managed.

Following file service validation, Print Services will be deployed and published through the domain to test client-side resource discovery and role-based server functionality.

Finally, web services will be introduced using IIS on Windows Server and Apache on Ubuntu. This allows testing of cross-platform service hosting, DNS record management, port configuration, and firewall validation within the isolated lab network.

Phase 3 focuses on turning the lab into a functioning enterprise-style environment where identity, access control, and application services operate together under centralized management.

---

## What was built

In progress

---

## Order of Documentation

1. [Active Directory Users and NTFS Permissions](ad-users-and-ntfs-permissions.md)

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


## AD User Creation, File Share and NTFS Permissions

For actual steps, view the view the [AD user creation, file share and NTFS permissions documentation](ad-users-and-ntfs-permissions.md)

This section focuses on creating domain users within Active Directory and configuring shared folders on the File Server with proper NTFS permissions. The goal is to simulate a real-world environment where access to resources is centrally managed and controlled through user accounts and security groups.

These configurations allow us to validate authentication, authorization, and file access behavior from a domain-joined client, ensuring that identity and access control are functioning correctly within the lab infrastructure.