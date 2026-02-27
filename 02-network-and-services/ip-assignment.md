# Phase 2 — IP Assignment (WS2019-DC01, WS2019-FS01, Ubuntu-SRV01, W11-CL1)

## Summary

This document outlines how IP addresses were assigned within the isolated lab network.

The lab operates on a dedicated internal subnet (192.168.11.0/24) attached to a vSwitch with no physical uplink. All infrastructure servers were manually configured with static IP addresses to ensure consistent service availability. Client machines receive IP addresses dynamically from the Windows DHCP server.

Static addressing for core infrastructure prevents IP conflicts and ensures critical services such as Active Directory, DNS, and DHCP remain reachable at predictable addresses.

---

## Lab Network Overview

```
Network: 192.168.11.0/24
Subnet Mask: 255.255.255.0
Default Gateway: None (isolated internal network)
```

This network is fully isolated from the home router. There is no external DHCP server or internet connectivity at this stage.

---

## Static IP Assignment Planning

| Device Name       | Role                         | IP Address       | Subnet Mask      | Default Gateway | DNS Server       |
|------------------|------------------------------|-----------------|-----------------|----------------|-----------------|
| WS2019-DC01      | Domain Controller / DNS      | 192.168.11.10   | 255.255.255.0   | —              | 192.168.11.10   |
| WS2019-FS01      | File Server / DHCP           | 192.168.11.20   | 255.255.255.0   | —              | 192.168.11.10   |
| Ubuntu-SRV01     | Linux Server                 | 192.168.11.30   | 255.255.255.0   | —              | 192.168.11.10   |
| Windows 11 Client| Domain-Joined Client         | DHCP Assigned   | 255.255.255.0   | —              | 192.168.11.10   |

Infrastructure servers use static IP assignments.  
Client machines obtain addresses dynamically from the internal DHCP scope to be configured on WS2019-FS01.

---

### DHCP Scope Planning (Internal DHCP Server)

Since the lab is isolated, DHCP is provided by WS2019-FS01 instead of a home router.

The internal DHCP scope was planned as follows:

```
DHCP Scope Range: 192.168.11.100 – 192.168.11.200
```

This pool range is necessary to plan in order to avoid conflicts when assigning static IPs.

---

## Prerequisites

### vSwitch Creation (vSwitch-Lab)

To isolate the lab environment from the home network, a new standard vSwitch named **vSwitch-Lab** was created in ESXi.

This vSwitch was configured **without a physical uplink (no vmnic attached)**, meaning it does not connect to the home router or external network. All traffic on this switch remains internal to the ESXi host.

A new port group named **LAB-ISOLATED** was then created and attached to vSwitch-Lab.

All lab virtual machines (DC01, FS01, Ubuntu-SRV01,) were moved to this port group to ensure they operate exclusively within the 192.168.11.0/24 internal subnet.

This design:

- Prevents DHCP conflicts with the home router  
- Keeps all lab traffic contained  
- Creates a controlled environment for testing domain services  
- Allows full control over IP addressing and network behavior


---

## Firewall Configuration

### Overview

Firewall rules changed to allow necessary traffic between the Domain Controller, File Server, Ubuntu Server, and client machines.

By default, Windows Defender Firewall blocked certain inbound traffic. To ensure proper communication for services such as Active Directory, DNS, DHCP, file sharing, and remote management, specific rules were enabled.

---

## Windows Firewall Configuration (WS2019-DC01)

To allow basic network diagnostics within the isolated lab network, ICMPv4 echo requests were enabled on the Domain Controller.

This allows other lab machines to successfully ping the DC for connectivity verification.

### Action Performed

- Opened **Windows Defender Firewall with Advanced Security**
- Navigated to **Inbound Rules**
- Enabled rule:
  - **File and Printer Sharing (Echo Request – ICMPv4-In)**

This permits ICMP traffic within the internal lab subnet only.

---

## Configuring Static IP Addresses

### Windows Server – WS2019-DC01 (Domain Controller)

After verifying network connectivity and firewall configuration, a static IP address was assigned to the Domain Controller.

Static configuration ensures that core infrastructure services remain reachable at a consistent address.

---

### Configuration Applied

```
IP Address: 192.168.11.10
Subnet Mask: 255.255.255.0
Default Gateway: (None – isolated network)
Preferred DNS Server: 192.168.11.10
```

---

### Key Notes

- The Domain Controller points to itself for DNS.
- In an Active Directory environment, DNS is critical for locating domain services such as authentication and LDAP.
- No default gateway is configured because the lab operates on an isolated vSwitch with no external routing.

---

### Windows Server – WS2019-FS01 (File Server / DHCP)

The File Server was also configured with a static IP address to ensure consistent availability for file shares and DHCP services.

---

### Configuration Applied

```
IP Address: 192.168.11.20
Subnet Mask: 255.255.255.0
Default Gateway: (None – isolated network)
Preferred DNS Server: 192.168.11.10
```

---

### Important

All domain-joined systems must use the Domain Controller (192.168.11.10) as their primary DNS server.

Active Directory relies on DNS for:

- Domain controller discovery  
- Authentication services  
- Service location (SRV records)  
- LDAP communication  

For this reason, the DNS server should never point to an external resolver in an AD environment.

---

### Ubuntu Server – Static IP Configuration (Ubuntu-SRV01)

The Ubuntu Server was configured with a static IP address using Netplan to ensure consistent network identification within the isolated lab environment.

---

### Identify Netplan Configuration File

First, confirm the active Netplan configuration file: `ls /etc/netplan`

Edit the appropriate YAML file (example shown below):

`sudo nano /etc/netplan/50-cloud-init.yaml`

---

### Configuration Applied

The following static configuration was defined:

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
```

Notes:

- `dhcp4: false` disables automatic IP assignment
- `192.168.11.30/24` assigns a static IP within the reserved infrastructure range
- DNS points to the Domain Controller (192.168.11.10)
- No default gateway is configured, as the lab network is isolated with no external routing

---

### Apply Configuration

After saving the file: `sudo netplan try`

If no errors are returned, press **ENTER** to confirm and apply permanently.

---

## Network Connectivity Testing

After configuring static IP addresses on all infrastructure servers, connectivity was validated within the isolated subnet.

Since the lab operates without a gateway or internet access, only internal communication was tested.

---

### Tests Performed

1. Verify IP assignment: `ip a`
2. Ping Domain Controller (DNS + internal communication test): `ping 192.168.11.10`

All infrastructure servers successfully communicated within the 192.168.11.0/24 network.

---

## Result

All servers were successfully configured with static IP addresses within the reserved infrastructure range.

This ensures:

- Consistent network identification
- No dependency on external DHCP
- Reliable communication between AD, DNS, DHCP, and internal services

The isolated subnet provides a controlled environment for testing domain services and future lab expansion.

To continue, see the [Active Directory and DNS documentation](ad-and-dns-configuration.md).


