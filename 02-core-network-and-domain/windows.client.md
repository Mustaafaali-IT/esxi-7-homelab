# Creating and Joining a Windows 11 Client to Active Directory

## Summary

This document outlines the deployment and configuration of a Windows 11 virtual machine (W11-CL01) that functions as a domain-joined client within the lab environment.

The client is connected to the isolated 192.168.11.0/24 subnet and is configured to obtain its IP address dynamically from the internal DHCP server (WS2019-FS01). After receiving a lease, the system is joined to the `lab.local` Active Directory domain to simulate a typical enterprise workstation setup.

This configuration allows testing of:

- Domain authentication
- Group Policy application
- DNS resolution within the domain
- Centralized user management
- File share access and permission validation

The Windows 11 client serves as the primary endpoint for validating Active Directory, DNS, DHCP, and file service functionality within the lab infrastructure.

---

## Prerequisites

### File and DHCP Server Configuration

Before creating and joining a Windows 11 client you must first create the core networking infrastructure.

See the [file and DHCP server documentation](fs-and-dhcp-configuration.md) for steps on completion.

---

## Creating the Windows 11 Virtual machine

### Configuration

The Windows 11 Virtual machine was configures as follows:

| VM Name         | OS                     | vCPU | RAM  | Disk | Provisioning | Notes |
|----------------|-------------------------|------|------|------|--------------|------|
| W11-CL01     | Windows 11 Pro      | 2    | 6GB  | 60GB | Thin        | Client machine|


The Windows 11 Client VM was created from the locally stored ISO in `datastore-ssd01` and was attached to the `LAB-ISOLATED` port group that was previously created.

upon configuring the VM, ESXi 7 only had to option to select guest OS version Microsoft Windows 10 (64-bit). 

This isn't an issue since Windows 11 and Windows 10:

- Use the same NT kernel family
- Same driver model
- Same virtual hardware support

The only Windows 11-specific requirement is:

- TPM 2.0
- Secure Boot
- UEFI firmware

This was corrected by customizing VM boot options, selecting EFI as firmware used to boot, and enabling secure boot. 

ESXi 7 host does not have a virtual TPM encryption key provider, so the TPM requirement was bypassed during install. This will not effect the duration of the lab.

---

## Windows Installation

### TPM Bypass

Since this Windows 11 VM is running in a virtualized lab environment, the default installation process requires TPM 2.0 support, which is not configured on this VM.

To proceed with installation, the TPM requirement was bypassed during setup by accessing the Windows installation registry editor (Shift + F10) and creating the appropriate `LabConfig` registry keys to disable TPM and Secure Boot checks. This allows Windows 11 Pro to install in a controlled virtual environment without modifying host hardware or enabling virtual TPM.

This adjustment was made strictly for lab purposes and does not impact domain functionality, networking, or Active Directory integration. Once installed, the system operates normally as a domain-joined client within the isolated 192.168.11.0/24 subnet.

---

### Network Connection Bypass

Because the lab operates on an isolated network with no internet access, Windows 11 setup cannot complete the out-of-box experience (OOBE) using the default network requirement.

To proceed, the network requirement was bypassed by opening Command Prompt during setup (Shift + F10) and running: `OOBE\BYPASSNRO`

This restarts the setup process and enables the option to continue without an internet connection, allowing the VM to be configured for offline domain integration within the lab environment.

---
 
## Windows Hostname Change

Running the `hostname` command in the CLI showed that the Windows servers were assigned random default names.

To align everything with the lab naming convention, the following PowerShell command was used:

`Rename-Computer -NewName "Computer-Name" -Restart`

This ensured each server followed the proper naming structure and prevented confusion later when managing domain roles and services.

---

## Verify DHCP Lease

After logging in, the client’s network configuration was validated using: `ipconfig /all`

The following values were confirmed:

```
DNS Suffix Search List: lab.local
DHCP Enabled: Yes
IPv4 Address: 192.168.11.100 (Within configured DHCP scope)
DHCP Server: 192.168.11.20 (WS2019-FS01)
DNS Server: 192.168.11.10 (WS2019-DC01)
```

This confirms:

- The client successfully obtained an IP lease from the internal DHCP server
- DNS settings were correctly assigned
- The system is aware of the `lab.local` domain

Additional validation included successful ping requests between the client and infrastructure servers, confirming proper network communication within the 192.168.11.0/24 subnet.

---

## Joining the lab.local Domain

The Windows 11 client was joined to the `lab.local` domain through System Properties by entering the domain name and authenticating with a domain administrator account (`LAB\Administrator`). After reboot, logging in with the domain account confirmed the machine was successfully joined and communicating with the Domain Controller.

## Result

The Windows 11 virtual machine was successfully deployed and integrated into the lab environment.

- TPM and network checks were bypassed to allow installation within the isolated lab
- The client obtained a dynamic IP address from the internal DHCP server
- DNS settings were correctly assigned to the Domain Controller (192.168.11.10)
- The system was successfully joined to the `lab.local` domain
- Domain authentication was verified after reboot

The Windows 11 client now functions as a fully domain-joined workstation within the 192.168.11.0/24 isolated subnet. This confirms proper integration between DHCP, DNS, Active Directory, and client systems within the lab infrastructure.

## Next Steps

This concludes phase 2, and leads us to phase 3, [Server Roles & Application Services](../03-server-roles-and-application-services/README.md)

First, we will begin creating domain users in Active Directory to simulate a real-world environment where accounts are centrally managed instead of using local machine users.

Shared folders will be created on the File Server, and NTFS permissions will be configured to control which users or groups have access. These permissions will then be tested from the Windows 11 domain-joined client to confirm that authentication and access control are working as expected.

This will allow the lab to move beyond basic domain connectivity and into practical access management and permission validation.

To view the full next steps, view the [Phase 3 - Server Roles & Application Services dcoumentation](../03-server-roles-and-application-services/README.md)