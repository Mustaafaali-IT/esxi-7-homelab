# vSwitch Configuration

This document outlines the default virtual networking configuration of the ESXi host.

---

## 🌐 Default Virtual Switch

### vSwitch0

- Default virtual switch created during ESXi installation
- Backed by one physical uplink
- Uplink: `vmnic0` (Intel NIC)
- Used for:
  - Management Network
  - Virtual machine network traffic (initial phase)

No custom vSwitches created at this stage.

---

## 🔌 Physical Network Adapter

### vmnic0

- Connected to physical LAN
- Wired Ethernet connection
- Link status confirmed as "Up"
- Provides external network access to host and VMs

---

## 🧠 VMkernel Interface

### vmk0 – Management Network

- Attached to: `vSwitch0`
- Port Group: Management Network
- IPv4: Static configuration
- Default Gateway: Configured
- DNS: Configured

vmk0 is responsible for:
- Host management access
- ESXi Host Client access
- SSH connectivity

---

## 🌍 Static IP Configuration

Management network was switched from DHCP to Static IPv4 to ensure consistent host access.

Configured:
- Static IP address
- Subnet mask
- Default gateway
- DNS server

After configuration:
- Reconnected to ESXi Host Client using new IP
- Verified stable connectivity

---

## 🔮 Future Networking Plans

Planned enhancements include:

- Creation of additional port groups
- Internal-only network for domain lab
- VLAN segmentation
- Dedicated network for server infrastructure
- Potential distributed switch (if vCenter deployed)

Current configuration provides a stable foundation for initial VM deployment.