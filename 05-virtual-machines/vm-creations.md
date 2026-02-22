# Virtual Machine Creation

This document outlines ISO preparation, VM deployment strategy, and resource allocation planning for the ESXi homelab.

---

## 📦 ISO Preparation

Downloaded:

- Windows Server 2019 Evaluation ISO
- Windows Server 2022 Evaluation ISO (for future deployment)
- Ubuntu Server 24.04 LTS ISO
- Windows 11 ISO (for client testing)

All ISO files will be uploaded to the ESXi datastore prior to VM creation.

ISO files are stored inside:
Datastore → /ISO/

---

## 🏗️ Deployment Strategy (Phased Build)

Virtual machines will be deployed incrementally to simulate a real-world infrastructure rollout.

### Phase 1 – Core Infrastructure
- 1x Domain Controller
- 1x Windows 11 Client
- 1x Ubuntu Server

### Phase 2 – Infrastructure Expansion
- File Server
- DHCP Server (if separated from DC)
- Exchange Server

---

## 🧠 Resource Allocation Planning

Current host capacity:
- 32 GB RAM
- 6 Physical CPU Cores
- 232 GiB usable VM storage

Initial VM allocation strategy:

| VM Role | vCPU | RAM | Disk (Thin Provisioned) |
|----------|------|------|--------------------------|
| Domain Controller | 2 | 6 GB | 40 GB |
| Windows 11 Client | 2 | 6 GB | 60 GB |
| Ubuntu Server | 2 | 4 GB | 25 GB |

RAM will be monitored to avoid excessive overcommitment.

---

## ⚙️ VM Creation Standards

All VMs will follow these standards:

- VM Compatibility: ESXi 7.x
- Firmware: EFI
- Disk Provisioning: Thin
- Network Adapter: VMXNET3
- Network: vSwitch0 (Management Network) initially
- VMware Tools installed after OS deployment

---

## 💾 Storage Considerations

- All VMs stored on SSD-backed VMFS6 datastore
- Thin provisioning used to conserve disk space
- Storage expansion planned if utilization exceeds 70%

---

## 🔮 Future Enhancements

- Convert base VMs into templates
- Snapshot before major configuration changes
- Introduce separate internal network for lab isolation
- Deploy vCenter for centralized management

This VM deployment process mirrors small business and entry-level enterprise infrastructure practices.

## Update — Core VM Base OS Deployment Complete (Milestone)

Successfully deployed and installed base operating systems for all planned core VMs:

- WS2019-DC01
  - 2 vCPU
  - 6GB RAM
  - 40GB disk (Thick provisioned)
  - Windows Server 2019 Standard (Desktop Experience)

- WS2019-SRV02
  - 2 vCPU
  - 6GB RAM
  - 40GB disk (Thin provisioned)
  - Windows Server 2019 Standard (Desktop Experience)

- Ubuntu-SRV01
  - 2 vCPU
  - 4GB RAM
  - 30GB disk (Thin provisioned)
  - Ubuntu Server LTS
  - OpenSSH Server installed

All VMs boot successfully and are accessible via ESXi console.
No roles or infrastructure services configured yet.
Awaiting final architecture confirmation before promoting roles.