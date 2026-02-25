# Phase 1 — ESXi Install + Core VM Deployment

## Goal
Install VMware ESXi 7.0U3 on bare metal hardware, configure foundational host networking and storage, and deploy the required virtual machines (2x Windows Server 2019, 1x Ubuntu Server LTS).

This phase establishes the virtualization foundation that all later infrastructure services will depend on.

---

## What was built
- ESXi 7.0U3 installed on dedicated Seagate HDD
- Static management IP configured on ESXi host (vmk0)
- Intel PRO/1000 GT NIC selected as primary adapter
- Samsung EVO SATA SSD configured as primary VM datastore (`datastore-ssd01`)
- Deployed and installed:
  - Windows Server 2019 VM (WS2019-DC01)
  - Windows Server 2019 VM (WS2019-FS01)
  - Ubuntu Server 24.04 LTS VM (Ubuntu-SRV01)(OpenSSH enabled)

---

## Architecture Snapshot

### Host
- Hypervisor: VMware ESXi 7.0U3
- Boot disk: 500GB Seagate HDD (ESXi installation)
- VM datastore: 256GB Samsung EVO SSD (`datastore-ssd01`, VMFS6)
- Primary NIC: Intel PRO/1000 GT Desktop Adapter
- Onboard Realtek NIC: Disabled in BIOS

---

### VMs

| VM Name         | OS                     | vCPU | RAM  | Disk | Provisioning | Notes |
|----------------|-------------------------|------|------|------|--------------|------|
| WS2019-DC01     | Windows Server 2019     | 2    | 6GB  | 40GB | Thick        | DC/DNS |
| WS2019-FS01    | Windows Server 2019     | 2    | 6GB  | 40GB | Thin         | File/DHCP |
| Ubuntu-SRV01    | Ubuntu Server LTS       | 2    | 4GB  | 30GB | Thin         | OpenSSH enabled |

**Note:** WS2019-DC01 was provisioned as Thick by default. This does not negatively impact lab functionality and was intentionally left unchanged.

---

# Detailed Implementation

---

## 1) ESXi Installation

- Booted from ESXi 7.0U3 installer USB
- Selected Seagate HDD as installation target
- Confirmed installation completed successfully
- Removed installer USB
- Verified host boots directly into ESXi

**Concept:**  
ESXi is a Type-1 (bare metal) hypervisor. It installs directly onto hardware and abstracts CPU, memory, storage, and networking for virtual machines.

Separating ESXi (HDD) from VM storage (SSD) provides:
- Clean role separation
- Easier recovery
- Better performance for workloads

---

## 2) ESXi Initial Networking Configuration

Upon first boot, ESXi configures a default management network.

### Key Components Reviewed:

### vmk0 (VMkernel Adapter)

- vmk0 is the default ESXi management interface.
- It is responsible for:
  - Host management access (Web UI / SSH)
  - Host-level services
  - Potential vMotion and other advanced services in future phases

I configured:
- Static IP address
- Subnet mask
- Default gateway
- DNS servers

**Concept:**  
A VMkernel adapter (vmkX) is not a normal VM network card.  
It is used by the ESXi host itself to communicate.

vmk0 is critical because:
If its IP changes or fails, management access is lost.

---

### vSwitch0

ESXi automatically creates:

- Standard Virtual Switch: `vSwitch0`
- Port Group: `Management Network`
- VMkernel adapter: `vmk0` attached to that port group

Structure looks like:

Physical NIC (Intel PRO/1000 GT) -> vSwitch0 -> Management Network Port Group -> vmk0 (Management IP)

**Concept:**  
A vSwitch is a virtual Layer 2 switch inside ESXi.

It connects:
- Physical NICs
- VM network adapters
- VMkernel adapters

Port groups act like VLAN segments or logical switch ports.

---

### Why Static IP Was Assigned

The ESXi host must always be reachable.

Using DHCP for a hypervisor risks:
- IP address changing
- Losing access to management interface
- Breaking automation or DNS records

Static IP ensures stable infrastructure foundation.

---

## 3) Storage Configuration — Datastore Creation

