# AD User Creation, File Share and NTFS Permissions

##Summary

This document covers creating domain users in Active Directory and setting up shared folders on the File Server with proper NTFS permissions.

The goal is to simulate a real-world setup where user accounts are centrally managed and access to files is controlled based on permissions. These configurations are then tested from the Windows 11 domain-joined client to confirm that authentication and access control are working correctly within the lab.

---

## Prerequisites

### File and DHCP Server Configuration

Before user creation and file sharing you must first create the core networking infrastructure.

See the [Windows 11 client documentation](windows.client.md) for steps on completion.

---