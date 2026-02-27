# BIOS Configuration — Phase 1

This document outlines the BIOS settings configured prior to installing VMware ESXi 7.0U3 on the host system.

Proper BIOS configuration ensures:
- Hardware virtualization support
- Storage compatibility
- Stable ESXi boot behavior
- Clean separation between host OS and VM storage

---

## Boot Configuration

### Boot Mode
- Boot Mode: **UEFI**
- CSM (Compatibility Support Module): Disabled

UEFI mode is required for modern ESXi installations and GPT partition support.

---

## CPU Virtualization Settings

### Intel Virtualization Technology
- Intel VT-x: **Enabled**

This enables hardware-assisted virtualization, allowing ESXi to run 64-bit virtual machines.

### Intel VT-d (if available)
- Enabled

VT-d allows future experimentation with PCI passthrough (e.g., dedicated NICs or storage controllers).

---

## Storage Configuration

### SATA Mode
- SATA Mode: **AHCI**
- RAID Mode: Disabled

ESXi requires AHCI mode for proper detection of local storage devices.

### Storage Layout Strategy

- **500GB Seagate HDD** → ESXi installation disk
- **256GB Samsung EVO SSD** → VM datastore (`datastore-ssd01`)

This separation ensures:
- ESXi host OS remains isolated
- VM workloads utilize SSD performance
- Storage roles are clearly defined

---

## Network Adapter Configuration

### Primary NIC
- Intel PRO/1000 GT Desktop Adapter: Enabled and used for ESXi management
- Confirmed compatible with VMware ESXi 7

Intel adapters are preferred in ESXi environments due to strong native driver support and long-term stability.

---

### Disabled NIC
- Onboard Realtek Adapter: Disabled in BIOS

Reason:
Realtek NICs often lack native ESXi driver support and may require community drivers. Disabling it ensures:
- Cleaner network configuration
- No driver ambiguity
- Stable management interface selection
- Reduced troubleshooting complexity

---

## Boot Order Strategy

### During Installation
- USB installer set as first boot device

### After Installation
- Seagate HDD (ESXi installation disk) set as primary boot device
- All other storage devices placed lower in boot priority

This prevents accidental boot attempts from non-ESXi disks.

---

## Issues Encountered

- Samsung EVO SSD initially not detected in ESXi
  - Root Cause: SATA power cable disconnected
  - Resolution: Reseated power cable; SSD detected in BIOS and ESXi

---

## Lessons Learned

- Always verify virtualization (VT-x) is enabled before installing ESXi
- AHCI mode is required for proper disk detection
- Boot order must be explicitly reviewed after installation
- Hardware issues (cabling) can mimic software detection failures
- Separate ESXi OS disk from VM datastore disk for clean infrastructure design

Proper BIOS preparation establishes a stable foundation for all virtualization workloads deployed in this lab.