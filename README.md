# GS_Homelab_v1
> Documentation of my ProxMox-based homelab setup
>
> **Status:** In Progress (Month 1 of 9)  
> **Last Updated:** November 2025 (initial commit)

---

## PROJECT GOALS:
- **Primary:**
   - Build Networking and IT skills by creating, administrating, and maintaining a personal homelab during last months of my senior year (graduating Aug. 2026)

- **Secondary:**
   - Add functionality to lab to improve home network security, ease-of-use, and performance
 
**Key Focus Areas:**
- Network architecture & routing
- Virtualization (Proxmox)
- Security & firewalls
- Monitoring & troubleshooting
- Professional documentation

---

## LAB INFRASTRUCTURE:

**Hardware:**
- **CPU:** intel Core i7-10700F (8C/16T)
- **RAM:** 16GB DDR4 @ 3200Mhz (Dual-Channel, 1DPC)
- **MoBo:** Z490 Chipset
- **Storage:** 500GB Sata III HDD
- **Net Speed:** 500 mbps
- **Power Draw:** <80W Average

**Software Stack:**
- **Hypervisor:** ProxMox VE v9.0-1
- **Management:** ProxMox Web Interface *(IP: 192.168.4.10:8006)*
- **Operating Systems Used:** Ubuntu Server v24.04.3

[Detailed Hardware Specifications](hardware/server-specs.md) - [ProxMoxVE Internal Network Configuration](proxmox/net-config.md)

---

## PROJECT TIMELINE:

### **COMPLETED PROJECTS**

####*n/a | TBA*

### **IN PROGRESS**

#### HL Server Build | ProxMoxVE Installation and Setup
- [Build Log](hardware/build-log.md) - [PVE Installation](proxmox/installation.md)
- *IN PROGRESS - Nov. 2025*

### **PLANNED PROJECTS**

- **Project no. 1:** Multi-VM Setup and Initial Routing Experiments - *Planned: Nov. 2025*
- **Project no. 2:** Network DNS Server and Ad-blocking with Pi-Hole - *Planned: Dec. 2025*
- **Project no. 3:** VLAN Setup (Virtual) - *Planned: Jan. 2026*
- **Project no. 4:** Reverse Proxy (Nginx Proxy Manager) - *Planned: Jan. 2026*
- **Project no. 5:** VPN Server (WireGuard) - *Planned: Feb. 2026*
- **Project no. 6:** Firewall Configuration (pfSense) - *Planned: Mar. 2026*
- **Project no. 7:** Network Monitoring (Prometheus + Grafana) - *Planned: Apr. 2026*
- **Project no. 8:** DHCP Server with Dynamic DNS - *Planned: May 2026*
- **Project no. 9:** Site-to-Site VPN - *Planned: Jun. 2026*
- **Project no. 10:** Documentation Polishing & Network Diagrams - *Planned: Jul. 2026*

---

## REPOSITORY STRUCTURE:

```
GS_Homelab/
├── README.md                    ← You are here
├── hardware/
│   ├── server-specs.md         Hardware documentation
│   └── build-log.md            Build process notes
├── proxmox/
│   ├── installation.md         PVE setup guide
│   └── net-config.md           Network configuration
├── projects/
│   └── [Future project docs]
└── troubleshooting/
    └── [Issues & solutions]
```

---

## WHY THIS PROJECT?

As an IT student expecting to graduate in summer 2026, I'm looking for demonstrable technical skills, putting my practical knowledge from my coursework into something real. This Homelab is designed to provide:
- Real-world infrastructure management experience
- Troubleshooting skills from actual networking issues
- Foundational practical knowledge for my future career in the IT and networking fields

---

## LICENSE AND USAGE:

This documentation is a personal project and learning experience built for me, specifically. However, feel free to utilize this repo as a reference if you wish to build your own homelab.

---

**Contact Info:** If you need to get in touch with me, feel free to email me at [scottgannon2003@gmail.com](mailto:scottgannon2003@gmail.com)
