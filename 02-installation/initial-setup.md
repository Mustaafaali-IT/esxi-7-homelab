# Initial ESXi Host Configuration

This document outlines the initial configuration steps performed after completing the ESXi installation.

---

## 🖥️ ESXi Version Verification

- VMware ESXi 7.0.3
- Release Build: 20356533
- Running in Evaluation Mode

Version confirmed from ESXi console and Host Client interface.

---

## 🌐 Network Verification

After installation:

- Physical wired network connection confirmed
- Management Network (vmk0) initialized successfully
- DHCP-assigned IPv4 address obtained
- Default gateway assigned via router
- Host reachable from local network

---

## 🔐 Web Interface Access

- Accessed ESXi Host Client via HTTPS: https://<ESXi-IP>
- Logged in as `root`
- Verified host summary information
- Confirmed hardware detection and storage visibility

---

## 🌍 Static Management IP Configuration

To ensure consistent access:

- Edited VMkernel NIC (vmk0)
- Switched from DHCP to Static IPv4
- Configured:
- IP Address (Lab Static)
- Subnet Mask
- Default Gateway
- DNS Server

After applying static configuration:
- Reconnected to ESXi Host Client using new IP
- Verified persistent network connectivity

---

## 📦 Storage Verification

- Confirmed 500GB HDD hosting ESXi system partitions
- Confirmed 256GB SSD visible as storage device
- Datastore prepared for VM deployment

---

## ✅ Host Ready Status

At completion of initial setup:

- ESXi boot device confirmed
- Management network stable
- Datastore prepared
- SSH enabled for advanced troubleshooting (if required)

Host is now ready for virtual machine deployment.

---

##Updates 

