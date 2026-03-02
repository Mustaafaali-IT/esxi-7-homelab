# Apache Web Server Deployment and Domain Join (Ubuntu)

## Summary

This document covers the installation and configuration of an Apache web server on the Ubuntu Server within the isolated lab network, joining it to the lab.local domain and configuring SSH.

The objective is to simulate a Linux-based web service operating inside a Windows Active Directory environment. This mirrors real-world enterprise scenarios where Linux web servers rely on Windows DNS infrastructure for name resolution and client access.

This configuration will include:

- Installing Apache
- Configuring the default web root
- Validating service status and port binding
- Creating an internal DNS record on the Domain Controller
- Testing name-based resolution from a Windows 11 domain-joined client
- Verifying client-to-server communication over HTTP

---

### VMs In Use

| VM Name         | Operating System        | vCPU | RAM | Disk  | Provisioning | Assigned Roles                 |
|-----------------|------------------------|------|-----|-------|-------------|--------------------------------|
| WS2019-DC01     | Windows Server 2019    | 2    | 6GB | 40GB  | Thick       | Active Directory, DNS |
| WS2019-FS01     | Windows Server 2019    | 2    | 6GB | 40GB  | Thin        | File Services, DHCP, Print Server |
| Ubuntu-SRV01    | Ubuntu Server LTS      | 2    | 4GB | 30GB  | Thin        | Apache Web Server, OpenSSH    |
| W11-CL01     | Windows 11 Pro      | 2    | 6GB  | 60GB | Thin        | Client machine|

**Note:** WS2019-DC01 was provisioned as Thick by default. This does not negatively impact lab functionality and was intentionally left unchanged.

---

## Prerequisites

### Phase 2 - Core Networking Infrastructure

Before user creation and file sharing you must first create the core networking infrastructure.

See the [Phase 2 – Core Network and Domain Infrstructure (DNS, DHCP, File Services, Windows Clients)](../02-core-network-and-domain/README.md) for steps on completion.

---

## Ubuntu DNS Configuration and Verification

Before deploying Apache, DNS resolution on the Ubuntu server was properly integrated into the Active Directory environment.

Initially, the server was able to reach the Domain Controller via IP, but hostname resolution was inconsistent due to the systemd stub resolver not properly forwarding DNS requests.

Rather than bypassing systemd-resolved or manually overriding `/etc/resolv.conf`, DNS was configured correctly through the Netplan configuration file.

The DNS server was set to the Domain Controller (`192.168.11.10`), and the internal domain search suffix (`lab.local`) was defined. The new static IP configuration is as defined:

```
	network:
  version: 2
  renderer: networkd
  ethernets:
    ens160:
      dhcp4: false
      addresses:
        - 192.168.11.30/24
      nameservers:
        addresses:
          - 192.168.68.10
		search:
		  - lab.local
```

After applying the Netplan configuration, DNS resolution was validated using:

- `nslookup ws2019-dc01.lab.local`
- `resolvectl status`

---

## Temporary Internet Access for Package Installation

Because the Ubuntu server resides inside an isolated lab network, it does not have internet access by design.

To install Apache and required dependencies, a temporary secondary network adapter was attached to the VM and connected to the original internet-enabled vSwitch. The existing lab interface was not modified or removed. This preserves internal DNS configuration and Active Directory integration.

DHCP was temporarily enabled on the secondary interface via Netplan. This allowed the server to obtain a default route for outbound internet access for package installation.

Key design decisions:

- The original lab interface remained statically configured.
- No DNS changes were made during this step.
- Internet access was treated as a temporary build-stage requirement.

Once Apache was successfully installed and validated, the secondary network adapter was removed and the Netplan configuration was reverted to restore full network isolation.

---

## Apache Web Server installation and Configuration

### Apache Installation and Service Validation

With temporary internet access enabled, Apache was installed using the standard package manager.

After installation, service state was verified using:

- `systemctl status apache2`
- `curl localhost`

The service reported:

- Loaded
- Enabled
- Active (running)

Apache was then tested from the Windows 11 domain-joined client by browsing directly to the Ubuntu server’s lab IP address. The default Apache page loaded successfully, confirming:

- Apache is listening on all interfaces
- Port 80 is open
- Cross-network routing between Windows and Linux is functional
- No firewall restrictions are blocking HTTP traffic

Following validation, the temporary internet adapter was removed and Netplan was restored to isolated lab configuration.

At this stage, Apache is fully installed and operational within the isolated network.

---

