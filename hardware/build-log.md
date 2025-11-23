# PROXMOX VE SERVER BUILD LOG

## BUILD INFORMATION
- **Start:** Nov. 21, ~7PM
- **End:** Nov. 21, ~10PM
- **Total Build Time:** ~3 Hrs

---

> **TL;DR:** Built a Proxmox server in ~3 hours using donated and spare parts (i7-10700F, Z490-A Pro, 16GB DDR4, mixed SSD/HDD storage). Cleaned and rebuilt the system, installed components, configured BIOS (XMP, VT-x/VT-d, headless boot), and installed Proxmox after fixing an NVIDIA GPU freeze by adding `nomodeset`. System now POSTs, runs stable, is accessible via web UI, and passes initial tests.

---

## PRE-BUILD CHECKLIST

### PARTS INVENTORY

**Verified and ready:**

- **CPU:** Intel Core i7-10700F (8C/16T)
  - Source: Previous desktop build
  - Condition: Working, no issues
  
- **Motherboard:** MSI Z490-A Pro
  - Source: Same desktop build
  - Verified: Supports VT-x/VT-d for virtualization [1]
  
- **CPU Cooler:** Intel E97379-003 (Stock LGA1200/115x) [2]
  - Source: Spare parts from older build
  - Condition: Tested, fan spins freely, minimal dust remaining after wiping/blowing clean
  
- **RAM:** Corsair Vengeance 16GB (2x8GB DDR4) 3000MHz
  - Source: Friend's old AM4 build
  - Speed: Will run at 2933MHz with XMP enabled [3]
  - Previous owner: No stability issues reported

- **Storage (1):** Intel 240GB SSD
  - Source: Included from previous build in donated case
  - Concerns: Small size, age

- **Storage (2):** Western Digital 1TB 3.5" SATA III HDD
  - Source: Included from previous build in donated case
  - Concerns: Age

- **Storage (3):** Western Digital 500GB 2.5" SATA III HDD
  - Source: Old laptop (~2018)
  - Concerns: Age, speed
  
- **PSU:** Corsair HX750 (750W, 80+ Gold, Semi-Modular)
  - Source: Included from previous build in donated case
  - Concerns: Age -> Visible components look OK, max draw also much lower than rated wattage -> *should* be fine for now, theoretically -> replace when possible or if any signs of failure emerge
  - Verified: Powers on, all necessary cables present
  
- **Case:** Corsair Vengeance C70 (ATX Mid Tower)
  - Source: Friend donation, Included spare parts from old gaming build (originally built c.2013) 
  - Verified: most expansion slot covers present, included fans still work, drive bays intact, everything cleaned and prepped for motherboard transplant
  
- **GPU:** NVIDIA GeForce GTX 650 Ti (TEMPORARY)
  - Purpose: Initial setup only
  - Will remove: After web interface verified working [4]

#### Build Notes
**[1] Motherboard - Virtualization Support:**
Z490-A PRO verified to support hardware virtualization (VT-x/VT-d) with the i7-10700F. Will enable during BIOS configuration.

**[2] CPU Cooler - Stock vs Aftermarket:**
Intel stock cooler verified working. Should handle server workload (no overclocking). Will monitor temps and upgrade to aftermarket if exceeding 75°C under load.

**[3] RAM - XMP Configuration:**
Originally planned to run at JEDEC speeds (2666MHz) for stability. However, this kit ran stable at 3200MHz for previous owner. Will enable XMP for better VM performance. Can always disable if instability occurs.

**[4] GPU - Temporary Installation:**
- **Used for:** BIOS config, Proxmox installation, network verification
- **Removal timeline:** After confirming web interface access (192.168.4.10:8006)
- **BIOS setting verified:** Will enable proper UEFI settings once server POSTs
- **Power savings:** ~55W/hr estimated reduction compared to leaving GPU installed
- **Kept accessible:** Will store GPU nearby for troubleshooting if needed

**[General] Part Age**
- Most parts ~12+ years old
  - includes key parts -> storage + PSU
- Replace PSU when possible, monitor drive health closely
- CPU cooler age -> likely *negligible impact* on performance

---

## POST-BUILD TESTING PLAN

### Initial Power-On Tests
1. **Visual inspection** - Check all connections, no loose screws
2. **First POST** - Should see BIOS screen after a few moments, verify with boot debug LEDs
3. **BIOS verification** - Check all components detected (CPU, RAM, storage)
4. **Temperature check** - CPU should be <40°C idle in BIOS

