# Server Specifications

**Last Updated:** November 22, 2025
**Status:** Finished - Active
**Hostname:** pve (default)
**Primary IP:** 192.168.4.101/22

---

## Hardware Overview

This document provides detailed specifications for the primary Proxmox server running my Homelab's infrastructure.

### Physical Server Details

**Form Factor:** Desktop/ATX Mid-Tower 
**Manufacture/Model:** Custom Build
**Purchase/Build Date:** November 22, 2025
**Warrenty Status:** N/A, Custom build. Most individual parts are out of warranty

---

## Component Specifications

### Processor / CPU

- **Model/SKU:** Intel Core i7-10700F
- **Architecture:** Comet Lake (10th Gen)
- **Cores:** 8 Physical Cores
- **Threads:** 16 Threads (Hyper-Threading enabled)
- **Base Clock:** 2.9GHz
- **Boost Clock:** up to 4.8Ghz
- **Cache:** 16MB Intel Smart Cache
- **TDP:** 65W
- **Socket:** LGA1200
- **Virtualization Support:** VT-x (hardware virtualization), VT-d (directed I/O) - Enabled in BIOS

### Memory / RAM

- **Capacity:** 16GB
- **Type:** DDR4
- **Speed:** JEDEC 2666MHz, XMP to 2933MHz
- **Configuration:** Dual-Channel (2 x 8GB)
- **DPC/DIMMs Per Channel:** 1DPC
- **ECC Support:** No
- **Manufacture/Model:** Corsair Vengeance LPX - Model No. CMK16GX4M2B3000C15
- **Upgrade Path:** Up to 128GB (4 Slots on Motherboard)

### Motherboard

- **Chipset:** Intel Z490
- **Manufacturer/Model:** MSI Z490-A Pro - Model No. 7C75-003R
- **Socket:** LGA1200
- **Form Factor:** ATX
- **BIOS/UEFI Version:** TBD
- **BIOS Date:** TBD

***Key Features***
- **PCIe Slots:** 2 x16 slots, 3 x1 slots - All PCIe 3.0
- **M.2 Slots:** 1 m.2-22110 slot, 1 m.2-2280 - Both PCIe 3.0 x4
- **SATA Ports:** 6 SATA III (6Gb/s) Ports
- **USB Ports:**
   - 2 USB 3.2 Gen 2 10Gbps ports (1 Type A + 1 Type C on rear I/O)
   - 7 USB 3.2 Gen 1 5Gbps ports (2 Type-A ports on rear I/O, 4 ports + 1 Type-C through internal connectors)
   - 6 USB 2.0 ports (2 Type-A ports on rear I/O, 4 ports through internal connectors)
- **Network Interface:** 2.5 Gigabit Ethernet - Model No. RTL8125B-CG

### Storage

### Primary Storage

- **Drive 1 (boot/VM Storage | local-lvm/local):**
   - **Type:** 2.5" SATA III SSD
   - **Capacity:** 240GB
   - **Interface:** SATA III (6Gb/s)
   - **Manufacturer/Model:** Intel
   - **Usage:** Containers and Disk Images (LVM-thin) - Templates and ISO's (LVM)
   - **File System:** ext4
- **Drive 2 (backup-primary):**
   - **Type:** 3.5: SATA III HDD
   - **Capacity:** 1TB/1000GB
   - **RPM:** 7200 RPM
   - **Interface:** SATA III (6Gb/s)
   - **Manufacturer/Model:** Western Digital
   - **Usage:** Primary storage for VM backups
   - **File System:** ext4
- **Drive 3 (backup-secondary):**
   - **Type:** 2.5" SATA III HDD
   - **Capacity:** 500GB
   - **RPM:** 5400
   - **Interface:** SATA III (6Gb/s)
   - **Manufacture/Model:** Western Digital
   - **Usage:** Secondary drive for backup storage
   - **File System:** ext4

***Storage Notes:***
- See [backup-strategies.md](/proxmox/backup-strategies.md) for info on backup schedule and drive setup

---

## Network Configuration

## Network Interfaces

###Primary NIC:

