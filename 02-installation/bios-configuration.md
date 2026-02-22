# BIOS Configuration

This document outlines the BIOS settings configured prior to installing VMware ESXi 7 on the host system.

Correct BIOS configuration is critical to ensure proper virtualization support, USB boot functionality, and hardware compatibility.

---

## 🔧 System Mode

### Boot Mode
- Boot Mode: **UEFI**
- CSM: Disabled (if available)

UEFI is required for modern ESXi installations and GPT partition support.

---

## 🧠 CPU Virtualization Settings

### Virtualization Technology
- Intel VT-x: **Enabled**

This allows ESXi to run virtual machines using hardware-assisted virtualization.

(Optional – if available)
- Intel VT-d: Enabled (for future device passthrough experimentation)

---

## 🔌 USB Configuration

Since ESXi is installed on a USB device, USB compatibility is critical.

- Legacy USB Support: Enabled
- USB Keyboard Support: Enabled
- USB Boot: Enabled

USB functionality was verified prior to installation.

---

## 🌐 Network Adapter

- Onboard Intel NIC enabled
- Intel Gigabit CT Desktop Adapter confirmed for ESXi compatibility

Intel NICs are preferred due to strong driver support in ESXi.

---

## 🗂️ Boot Order Strategy

During installation:
- USB installer set as first boot device

After installation:
- ESXi boot USB set as primary boot device
- All other disks placed below USB in boot priority

This ensures ESXi does not accidentally boot from another disk containing residual partitions.

---

## 📝 Issues Encountered

- USB keyboard initially failed with ESXi 6.5 installer
- Resolved by upgrading to ESXi 7.0.3 installer
- USB compatibility confirmed after BIOS review

---

## 📚 Lessons Learned

- Always verify virtualization is enabled before installation
- UEFI mode is required for modern ESXi builds
- USB legacy support can prevent input device issues
- Boot order must be explicitly verified when multiple storage devices are present
- Older ESXi versions may lack driver support for newer USB controllers

Proper BIOS preparation prevents storage conflicts and boot ambiguity during ESXi deployment.