### Configuration
1. **BIOS settings** - Enable VT-x, VT-d, XMP, configure boot options
2. **Stability test** - Let sit in BIOS for 10 minutes, check temps stay stable
3. **USB boot/PVE installation** - Boot from Proxmox installer USB
4. **Network test** - Verify ethernet connection during install using port 'link' and 'activity' LEDs

### Post-Installation Tests
1. **Web interface** - Access from laptop at 192.168.4.10:8006
2. **Internet connectivity** - `ping 8.8.8.8` and `ping google.com` from PVE shell
3. **HDD Health** - Run `smartctl -a /dev/sda` command from PVE shell
     - Possibly create a bash script to run SMART test daily (or see if such a utility is pre-installed in PVE)
5. **Reboot test** - Reboot server, verify it comes back up
6. **Enable and test headless boot** - Once appropriate settings are enabled, ensure server powers on and boots into PVE by using web interface.

### Success Criteria
- [X] System POSTs successfully
- [X] All components recognized
- [X] Proxmox installed and accessible via web interface
- [X] Network connectivity confirmed
- [X] Temps <75°C under load
- [X] No unusual noises (fan grinding, coil whine)

---

## BUILD TIMELINE

### Case Prep and Component Cleaning:
The case and internal components required significant cleaning due to long-term storage. I disassembled the system, removed dust with a powered air duster and soft brush, and used 98% isopropyl alcohol where needed. The chassis panels and frame were wiped down, and all original hardware was removed for the motherboard transplant.

The original system specifications were:
  - CPU: Intel Core i5-3570K (w/ stock cooler)
  - RAM: 8GB (4x2GB) DDR3 @ 1600MHz
  - Motherboard: ASUS P8Z77-V LX (Z77 Chipset)
  - Storage: 240GB boot SSD (Intel), 1TB mass-storage HDD (WD)
  - GPU: NVIDEA GeForce GTX 650 Ti
  - PSU: Corsair HX750 (750W, 80+ Gold)
  - Case: Corsair Vengeance C70

Using the donor case and parts, plus my own former CPU and motherboard and additional storage/RAM, I prepared the system for its new configuration:
  - CPU: Intel Core i7-10700F (w/ stock cooler)
  - RAM: Corsair Vengeance 16GB (2x8GB) DDR4
  - Motherboard: MSI Z490-A Pro (Z490 Chipset)
  - Storage: 240GB boot SSD, 1TB + 500GB HDDs
  - GPU: NVIDEA GeForce GTX 650 Ti
  - PSU: Corsair HX750
  - Case: Corsair C70

### Motherboard Prep and Installation:
Since the i7-10700F was already installed in the MSI Z490-A Pro, prep consisted primarily of:
  - Removing the leftover AIO backplate
  - Installing the RAM (A2/B2 dual-channel slots)
  - Applying thermal paste (Noctua NT-H1)
  - Installing the Intel stock cooler
  - Performing a preliminary POST test with the temporary GPU

The initial test confirmed that fans spun up, the board powered correctly, and no fault indicators appeared.

Installing the board into the case required adjusting standoff usage — the case supports modern ATX mounting patterns, but I only had five suitable screws available from the previous motherboard (a thinner ATX format board common with LGA115X sockets). I selected the four corners plus the I/O-side mount to ensure sufficient structural support.

### Finishing Up the Build:
I reinstalled and connected:
  - Front I/O headers
  - Case fans (2× intake, 1× exhaust)
  - PSU cables
  - Temporary GPU

Cable management was prioritized for serviceability rather than aesthetics, ensuring airflow and ease of future upgrades.

### BIOS and Software Issues
On first POST, I cleared CMOS to reset legacy settings, then configured:
- XMP (RAM runs stable at 2933MHz)
- VT-x and VT-d
- UEFI boot settings
- VGA Detection disabled for headless boot

During Proxmox installation, the installer repeatedly froze at initialization. Research indicated a known NVIDIA GPU driver/installer compatibility issue. Adding the `nomodeset` kernel parameter resolved the problem, and installation completed successfully without further issues.

---

## FURTHER DOCUMENTATION ON SERVER SETUP

This concludes the documentation on the physical server build and basics of the Proxmox VE installation process. For further documentation on the rest of the installation, see the [installation.md](/proxmox/installation.md) file. For more detailed server specifications, utilization details, and storage details, see [server-specs.md](/hardware/server-specs.md). For internal network configuration information inside the Proxmox VE OS, see [net-config.md](/proxmox/net-config.md).
