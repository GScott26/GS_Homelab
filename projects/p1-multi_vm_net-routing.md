# Project 1: Multi-VM Network Setup

**Completed:** Nov. 22, 2025  
**Time Spent:** ~5 Hrs
**Difficulty:** Easy  
**Status:** Complete

---

## Objective

Learn fundamental networking concepts by creating three virtual machines on isolated networks and configuring routing between them.

**Key Learning Goals:**
- Virtual network bridge creation
- Static IP configuration
- Linux routing fundamentals
- Network troubleshooting methodology

---

## Network Topology
```
                 PROXMOX HOST (192.168.4.101)
┌───────────────────────────────────────────────────────────────┐
│                                                               │
│  Physical Network: vmbr0 (192.168.4.0/22)                     │
│  └── Connection to home network/internet                      │
│                                                               │
│  ┌─────────────────────────────────────────────────────────┐  │
│  │                                                         │  │
│  │  Lab Network A: vmbr1 (10.10.10.0/24)                   │  │
│  │                                                         │  │
│  │         10.10.10.10                                     │  │
│  │      ┌──────────────┐                                   │  │
│  │      │  test-vm1    │                                   │  │
│  │      │  (ID: 101)   │                                   │  │
│  │      │  Ubuntu 24.04│                                   │  │
│  │      │  1GB RAM     │                                   │  │
│  │      └──────┬───────┘                                   │  │
│  │             │                                           │  │
│  │             │ ens18                                     │  │
│  │             │                                           │  │
│  │          10.10.10.1                                     │  │
│  │      ┌──────┴────────┐                                  │  │
│  │      │               │                                  │  │
│  └──────┤  router-vm    ├──────────────────────────────────┘  │
│         │  (ID: 100)    │                                     │
│  ┌──────┤  Ubuntu 24.04 ├──────────────────────────────────┐  │
│  │      │  1GB RAM      │                                  │  │
│  │      │               │                                  │  │
│  │      └──────┬────────┘                                  │  │
│  │          10.20.20.1                                     │  │
│  │             │                                           │  │
│  │             │ ens19                                     │  │
│  │             │                                           │  │
│  │      ┌──────┴───────┐                                   │  │
│  │      │  test-vm2    │                                   │  │
│  │      │  (ID: 102)   │                                   │  │
│  │      │  Ubuntu 24.04│                                   │  │
│  │      │  1GB RAM     │                                   │  │
│  │      └──────────────┘                                   │  │
│  │         10.20.20.10                                     │  │
│  │                                                         │  │
│  │  Lab Network B: vmbr2 (10.20.20.0/24)                   │  │
│  │                                                         │  │
│  └─────────────────────────────────────────────────────────┘  │
│                                                               │
└───────────────────────────────────────────────────────────────┘

Traffic Flow Example:
test-vm1 (10.10.10.10) → router-vm (10.10.10.1) → router-vm (10.20.20.1) → test-vm2 (10.20.20.10)
```

---

## Virtual Machines Created

| VM Name | VM ID | vCPU | RAM | Disk | IP Address(es) | Network(s) | Gateway | Role |
|---------|-------|------|-----|------|----------------|------------|---------|------|
| router-vm | 100 | 1 | 1GB | 8GB | 10.10.10.1<br>10.20.20.1 | vmbr1<br>vmbr2 | N/A | Router between networks |
| test-vm1 | 101 | 1 | 1GB | 8GB | 10.10.10.10 | vmbr1 | 10.10.10.1 | Test client (Network A) |
| test-vm2 | 102 | 1 | 1GB | 8GB | 10.20.20.10 | vmbr2 | 10.20.20.1 | Test client (Network B) |

**OS:** Ubuntu Server 24.04.1 LTS (all VMs)  
**Storage:** local-lvm (SSD)  
**Total Resources:** 3 vCPUs, 3GB RAM, 24GB disk

*note:* see [server-specs.md](/hardware/server-specs.md) for current running VM's and resource usage

---

## Implementation Steps

### Phase 1: Network Bridge Creation

**Created two isolated virtual networks in Proxmox:**

**vmbr1 Configuration:**
```
Name: vmbr1
IP/CIDR: 10.10.10.1/24
Autostart: Yes
Bridge ports: (none - isolated)
Purpose: Lab Network A
```

**vmbr2 Configuration:**
```
Name: vmbr2
IP/CIDR: 10.20.20.1/24
Autostart: Yes
Bridge ports: (none - isolated)
Purpose: Lab Network B
```

**Verification:**
```bash
ip link show type bridge
# Output showed vmbr0, vmbr1, vmbr2 all UP
```

---

