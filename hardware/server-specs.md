# Server Specifications

**Last Updated:** November 19, 2025
**Status:** In Production
**Hostname:** gs_HomeLab_v1
**Primary IP:** 192.168.4.10

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
- **Speed:** JEDEC 2666MHz, XMP to 3200MHz
- **Configuration:** Dual-Channel (2 x 8GB)
- **DPC/DIMMs Per Channel:** 1DPC
- **ECC Support:** No
- **Manufacture/Model:** Corsair Vengeance LPX - Model No. CMK16GX4M2B3200C16
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

- **Drive 1 (Boot/Primary):**
  - **Type:** 2.5" SATA III HDD
  - **Capacity:** 500GB
  - **RPM:** 5400
  - **Interface:** SATA III (6Gb/s)
  - **Manufacture/Model:** Western Digital - Model No. WD5000LPCX-xxxxxxx
  - **Usage:** Proxmox VE OS + VM Storage
  - **File System:** ext4

***Storage Notes:***
- Consider upgradeing to SSD--NVMe preferred, SATA III at least--to improve VM performance
- Current setup should be suitable for my first few planned projects.
- Backup Strategy: Create and export backups to main PC's high capacity reserve drive (2TB 7200RPM Seagate Barracuda Compute HDD, mostly unused currently besides some game saves from games I havent played in a while). Should prove sufficient while I learn more about data redundancy and automation of these tasks. See [backup-strategies.md](/proxmox/backup-strategies.md) for more info.

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

- **IP Address:** 192.168.4.10/24
- **Subnet Mask:** 255.255.255.0
- **Gateway:** 192.168.4.71
- **DNS Servers:** Primary - 97.64.168.10 | Secondary - 97.64.168.11

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
- **Version:** 9.0-1
- **Kernal Version:** 6.x (major version only for security purposes)
- **Install Date:** November 2025
- **Web Interface:** https://192.168.4.10:8006

### BIOS/UEFI:

- **Version:**
- **Date:**
- **Last Updated:**

### Critical BIOS Settings:

- VT-x (Intel hardware virtualization technology): Enabled
- VT-d (Intel Directed I/O / IOMMU technology): Enabled
- Secure Boot: Disabled (For Proxmox compatibility)
- Boot Mode: UEFI/Legacy

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
- **Storage:** 0GB of 500GB

### Available Resources:

- **vCPUs Available:** 16 threads
- **RAM Available:** 16GB
- **Disk Space Available:** 500GB

---

## Additional Information - Known Limitations and Considerations

### Current Constraints:

1. **Storage Issues**
    - **Speed:** 5400 RPM HDD limits VM performance, boot times, app loading, etc
       - **Mitigation:** Planned SSD upgrade once budget improves
    - **Health:** Used disk from old business laptop, unknown amount of power-on-hours
       - **Mitigation:** Backup strategy and regular SMART testing to ensure drive health is properly monitored

2. **RAM Capacity:** 16GB limits the number of concurrent VMs
    - **Impact:** Lighter VMs required, may need lighter Linux distros to save on resources
    - **Mitigation:** Use lighter distros, monitor resource usage, allocate smartly

4. **Single NIC:** No network redundancy
    - **Impact:** Single point of failure for network connectivity
    - **Mitigation:** Acceptable risk for homelab usecase -> failure is unlikely, and the majority of planned services are non-vital and therefore potential downtime is more acceptable if replacement NIC has to be purchased.

5. **Non ECC RAM:** No error correction for system memory
    - **Impact:** Increased (though still minimal) risk of memory errors
    - **Mitigation:** Regular backups of VMs saved to multiple locations (server drive, personal drive, potentially cloud sync), acceptable risk for homelab usecase with those precautions.
  
### Future Upgrade Paths:

1. Boot SSD *(Highest Priority)*
    - NVMe or SATA SSD for VM performance gains
    - Ideally 250-500GB
    - Move current drive to store backups only
        - less critical task
        - local backup storage only for redundancy
    - Budget Estimate: ~$50-$100

2. RAM Expansion *(Medium Priority)*
    - 16GB -> 32GB+
    - Potentially faster, likely around 3600MHz max for stability purposes
    - Will allow for VMs with more intensive workloads
    - Budget Estimate: $120-$180

3. Longer-term Considerations *(Low Priority)*
    - Dedicated Mass Storage (High-Capacity HDD for monitoring software logs, full system backups, etc.)
    - Case/Ventilation upgrade -> more fans and/or a more open case for better airflow
    - Aftermarket CPU cooler for lower thermals -> higher clock speeds for vCPUs
    - UPS for protection against power interruptions
