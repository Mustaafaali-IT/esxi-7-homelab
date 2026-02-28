# AD User Creation, File Share and NTFS Permissions

## Summary

This document covers creating domain users in Active Directory and setting up shared folders on the File Server with proper NTFS permissions.

The goal is to simulate a real-world setup where user accounts are centrally managed and access to files is controlled based on permissions. These configurations are then tested from the Windows 11 domain-joined client to confirm that authentication and access control are working correctly within the lab.

We will:

- Create an AD User
- Create an AD Security Group
- Create a Shared Folder on FS01
- Configure NTFS Permissions
- Configure Share Permissions
- Test access from Windows 11 client
- Verify permission behavior

---

## Prerequisites

### Phase 2 - Core Networking Infrastructure

Before user creation and file sharing you must first create the core networking infrastructure.

See the [Phase 2 – Core Network and Domain Infrstructure (DNS, DHCP, File Services, Windows Clients)](../02-core-network-and-domain/README.md) for steps on completion.

---