### Phase 2: VM Creation & Installation

**Downloaded Ubuntu Server ISO:**
```bash
cd /var/lib/vz/template/iso
wget https://releases.ubuntu.com/24.04/ubuntu-24.04.3-live-server-amd64.iso
```

**Created VMs via Proxmox web interface:**
- Used VirtIO drivers for network (paravirtualized - better performance)
- Installed OpenSSH server during OS installation
- router-vm required two network interfaces (added second NIC post-creation)

**Installation Notes:**
- Initial attempt with 512MB RAM failed during kernel installation
- Increased to 1GB RAM - installation completed successfully
- Installation took ~15-20 minutes per VM on HDD-backed storage

---

### Phase 3: Network Configuration

**router-vm - Network Configuration:**

Located interfaces:
```bash
ip link show
# Identified: ens18 (vmbr1), ens19 (vmbr2)
```

Configured static IPs via netplan (`/etc/netplan/50-cloud-init.yaml`):
```yaml
network:
  version: 2
  ethernets:
    ens18:
      addresses:
        - 10.10.10.1/24
    ens19:
      addresses:
        - 10.20.20.1/24
```

Enabled IP forwarding (makes Linux act as router):
```bash
# Enable immediately
sudo sysctl -w net.ipv4.ip_forward=1

# Persist across reboots
echo "net.ipv4.ip_forward=1" | sudo tee -a /etc/sysctl.conf

# Verify
cat /proc/sys/net/ipv4/ip_forward
# Output: 1 -> Success!
```

---

**test-vm1 - Network Configuration:**

Static IP configuration (`/etc/netplan/50-cloud-init.yaml`):
```yaml
network:
  version: 2
  ethernets:
    ens18:
      addresses:
        - 10.10.10.10/24
      routes:
        - to: default
          via: 10.10.10.1
      nameservers:
        addresses:
          - 8.8.8.8
```

Applied configuration:
```bash
sudo netplan apply

# Verification
ip addr show ens18
# Output: inet 10.10.10.10/24 -> Success!

ip route
# Output: default via 10.10.10.1 -> Success!
```

---

**test-vm2 - Network Configuration:**

Static IP configuration (`/etc/netplan/50-cloud-init.yaml`):
```yaml
network:
  version: 2
  ethernets:
    ens18:
      addresses:
        - 10.20.20.10/24
      routes:
        - to: default
          via: 10.20.20.1
      nameservers:
        addresses:
          - 8.8.8.8
```

Applied and verified same as test-vm1.

---

## Testing & Validation

### Test 1: Local Network Connectivity

**From test-vm1 → router-vm (same network):**
```bash
ping -c 3 10.10.10.1
```

**Result:**
```
PING 10.10.10.1 (10.10.10.1) 56(84) bytes of data.
64 bytes from 10.10.10.1: icmp_seq=1 ttl=64 time=0.245 ms
64 bytes from 10.10.10.1: icmp_seq=2 ttl=64 time=0.312 ms
64 bytes from 10.10.10.1: icmp_seq=3 ttl=64 time=0.298 ms

--- 10.10.10.1 ping statistics ---
3 packets transmitted, 3 received, 0% packet loss, time 2048ms
rtt min/avg/max/mdev = 0.245/0.285/0.312/0.028 ms
```

**Success!!** - Local network connectivity verified

---

**From test-vm2 → router-vm (same network):**
```bash
ping -c 3 10.20.20.1
```

**Result:**
```
[Similar successful output]
```

[X] **Success** - Local network connectivity verified

---

### Test 2: Cross-Network Routing (The Main Goal!)

**From test-vm1 → test-vm2 (different networks):**
```bash
ping -c 5 10.20.20.10
```

**Result:**
```
PING 10.20.20.10 (10.20.20.10) 56(84) bytes of data.
64 bytes from 10.20.20.10: icmp_seq=1 ttl=63 time=0.512 ms
64 bytes from 10.20.20.10: icmp_seq=2 ttl=63 time=0.445 ms
64 bytes from 10.20.20.10: icmp_seq=3 ttl=63 time=0.478 ms
64 bytes from 10.20.20.10: icmp_seq=4 ttl=63 time=0.501 ms
64 bytes from 10.20.20.10: icmp_seq=5 ttl=63 time=0.489 ms

--- 10.20.20.10 ping statistics ---
5 packets transmitted, 5 received, 0% packet loss, time 4089ms
rtt min/avg/max/mdev = 0.445/0.485/0.512/0.022 ms
```

**Success!!** - Cross-network routing working!

**Note:** TTL=63 (not 64) indicates packet passed through one router hop

---

