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
  - Concerns: Small size

- **Storage (2):** Western Digital 1TB 3.5" SATA III HDD
  - Source: Included from previous build in donated case
  - Concerns: None

- **Storage (3):** Western Digital 500GB 2.5" SATA III HDD
  - Source: Old laptop (~2018)
  - Concerns: Age, speed
  
- **PSU:** Corsair HX750 (750W, 80+ Gold, Semi-Modular)
  - Source: Friend donation
  - Verified: Powers on, all necessary cables present
  
- **Case:** Corsair Vengeance C70 (ATX Mid Tower)
  - Source: Friend donation
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
- **Power savings:** ~55W reduction compared to leaving GPU installed
- **Kept accessible:** Will store GPU nearby for troubleshooting if needed

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
  Initially, the case was a gift to a friend from one of their relatives. It served as their primary PC when they were in middle school into high school, mainly used for gaming and internet browsing according to friend. Since then, it has sat in a closet in their parents house, collecting dust, until I mentioned wanting to start this homelab to them and they offered to give this PC to me. I accepted, and thusly got everything needed to create this server effectively for free. The original specs were;
  - CPU: Intel Core i5-3570K (w/ stock cooler)
  - RAM: 8GB (4x2GB) DDR3 @ 1600MHz
  - MoBo: ASUS P8Z77-V LX (Z77 Chipset)
  - Storage: 240GB boot SSD (Intel), 1TB mass-storage HDD (WD)
  - GPU: NVIDEA GeForce GTX 650 Ti
  - PSU: Corsair HX750 (750W, 80+ Gold)
  - Case: Corsair Vengeance C70

  Additionally, I had a motherboard and CPU from a previous personal build, and the same friend offered to give me a spare kit of DDR4 RAM and a 2.5" HDD they had lying around as well. Swapping out the RAM, CPU, motherboard, and adding the extra drive, that leaves us with the following parts list;
  - CPU: Intel Core i7-10700F (w/ stock cooler, as LGA1200 and LGA 115X are *mostly* interchangable for coolers)
  - RAM: Corsair Vengeance 16GB (2x8GB) DDR4 @ 3000Mhz (rated for, but couldn't reach, will elaborate shortly)
  - Motherboard: MSI Z490-A Pro (Z490 Chipset)
  - Storage: 240GB boot SSD, 1TB + 500GB HDDs
  - GPU: NVIDEA GeForce GTX 650 Ti
  - PSU: Corsair HX750
  - Case: Corsair C70

  When the PC got to me, it was quite dusty, so some time was spent cleaning everything up. Chassis panels and the chassis frame itself (once all hardware was removed) was cleaned with moistened paper towels, while more sensitive components were simply wiped to remove dust or scrubbed gently with 98% isopropyl alcohol-soaked paper towels to remove stubborn dirt/dust on the PCBs. Additionally, a powered air-duster was used with a soft-bristled brush to remove dust harder-to-reach areas, such as fan blades and the CPU cooler heatsink. Once everything was cleaned I ensured the case's standoffs were installed in the correct locations, and moved onto motherboard prep and installation.

### Motherboard Prep and Installation:
  Thankfully, motherboard prep was really easy for this build, as the most important part was already installed. The CPU was kept in the socket while the motherboard was being stored, mostly just to keep the socket from possibly getting damaged. But that meant all I really had to do to prep the board for installation was to remove the Corsair AIO Intel backplate installed on it from the build it was in previously, install the RAM, and install the CPU cooler. The RAM installation went flawlessly, just requiring a quick look at the siltscreening on the motherboard to see the proper slots to use to enable 1DPC dual channel mode (the usual "A2 and B2" in my case), lining up the notch on the DIMMs with the one on the slot, and clicking them into place.

  The cooler was a little more confusing, but still not bad. I just got a little confused at how the leg clips work since it had been a while since I'd installed an Intel stock cooler, but it was easy enough to figure out and get them ready to install. I typically use Noctua NT-H1 as my go-to thermal paste, and decided to here as well, adding a small blob of it onto the CPUs IHS before clicking the cooler into place. Once everything was installed, and the CPU cooler fan had bben plugged into the correct header, I temporarily added the GPU into the top PCIe x16 slot, plugging in all the PSU cables and turning it on by jumping the switch header to make sure everything spun up and the system powered on. Luckily, everything appeared to be working properly!

  Next, I moved onto placing the motherboard into the case. This would prove to be a bit of an ordeal. Like a lot of older ATX boards, the previous motherboard from the old PC hd been a bit thinner than modern boards, only having 6 mount points instead of the ~9 most modern ATX boards have. Luckily, the case had the correct configuration of standoffs to support a modern ATX board, but I was still left with 5 out of the 8 screws I needed to properly mount the board (the case used a peg standoff to hold the board in place without screws, the other standoffs were standard and used to fully mount the original board in the case). I decided to use 4 for the corners, as well as the mount point under the I/O ports, as that would provide  the most support to the I/O and potential expansion cards I may add at some point.

### Finishing Up the Build:
  Finally, the last step was to get everything else mounted back into the case and connected up to the motherboard. First I conntected the front IO, which involved plugging in the front USB, the HD Audio headers, and the front panel LEDs and buttons. However, I may decide to remove or partially cover/block the HDD activity light, as the server currently lives in my bedroom, and the blinking is kind of distracting as far as trying to sleep goes. Additionally, the HD Audio won't be used, but I decided to plug it in for the sake of not having the header just hanging loose in the back. Next, I connected all the PSU cables, including two PCIe 6-pin cables for the GPU while I set up the OS and BIOS. Lastly, I installed the GPU into the top PCIe x16 slot, making sure to connect the PSU cables once everything was in place and screwed in.

  The last step, as always, was cable management. Firstly, I had to reinstall the case fans, which went easily enough, and luckily my motherboard had ample headers for the 2 intake and 1 exhaust fan I had configured. Then, I managed the power cables, roughly organising them and locking them down in place using the case's built in cable managment clips. Over all, while the cable management isn't the best I've ever been responsible for, It'll definitely do for what this machine is used for, and not locking anything down permanently will allow for me to upgrade or replace anything that needs to be changed or fixed in the future. 

### BIOS and Software Issues
  After finishing the build, the first step was to make sure the machine POSTed and then configure the BIOS. I turned it on, and after waiting for a little bit, I was very happy to see the BIOS screen, although the settings were optimized for my old build on this motherboard, not the PC's current configuration. I shorted the "CMOS Clear" header, and once the reboot finished, the BIOS was ready for reconfiguration. I made sure to set up XMP, though my RAM rated at 3000MHz only boosted to 2933. Not a huge loss, I suppose, and as long as it's stable it shouldn't be a huge deal. Next, I disabled VGA detection, which would allow the PC to boot without a GPU once everything was set up. Finally, I enabled VT-x and VT-d in the "CPU Features" menu so that Proxmox could do it's job once it was installed.

  Once BIOS was configured, I grabbed a thumb drive to load the PVE 9.1-1 ISO onto, booted up RUFUS, and installed the ISO onto the drive. But it would be too easy if it worked first try, wouldn't it? Upon initializing the Proxmox installer ISO, I was greeted by the Installer GUI, but once I clicked any option, it would freeze very quickly after starting the process. This was odd at first, but I decided to let it try to continue, as I'd heard this "freezing" was potentially a normal part of the installation experience. However, after around 15 minutes with no progress, I became convinced that it wasn't going anywhere. I tried a few more times, rebooting and re-attempting the same process, but all with the same result.
  
  Finally, I decided to consult the internet, specifically the Proxmox Forums. There, I discovered that NVIDEA GPUs have issues loading GPU drivers during the installation process, and that usually causes the freezing behavior I'd been experiencing. It was an easy fix, however, and documentation existed to guide me through it. I had to add the parameter `nomodeset` to the linux kernal before starting the installation, which solved the issue, and Proxmox installed with no further issues.

---

## FURTHER DOCUMENTATION ON SERVER SETUP

This concludes the documentation on the physical server build and basics of the Proxmox VE installation process. For further documentation on the rest of the installation, see the [installation.md](/proxmox/installation.md) file. For more detailed server specifications, utilization details, and storage details, see [server-specs.md](/hardware/server-specs.md). For internal network configuration information inside the Proxmox VE OS, see [net-config.md](/proxmox/net-config.md).