### Publishing the Ubuntu Web Server in Active Directory DNS

With Apache successfully installed and validated by IP address, the next step was integrating the Linux web server into centralized Active Directory DNS.

An A record was created in the `lab.local` DNS zone on the Domain Controller.

A new host record was added:

- Hostname: `ubuntu-srv01`
- IP Address: (Ubuntu LAB interface – 192.168.11.X)

This publishes the Linux server into the same authoritative DNS namespace used by all domain-joined systems.

---

### Name-Based Resolution Validation

After creating the DNS record, resolution was tested from the Windows 11 domain-joined client.

Validation steps included:

- `ping ubuntu-srv01.lab.local`
- Accessing `http://ubuntu-srv01.lab.local` in a web browser

The hostname resolved correctly to the Ubuntu server’s LAB IP address, and the default Apache page loaded successfully.

This confirms:

- The Domain Controller is serving as authoritative DNS for the lab
- Windows clients resolve Linux-hosted services through AD DNS
- Cross-platform communication is functioning correctly
- HTTP traffic is allowed across the segmented network
- Service discovery is centralized and name-based

Resolution was also verified from the Ubuntu server itself using `nslookup`, ensuring forward lookup consistency within the zone.

---

## Active Directory Integration

### Preparing Ubuntu for Active Directory Integration

To enable domain joining and centralized authentication, the required AD integration packages were installed. These packages provide Kerberos authentication, identity resolution, domain discovery, and PAM integration.

The following components were installed:

- `realmd` (domain discovery and join utility)
- `sssd` and `sssd-tools` (identity and authentication services)
- `adcli` (AD join helper)
- `libnss-sss` and `libpam-sss` (Linux identity and PAM integration)
- `samba-common-bin` (AD compatibility)
- `oddjob` and `oddjob-mkhomedir` (automatic home directory creation)

These packages collectively allow Ubuntu to authenticate against Active Directory using Kerberos while resolving users and groups via SSSD.

---

### Domain Discovery and Join Process

Before joining the domain, the hostname was verified to ensure proper alignment with internal DNS conventions. The server was already configured with a static IP address and using the Domain Controller for DNS resolution.

Domain discovery was validated using:

- `realm discover lab.local`

Successful discovery confirmed:

- The AD domain is reachable
- Kerberos services are accessible
- DNS SRV records are functioning
- Ubuntu can communicate with the Domain Controller

The server was then joined to the domain using:

- `realm join lab.local -U Administrator`

This created a computer object in Active Directory and established trust between Ubuntu and the domain.

Post-join validation using:

- `realm list`

confirmed that the machine is now a member of `lab.local`.

---

### Domain Authentication Validation

After joining, domain identity resolution was verified.

Running:

- `id labuser@lab.local`

returned a valid UID, GID, and domain group membership. This confirms:

- SSSD is resolving domain identities
- Kerberos authentication is functioning
- AD groups are mapped correctly to Linux

The domain user was then used to authenticate directly on the Ubuntu server.

Successful login demonstrated:

- Centralized authentication via Active Directory
- No local Linux account required
- Proper Kerberos ticket issuance
- Cross-platform identity integration

---

### Home Directory Management for Domain Users

Upon initial login, the domain user did not automatically receive a home directory.

To resolve this, PAM was configured to enable automatic home directory creation using `oddjob-mkhomedir`.

After enabling this setting and restarting SSSD, domain users were able to log in with home directories created dynamically under:

`/home/username@lab.local`

This ensures:

- No manual user provisioning on Linux
- Clean separation between local and domain accounts

---
## SSH Configuration

### SSH Service Deployment and Initial Validation

SSH (Secure Shell) was configured to allow encrypted remote access to the server from other machines within the lab environment.

---

### Installing and Enabling OpenSSH Server

The OpenSSH server package was installed to provide remote shell access over TCP port 22.

After installation:

- The SSH service was enabled to start automatically at boot
- The service was started and verified using `systemctl`
- Service status confirmed it was running and listening

This ensures the server remains remotely manageable even after reboots.

---

### Connectivity Testing

Remote access was validated from the Windows 11 domain client using the built-in OpenSSH client.

Successful login confirmed:

- Port 22 is reachable across the network
- Firewall rules are not blocking SSH traffic
- Kerberos / SSSD authentication works over SSH
- Active Directory domain credentials are accepted
- No local Linux account dependency is required

The login prompt displayed the domain-based identity:

`labuser@lab.local@ubuntu-srv01`

This confirms centralized authentication through Active Directory rather than local user management.

---