**From test-vm2 → test-vm1 (reverse direction):**
```bash
ping -c 5 10.10.10.10
```

**Result:** Similar successful output

**Success!!!** - Bidirectional routing confirmed

---

### Test 3: Route Tracing

**From test-vm1, trace path to test-vm2:**
```bash
traceroute 10.20.20.10
```

**Result:**
```
traceroute to 10.20.20.10 (10.20.20.10), 30 hops max, 60 byte packets
 1  10.10.10.1 (10.10.10.1)  0.387 ms  0.245 ms  0.198 ms
 2  10.20.20.10 (10.20.20.10)  0.712 ms  0.598 ms  0.534 ms
```

**Analysis:**
- Hop 1: router-vm (10.10.10.1) - entered router from vmbr1
- Hop 2: test-vm2 (10.20.20.10) - reached destination on vmbr2

**Success!** - Traffic routes through router-vm as expected

---

## Performance Metrics

| Metric | Value | Notes |
|--------|-------|-------|
| **Ping Latency (same network)** | ~0.3 ms | test-vm1 ↔ router-vm |
| **Ping Latency (cross-network)** | ~0.5 ms | test-vm1 ↔ test-vm2 |
| **Routing Overhead** | ~0.2 ms | Negligible impact |
| **Packet Loss** | 0% | All tests |
| **VM Boot Time** | ~20 sec | From POST to login |
| **Network Configuration Time** | ~5 min | Per VM (netplan + apply) |

---

## Challenges & Solutions

### Challenge 1: Ubuntu Installation Failures

**Problem:**  
Initial VM creation used 512MB RAM. Ubuntu Server 24.04 installer crashed during kernel installation phase with out-of-memory errors.

**Symptoms:**
- Installer hung at "Installing the kernel" step
- After reboot, VMs stuck at "Booting from Hard Disk"
- Disks were corrupted from incomplete install

**Root Cause:**  
Ubuntu Server 24.04 requires minimum 1GB RAM for installation, user error on that one.

**Solution:**
1. Stopped affected VMs
2. Detached and removed corrupted disks via Hardware tab
3. Created fresh 8GB disks
4. Increased RAM to 1GB (1024MB)
5. Reinstalled Ubuntu - completed successfully

**Time Lost:** ~30 minutes

**Lesson Learned:**  
Always thouroughly check minimum requirements before VM creation. Could have saved time by starting with correct specs.

---

### Challenge 2: Network Interface Naming Confusion

**Problem:**  
Initially uncertain whether test-vm1 and test-vm2 should use different interface names (ens18 vs ens19).

**Misconception:**  
Thought interface names were global/correlated with bridge assignment.

**Resolution:**  
Interface names are local to each VM. Both test VMs have only one NIC each, so both use `ens18`. Only router-vm has two NICs (`ens18` and `ens19`) because it connects to two networks.

**Lesson Learned:**  
- Interface names are per-machine, not global
- `ip link show` on each VM reveals its specific interfaces
- Number of interfaces = number of NICs assigned to that machine

---

### Challenge 3: Netplan Syntax Sensitivity

**Problem:**  
First netplan configuration (router-vm) didn't apply - `sudo netplan apply` showed errors.

**Cause:**  
Simple spelling error, 'enthernets:' instead of 'ethernets:' in router-vm netplan yaml

**Solution:**
- Fixed spelling mistake
- Verified with `sudo netplan apply`

**Lesson Learned:**  
Always ensure correct spelling and syntax before applying changes to code.

---

## Skills Acquired

### Technical Skills

**Networking:**
- [X] **Subnetting:** Understanding CIDR notation (/24 = 255.255.255.0)
- [X] **Routing fundamentals:** How packets move between networks via gateways
- [X] **IP forwarding:** Enabling Linux kernel routing capability
- [X] **Network isolation:** Creating separate broadcast domains

**Linux Administration:**
- [X] **Netplan configuration:** Modern Ubuntu/Debian network configuration
- [X] **Static IP assignment:** Manual IP configuration vs DHCP
- [X] **Service configuration:** Persistent system settings via sysctl

**Proxmox/Virtualization:**
- [X] **Virtual bridge creation:** Software-defined networking
- [X] **VM hardware management:** Adding/removing NICs, adjusting resources
- [X] **ISO management:** Uploading and using installation media

**Troubleshooting:**
- [X] **ping:** Basic connectivity testing
- [X] **traceroute:** Path tracing and hop identification
- [X] **tcpdump:** Live packet capture and analysis
- [X] **ip commands:** Modern Linux networking inspection (`ip addr`, `ip route`, `ip link`)

---

### Soft Skills

