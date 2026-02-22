# Hardware Overview

This document outlines the physical host hardware used for the ESXi 7 homelab environment.

---

## 🖥️ Host Machine Specifications

### CPU
- Intel Core i5-8600K
- 6 Cores / 6 Threads
- 3.60 GHz Base Clock

### Memory
- 32 GB DDR4 RAM

### Storage Layout

| Device | Capacity | Purpose |
|--------|----------|----------|
| Seagate HDD | 500 GB | ESXi Hypervisor Installation |
| Samsung 850 EVO SSD | 256 GB (232 GiB usable) | VM Datastore (Primary VM Storage) |

Future expansion planned:
- Additional 512GB–1TB SSD for increased VM capacity and performance.

### Network
- Onboard NIC (Intel-based)
- Dedicated management network via vmk0
- Future VLAN segmentation planned

### Motherboard
- ASUS Z370 Chipset

---

## 📦 Storage Design Philosophy

The host separates the hypervisor from VM workloads:

- ESXi is installed on a dedicated HDD.
- All virtual machines run on SSD storage for improved performance.
- Storage expansion will be implemented as VM workload requirements increase.

This design mirrors small-business and entry-level enterprise infrastructure practices.

---

## 🧠 Resource Planning Notes

With 32GB RAM and 6 physical cores, this host is capable of running:

- 4–6 infrastructure VMs simultaneously
- A full Active Directory lab environment
- Mixed Windows and Linux workloads

Resource allocation will be carefully managed to simulate real-world infrastructure planning.

---

## 🔮 Upgrade Path

Planned upgrades include:

- Additional SSD storage
- Possible dedicated datastore separation
- Potential vCenter deployment
- Advanced networking segmentation

This hardware configuration provides a strong foundation for virtualization and enterprise lab development.