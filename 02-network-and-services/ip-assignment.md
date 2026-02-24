# Phase 2 — IP Assignment (WS2019-DC01, WS2019-FS01, Ubuntu-SRV01, W11-CL1)

## Summary

This document outlines how IP addresses were assigned to the Windows Servers, Ubuntu Server, and Windows 11 client. The Windows and Ubuntu servers were manually configured with static IP addresses outside of the DHCP pool range. This ensures the DHCP server does not automatically assign an IP address that is already being used by a server.

### Static IP Assignment Summary

| Device Name        | Role              | IP Address        | Subnet Mask       | Default Gateway   | DNS Server        |
|--------------------|------------------|------------------|------------------|------------------|------------------|
| WS2019-DC01        | Domain Controller / DNS | 192.168.68.10   | 255.255.255.0    | 192.168.68.1     | 192.168.68.10     |
| WS2019-FS01        | File Server / DHCP      | 192.168.68.20   | 255.255.255.0    | 192.168.68.1     | 192.168.68.10     |
| Ubuntu-SRV01       | Linux Server            | 192.168.68.30   | 255.255.255.0    | 192.168.68.1     | 192.168.68.10     |
| Windows 11 Client  | Domain-Joined Client    | DHCP Assigned   | 255.255.255.0    | 192.168.68.1     | 192.168.68.10     |

---

## Identifying Network Information

Before assigning static IP addresses, the following information must be confirmed:

- Default Gateway
- Subnet Mask
- DHCP Pool Range

### Finding the Default Gateway & Subnet Mask (Windows)

On a Windows machine connected to the network:

1. Open Command Prompt
2. Run: ipconfig

Look for:

- **Default Gateway** -> This is your router’s IP address
- **Subnet Mask** -> Defines the network size
- **IPv4 Address** -> Current device IP

In my environment:

- Default Gateway: 192.168.68.1
- Subnet Mask: 255.255.255.0

---

### DHCP Pool Range

Before assigning IP addresses to the servers, we first need to identify the DHCP pool range to ensure we select static addresses outside of it. This information can be found by accessing your gateway router’s web interface and locating the "DHCP Settings" usually found within the "Advanced Settings" tab. In my case using my router's mobile app was sufficent.

```
DHCP Pool Range: 192.168.68.100 – 192.168.68.250

Available Static Range: 192.168.68.2 – 192.168.68.99
```

---

## Firewall Configuration

### Overview

Firewall rules changed to allow necessary traffic between the Domain Controller, File Server, Ubuntu Server, and client machines.

By default, Windows Defender Firewall blocked certain inbound traffic. To ensure proper communication for services such as Active Directory, DNS, DHCP, file sharing, and remote management, specific rules were enabled.

---

### Steps to Modify Windows Firewall Rules

Changed were made to WS2019-DC01

1. Open **Windows Defender Firewall**
2. On the left hand side click **Advanced settings**
3. Click **Inbound Rules**
4. Locate the service **File and Printer Sharing (Echo Request - ICMPv4-In)**
5. Right-click -> **Enable Rule**

---

## Configuring a Static IP Addresses

### Windows Server (WS2019-DC01)

once steps above are completed we can now assign static IP's to our Windows Servers starting with the domain controller.

1. Open **Control Panel**
2. Navigate to:
   - Network and Internet  
   - Network and Sharing Center  
3. Click **Change adapter settings**
4. Right-click the active network adapter -> Select **Properties**
5. Select **Internet Protocol Version 4 (TCP/IPv4)** -> Click **Properties**
6. Select **Use the following IP address**
7. Enter the following:

   - IP Address: 192.168.68.10  
   - Subnet Mask: 255.255.255.0  
   - Default Gateway: 192.168.68.1  

8. Under **Use the following DNS server addresses**, enter:

   - Preferred DNS Server: 192.168.68.10  

9. Click **OK**

**Note:** it is improtant to select an IP address outside of the DHCP pool range.

---

### Windows Server (WS2019-FS01)

Follow the same steps outlined above for configuring a static IP address.

However, assign a different IP address within the available static range (outside of the DHCP pool) to avoid conflicts.

Ensure:
- The subnet mask matches the network
- The default gateway remains the router’s IP
- The DNS server points to the Domain Controller

**Note:** In an Active Directory environment, the Domain Controller also functions as the DNS server. Active Directory relies on DNS to locate domain services such as authentication, LDAP, and other directory resources. For this reason, all domain-joined servers and clients must use the Domain Controller as their primary DNS server.

---

### Ubuntu Server

1. Check the netplan file with ``` ls /etc/netplan ```
2. Edit that file with ``` sudo nano /etc/netplan/50-cloud-init.yaml ``` (Replace 50-cloud-init.yaml with file name)
3. Modify configuration file with the following 

	```
	
	network:
  version: 2
  renderer: networkd
  ethernets:
    ens160:
      dhcp4: false
      addresses:
        - 192.168.68.30/24
      routes:
        - to: default
          via: 192.168.68.1
      nameservers:
        addresses:
          - 192.168.68.10
		  
	```
	
4. Save with CTRL+O, Enter, CTRL+X
5. Enter ``` sudo netplan try ```
6. If no error, press ENTER to confirm

**Note:** The ` /etc/netplan/ ` directory in Ubuntu stores network configuration files written in YAML format. Netplan is the network management utility used in modern Ubuntu Server versions to define IP addressing, gateways, DNS servers, and interface settings.
**Note:** The ` sudo netplan try ` command temporarily applies new network configuration changes and allows you to test them before making them permanent.

---

## Network Connectivity Testing

After configuring static IP addresses and DNS settings on all servers, connectivity must be tested to ensure proper communication across the network.

The same validation process was performed on each machine.

### Tests Performed

1. Ping Default Gateway (Verifies basic network connectivity to the router.)
		
	``` ping 192.168.68.1 ```
		
2. Ping Domain Controller (Confirms server-to-server communication and DNS availability.)

	``` ping 192.168.68.10 ```
		 
4. External Connectivity Test (Optional)
	
	``` ping 8.8.8.8 ```
		
All machines should successfully respond to tests