- [X] **Problem decomposition:** Breaking complex task into phases
- [X] **Systematic troubleshooting:** Methodical issue identification
- [X] **Documentation discipline:** Recording configs and results as work progresses
- [X] **Resource planning:** Understanding VM requirements before creation

---

## Real-World Applications (according to Claude.AI Sonnet v4.5)

This project simulates common enterprise networking scenarios:

1. **Multi-site connectivity:**  
   In real networks, router-vm would be a physical router/firewall connecting branch offices or VLANs.

2. **Network segmentation:**  
   Enterprises separate networks for security (e.g., HR systems separate from guest WiFi). This demonstrates that isolation.

3. **Router configuration:**  
   Linux servers are commonly used as routers in production (cheaper than hardware routers, more flexible).

4. **Troubleshooting methodology:**  
   The testing process (local → cross-network → traceroute → packet capture) is how network engineers diagnose real issues.

---

## Configuration Files Reference

### router-vm: /etc/netplan/50-cloud-init.yaml
```yaml
network:
  version: 2
  ethernets:
    ens18:
      addresses:
        - 10.10.10.1/24
    ens19:
      addresses:
        - 10.20.20.1/24
```

### router-vm: /etc/sysctl.conf (IP forwarding)
```bash
# Added at end of file:
net.ipv4.ip_forward=1
```

### test-vm1: /etc/netplan/50-cloud-init.yaml
```yaml
network:
  version: 2
  ethernets:
    ens18:
      addresses:
        - 10.10.10.10/24
      routes:
        - to: default
          via: 10.10.10.1
      nameservers:
        addresses:
          - 8.8.8.8
```

### test-vm2: /etc/netplan/50-cloud-init.yaml
```yaml
network:
  version: 2
  ethernets:
    ens18:
      addresses:
        - 10.20.20.10/24
      routes:
        - to: default
          via: 10.20.20.1
      nameservers:
        addresses:
          - 8.8.8.8
```

---

## Next Steps & Extensions

**Potential enhancements to this project:**

1. **Add firewall rules:**  
   Use `iptables` on router-vm to control which traffic is allowed between networks

2. **Create a third network:**  
   Iterate on current setup by adding vmbr3 and another test VM - practice routing between three networks

3. **Implement NAT:**  
   Configure router-vm to provide internet access to test VMs via NAT

4. **Add monitoring:**  
   Install basic monitoring tools to track traffic statistics

---

## Command Reference

**Useful commands from this project:**
```bash
# Network information
ip addr show              # Show all IP addresses
ip link show              # Show all network interfaces
ip route                  # Show routing table
ip link show type bridge  # Show only bridge interfaces

# Connectivity testing
ping <ip>                # Test basic connectivity
ping -c 5 <ip>           # Ping exactly 5 times
traceroute <ip>          # Trace network path

# Packet capture
sudo tcpdump -i <interface>          # Capture on specific interface
sudo tcpdump -i <interface> icmp     # Capture only ICMP (ping)
sudo tcpdump -i <interface> icmp -n  # Don't resolve hostnames

# Network configuration
sudo netplan try          # Test config (auto-reverts in 120s)
sudo netplan apply        # Apply configuration permanently
cat /etc/netplan/*.yaml   # View current netplan config

# IP forwarding
cat /proc/sys/net/ipv4/ip_forward     # Check if enabled (1=yes, 0=no)
sudo sysctl -w net.ipv4.ip_forward=1  # Enable temporarily
cat /etc/sysctl.conf                  # View persistent settings

# VM management (from Proxmox host)
qm list                  # List all VMs
qm start <vmid>          # Start VM
qm stop <vmid>           # Stop VM
qm status <vmid>         # Check VM status
```

---

## Project Completion Checklist

- [x] Created vmbr1 (10.10.10.0/24)
- [x] Created vmbr2 (10.20.20.0/24)
- [x] Downloaded Ubuntu Server ISO
- [x] Created router-vm with dual NICs
- [x] Created test-vm1 on vmbr1
- [x] Created test-vm2 on vmbr2
- [x] Installed Ubuntu on all VMs
- [x] Configured static IPs on all VMs
- [x] Enabled IP forwarding on router-vm
- [x] Verified local network connectivity
- [x] Verified cross-network routing
- [x] Traced routing path with traceroute
- [x] Documented all configurations
- [x] Created ASCII network topology diagram
- [x] Recorded challenges and solutions
- [x] Updated GitHub repository

---

**Project Status:** Complete  
**Date Completed:** Nov. 22, 2025  
**Total Time:** ~5 hours  
**Ready for:** Project 2 (Pi-hole DNS Server)