Confirmed:
- Samsung EVO SSD detected in BIOS
- SATA mode set to AHCI
- SSD visible in ESXi under Storage → Devices

Created:

- VMFS6 datastore
- Name: `datastore-ssd01`
- Used full disk capacity

**Concept:**  
A datastore is a logical container for VM files.

Each VM consists of:
- .vmx (configuration file)
- .vmdk (virtual disk file)
- Log files
- Snapshots (if used)

Using SSD improves:
- I/O latency
- Boot speed
- Overall VM responsiveness

---

## 4) ISO Management

Uploaded installation media to:

`datastore-ssd01/ISOs/`

- Windows Server 2019 ISO
- Ubuntu Server LTS ISO

Storing ISOs inside datastore ensures:
- Centralized installation source
- No dependency on local machine during VM creation

---

## 5) VM Creation — Detailed Breakdown

Each VM was created manually via:

Virtual Machines -> Create / Register VM -> Create new virtual machine

### Configuration Decisions Explained

---

### CPU Allocation (2 vCPU)

Each server allocated:
- 2 virtual CPUs

Reason:
- Lightweight lab services
- Prevent CPU overcommitment
- Allow room for additional future VMs

**Concept:**  
vCPU represents a scheduling share of physical CPU cores.

ESXi maps vCPUs to physical cores dynamically.

Over-allocating CPU unnecessarily can reduce performance due to scheduling overhead.

---

### Memory Allocation

- Windows Servers: 6GB
- Ubuntu Server: 4GB

Reason:
- AD + DNS require moderate RAM
- File/DHCP services lightweight
- Ubuntu Apache server lightweight
- Host has 32GB total (Safe Margin)

**Concept:**  
Memory is reserved at runtime.
Over-allocating memory can reduce host flexibility.

Balanced allocation prevents resource starvation.

---

### Disk Provisioning Types

Two provisioning types were used:

#### Thick Provisioned (WS2019-DC01)
- Full 40GB allocated immediately

#### Thin Provisioned (WS2019-SRV02, Ubuntu)
- Disk grows as data is written

**Concept:**  
Thin provisioning conserves datastore space.
Thick provisioning reserves full capacity up front.

In labs, thin is typically sufficient.

---

### Network Adapter

Each VM attached to:

- Default Port Group: `VM Network`

This connects to:
- vSwitch0
- Physical Intel NIC

All VMs currently reside in same broadcast domain (flat network).

Network segmentation (VLANs) may be introduced in later phases.

---

## 6) OS Installation

### Windows Servers
- Installed Windows Server 2019 Standard (Desktop Experience)
- Verified login
- No roles configured yet

### Ubuntu Server
- Installed Ubuntu Server LTS
- Enabled OpenSSH during setup
- Resolved ISO detach issue after installation

---

# Validation

- ESXi management reachable via static IP
- vmk0 correctly bound to Management Network
- vSwitch0 functioning
- Datastore `datastore-ssd01` accessible
- All VMs:
  - Power on successfully
  - Boot into OS
  - Login verified

---

# Issues Encountered

### SSD Not Detected
Cause:
- SATA power cable disconnected

Resolution:
- Reseated cable
- SSD detected in BIOS and ESXi

---

### Ubuntu Installer Loop
Cause:
- ISO still mounted to virtual CD/DVD

Resolution:
- Changed CD/DVD from datastore ISO to Host/Client device
- Rebooted successfully

---

# Phase 1 Outcome

At the conclusion of Phase 1:

- Hypervisor layer is stable
- Networking foundation established
- Storage architecture defined
- Core server OS deployments complete
- Infrastructure ready for Phase 2 (network design + services)

---

# Next Phase Preview

Phase 2 will introduce:

- Static IP planning and assignment
- Active Directory + DNS deployment
- DHCP configuration
- File Services configuration
- Apache installation on Ubuntu
- Domain integration testing
- Windows 11 client deployment
- Web service an print service configuration

Phase 1 establishes the foundation.
Phase 2 builds identity and infrastructure services on top of it.

[Click to see Phase 2 Documentations](02-network-and-services/README.md)