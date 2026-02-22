# BIOS Configuration

Before installing ESXi, the BIOS was configured to ensure compatibility and proper virtualization support.

## Key BIOS Settings

### Virtualization
- Intel VT-x: Enabled

### USB Configuration
- Legacy USB Support: Enabled
- USB Keyboard/Mouse Support: Enabled

### Boot Settings
- Boot Mode: UEFI
- Boot Priority set to USB installer for initial installation

### Onboard Devices

- Intel NIC planned for ESXi compatibility (Intel Gigabit CT Desktop Adapter)

---

## Notes

- USB keyboard initially failed during ESXi 6.5 installation.
- Issue resolved by switching to ESXi 7.0.3 installer.
- USB compatibility confirmed in BIOS.

---

## Lessons Learned

Older ESXi versions may have USB driver compatibility issues.
Always verify:
- Virtualization enabled
- USB legacy support enabled
- Correct boot mode (UEFI)