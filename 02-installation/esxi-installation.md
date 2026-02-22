# ESXi 7 Installation

This document outlines the installation of VMware ESXi 7 on bare metal hardware.

---

## 📦 Version Installed

- VMware ESXi 7.0.3
- Release Build: 20356533
- Installed in Evaluation Mode (90-day trial)

---

## 💿 Installation Architecture

Final installation design:

| Device | Purpose |
|--------|----------|
| 500GB Seagate HDD | ESXi Hypervisor Installation |
| 256GB Samsung 850 EVO SSD | VM Datastore (VMFS6) |
| 16GB USB | Temporary Installer Media |

The hypervisor is isolated on a dedicated HDD while VM workloads run on SSD storage.

---

## 🛠 Installation Process

1. Created bootable ESXi installer USB using Rufus.
2. Partition Scheme: GPT
3. Target System: UEFI
4. Booted host from installer USB.
5. Selected 500GB HDD as ESXi installation target.
6. Configured secure root password.
7. Completed installation.
8. Removed installer USB.
9. Verified ESXi boots from HDD.
10. Connected SSD and created new VMFS6 datastore.

---

## 💾 Storage Configuration After Installation

- 500GB HDD contains ESXi system partitions only.
- 256GB SSD was fully wiped using Windows `diskpart clean`.
- SSD reformatted as VMFS6 datastore.
- Full usable capacity: ~232 GiB available for virtual machines.

---

## 🌐 Post-Install Status

- ESXi console successfully loaded.
- Management IP obtained via DHCP.
- Web interface accessible via HTTPS.
- Host ready for datastore configuration and VM deployment.

---

## ⚠️ Issues Encountered During Setup

- Initial dual-install confusion between USB and SSD.
- ESXi system partitions prevented SSD wipe.
- Resolved by:
  - Physically removing SSD
  - Cleaning SSD via Windows DiskPart
  - Reinstalling ESXi cleanly
- Confirmed final boot device before proceeding.

---

## 📚 Lessons Learned

- Always isolate installation target during ESXi setup.
- Avoid installing ESXi on the same disk intended for VM storage.
- Confirm boot device using: ```esxcli system boot device get ```
- If VMFS volumes appear locked, check for mounted system partitions.
- Cleaning disks externally (DiskPart) can resolve ESXi locking issues.

This installation establishes a clean foundation for enterprise-style virtual infrastructure deployment.