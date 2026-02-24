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

## ESXi Web UI – Connection Refused After Moving Behind Switch

### Issue
After moving the ESXi host from a direct router connection to an unmanaged switch, the Web UI became inaccessible.

Symptoms:
- Ping to ESXi IP worked
- rhttpproxy service was running
- ESXi firewall disabled
- Browser error: ERR_CONNECTION_REFUSED
- vmk0 showed Enabled: true with correct static IP and gateway

Despite Layer 3 connectivity working (ping), HTTPS (port 443) connections were being refused.

---

### Root Cause
After the physical network topology changed (router → switch → router), the ESXi management network stack did not fully rebind to the new path.

Likely contributing factors:
- Stale ARP entries on router
- MAC address table refresh delay
- Management services not properly binding after static IP was previously configured

The management interface appeared healthy but was not properly accepting TCP 443 connections.

---

### Resolution
1. Changed management network from Static → DHCP
2. Confirmed Web UI was accessible via new DHCP IP
3. Reconfigured management network back to Static
4. Web UI began functioning normally

Switching to DHCP forced:
- Full network stack refresh
- ARP table renewal
- Proper service binding to vmk0

---

### Lesson Learned
If ESXi Web UI:
- Responds to ping
- rhttpproxy is running
- Firewall is not blocking
- But browser shows “connection refused”

Try restarting the management network or temporarily switching to DHCP to force a clean network rebind after physical network changes.