- **Interface:** eth0
- **Type:** Onboard 2.5 Gigabit Ethernet
- **Chipset:** Realtek Model RTL8125B-CG
- **Speed:** 10/100/1000/2500Mbps
- **Link Status:** Full Duplex

### WAN Connection:

- **ISP Speed:** 500Mbps
- **Connection Type:** Cable (DOCSIS 3.1)

## IP Configuration

### Managment Interface:

- **IP Address:** 192.168.4.101/22
- **Subnet Mask:** 255.255.255.0
- **Gateway:** 192.168.4.1
- **DNS Servers:** Primary - 192.168.4.1

### VLAN Configuration:

- VLAN's are virtual (and to be added during Project 3 - planned for Jan. 2026)
- Current setup: Single flat network

---

## Power and Environmental

### Power Specifications:

- **Power Supply:**
- **Avg Power Draw:** Estimated @ ~80W
- **Idle Power:** To be calculated later
- **Load Power:** To be calculated later
- **UPS Protection:** None

### Cooling and Thermals:

- **CPU Cooler:** Intel stock LGA1200/1151 cooler (Model No. E97379-003)
- **Case Fans:** 2x total, 1x intake + 1x Exhaust
- **Ambient Temperature:** ~70F / 21C
- **Idle Temperature:** 
- **Load Temperature:** 

### Physical Environment:

- **Location:** Master Bedroom (Located with the rest of my network hardware)
- **Rack Mounted:** No, (Desktop configuration)
- **Dust Management:** Front panel and PSU Mesh screens, elevated from floor
- **Ventilation:** Adequate, case is in open air

## Software and Firmware

### Hypervisor:

- **Platform:** Proxmox Virtual Environment (PVE)
- **Version:** 9.1-1
- **Kernal Version:** 6.x (major version only for security purposes)
- **Install Date:** November 22, 2025
- **Web Interface:** https://192.168.4.101:8006

### BIOS/UEFI:

- **Version:**
- **Date:**
- **Last Updated:**

### Critical BIOS Settings:

- VT-x (Intel hardware virtualization technology): Enabled
- VT-d (Intel Directed I/O / IOMMU technology): Enabled
- Secure Boot: Disabled (For Proxmox compatibility)
- Boot Mode: UEFI
- VGA Detection: Disabled (headless boot)

---

## Resource Allocation

### Current VM/Container Allocation

*As of November 2025 - Early setup phase*

| VM/CT ID | Name | OS | vCPUs | RAM | Storage | Purpose | Status |
|:--------:|:----:|:--:|:-----:|:---:|:-------:|:-------:|:------:|
| [id num] | [name] | [os] | [0] | [0GB] | [0GB] | [service] | [N/A] |

### Total Allocated:

- **vCPUs:** 0 of 16 threads
- **RAM:** 0GB of 16GB
- **Storage:** 0GB of 240GB

### Available Resources:

- **vCPUs Available:** 16 threads
- **RAM Available:** 16GB
- **Disk Space Available:** 240GB

---

## Additional Information - Known Limitations and Considerations

### Current Constraints:

1. **RAM Capacity:** 16GB limits the number of concurrent VMs
    - **Impact:** Lighter VMs required, may need lighter Linux distros to save on resources
    - **Mitigation:** Use lighter distros, monitor resource usage, allocate smartly

2. **Single NIC:** No network redundancy
    - **Impact:** Single point of failure for network connectivity
    - **Mitigation:** Acceptable risk for homelab usecase -> failure is unlikely, and the majority of planned services are non-vital and therefore potential downtime is more acceptable if replacement NIC has to be purchased.

3. **Non ECC RAM:** No error correction for system memory
    - **Impact:** Increased (though still minimal) risk of memory errors
    - **Mitigation:** Regular backups of VMs saved to multiple locations (server drive, personal drive, potentially cloud sync), acceptable risk for homelab usecase with those precautions.
  
### Future Upgrade Paths:

1. RAM Expansion *(Medium Priority)*
    - 16GB -> 32GB+
    - Potentially faster, likely around 3600MHz max for stability purposes
    - Will allow for VMs with more intensive workloads
    - Budget Estimate: $120-$180
