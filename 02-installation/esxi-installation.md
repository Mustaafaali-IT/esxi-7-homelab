# ESXi 7 Installation

## Version Installed

- VMware ESXi 7.0.3
- Release Build: 20356533
- Installed in Evaluation Mode (90-day trial)

---

## Installation Process

1. Created bootable USB using Rufus.
2. Selected MBR partition scheme.
3. Booted from USB installer.
4. Selected Samsung SSD 850 (232.89 GiB) as installation target.
5. Created secure root password.
6. Completed installation successfully.
7. Removed USB installer and rebooted.

---

## Storage Layout

- ESXi installed directly on Samsung SSD.
- Datastore automatically created during installation.

---

## Post-Install Status

- ESXi console successfully loaded.
- Management IP obtained via DHCP.
- Ready for web interface configuration.

---

## Notes

- Initial attempt using ESXi 6.5 failed due to USB keyboard detection issues.
- ESXi 7 resolved compatibility problems.
- Installation completed without errors.