# Disk Layout – ESXi 7 Host

This document outlines the current physical disk configuration and storage strategy for the ESXi homelab environment.

---

## 🖴 Physical Disk Configuration

### 1️⃣ ESXi Host Disk

Model: Seagate HDD  
Capacity: 500 GB  
Purpose: ESXi Hypervisor Installation  

- Contains ESXi system partitions only
- No VM datastore located on this disk
- Dedicated exclusively to hypervisor operation

---

### 2️⃣ VM Datastore Disk

Model: Samsung SSD 850 EVO 250GB  
Reported Capacity (ESXi): ~232 GiB usable  
Partition Format: VMFS6  

Purpose: Virtual Machine Storage  

- Fully wiped using Windows DiskPart (`clean`)
- Reformatted as VMFS6 datastore
- Used exclusively for:
  - Virtual disks (VMDKs)
  - ISO storage
  - VM configuration files

---

### 3️⃣ Personal / Windows Storage Disk

Model: 3TB HDD  
Purpose: Windows personal use (games / applications)  

- Not formatted as VMFS
- Not used by ESXi
- Physically disconnected when running ESXi to prevent accidental formatting

---

## 🧠 Storage Strategy

This lab machine operates in two modes:

### Server Mode (ESXi Active)
- 500GB HDD → Hypervisor
- 256GB SSD → VM Datastore
- Windows data drive unplugged

### Windows Mode (Personal Use)
- Boot Windows
- Reconnect 3TB HDD
- ESXi disks remain untouched

This separation prevents:
- Filesystem conflicts
- Accidental formatting
- Mixed workload performance issues

---

## 📊 Current Datastore Capacity

Available VM storage: ~232 GiB  

Initial VM deployment plan:

| VM | Estimated Disk (Thin Provisioned) |
|----|------------------------------------|
| Domain Controller | 40 GB |
| Windows 11 Client | 60 GB |
| Ubuntu Server | 25 GB |

Estimated initial real usage:
~60–80 GB depending on installation footprint

Storage is sufficient for Phase 1 lab deployment.

---

## 🔮 Future Expansion Plan

Planned upgrades:

- Add 512GB–1TB SSD for expanded VM capacity
- Separate infrastructure VMs from workload VMs
- Potential datastore segmentation
- Prepare for future vCenter deployment

The current storage design provides a clean, scalable foundation for enterprise-style lab growth.