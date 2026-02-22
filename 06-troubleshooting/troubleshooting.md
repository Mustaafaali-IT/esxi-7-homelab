# Troubleshooting Log – ESXi 7 Homelab

This document records major issues encountered during the ESXi deployment process and how they were resolved.

---

## USB Keyboard Not Working During Installation

### Issue
During ESXi 6.5 installation, USB keyboard input stopped working at the installer screen.

### Root Cause
Older ESXi versions lacked proper USB controller driver support for the motherboard.

### Resolution
- Upgraded installer to ESXi 7.0.3
- Confirmed Legacy USB Support enabled in BIOS
- Verified UEFI boot mode

### Lesson Learned
Always use a supported ESXi version for modern hardware.

---

## Dual ESXi Install Confusion (USB vs SSD)

### Issue
Originally tried to use usb as boot device and SSD as storage for temp storage solution. 
System appeared to boot from USB, but ESXi was actually loading from SSD.

Symptoms:
- LOCKER volume mounted
- Disk marked as “busy”
- Unable to wipe partitions

### Root Cause
Multiple ESXi installations on separate disks caused boot ambiguity.

### Resolution
- Physically removed SSD
- Verified boot device using: ```esxcli system boot device get```

- Cleaned SSD using Windows DiskPart (`clean`)
- Reinstalled ESXi cleanly with isolated target disk

### Lesson Learned
Always isolate installation disk during ESXi setup to avoid boot conflicts.

---

## Unable to Wipe Datastore (Device Busy)

### Issue
Attempting to wipe SSD via ESXi failed with “device busy” errors.

### Root Cause
ESXi system partitions were mounted and in use.

### Resolution
- Confirmed active boot disk
- Unmounted mounted filesystems
- Ultimately wiped disk externally using Windows DiskPart

### Lesson Learned
If VMFS volumes are locked, verify boot device before modifying partitions.

---
