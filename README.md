# UTM + Windows 11 ARM Setup Guide (Apple Silicon Mac)

This guide explains how to install **Windows 11 ARM** on an Apple Silicon Mac using **UTM**, and prepare the environment for **SAP GUI** (for ABAP programming courses).  

---

## Table of Contents
1. [Requirements](#requirements)
2. [Install UTM](#1-install-utm)
3. [Create the VM](#2-create-the-vm)
4. [Get Windows 11 ARM](#3-get-windows-11-arm)
5. [VM Configuration](#4-vm-configuration)
6. [Install Windows](#5-install-windows)
7. [First Boot & Troubleshooting](#6-first-boot--troubleshooting)
8. [Guest Tools](#7-guest-tools)
9. [Networking Issues](#8-networking-issues)
10. [Next Steps: SAP GUI](#9-next-steps-sap-gui)

---

## Requirements

- Apple Silicon Mac (M1 or later)
- UTM app
- Windows 11 ARM64 ISO
- At least 64 GB free disk space
- Internet connection

Recommended VM RAM:

| Mac RAM | VM RAM |
|---|---:|
| 8 GB | 4 GB |
| 16 GB | 6–8 GB |
| 24 GB+ | 8 GB+ |

---

## 1. Install UTM
- Download: https://mac.getutm.app
- Install and launch UTM.  

## 2. Create the VM

In UTM, select:

```text
Create a New Virtual Machine
→ Virtualize
→ Windows
```

Recommended settings:  
- **CPU**: 4 cores  
- **Memory**: 4-8 GB 
- **Disk**: 64–80 GB (dynamic)  
- Enable **Install drivers and SPICE tools** if available


## 3. Get Windows 11 ARM

Download a **Windows 11 ARM64** ISO.

You can either:

- download it directly from Microsoft, or
- use **CrystalFetch** through UTM by selecting **Get Latest Windows 11 for ARM**.

For CrystalFetch on an Apple Silicon Mac, select:

```text
Windows 11
Build: Latest
Architecture: Apple Silicon
Language: your preferred language
Edition: Windows 11
```
Once the download is finished, return to UTM and select the ISO file to continue.  


## 4. VM Configuration

Select a **Shared Folder** if you want to transfer files between macOS and Windows.

For networking, select:

```text
Shared Network
```
Do not switch to Bridged networking unless Shared Network causes a specific problem.

## 5. Install Windows
- Start the VM → press any key to boot → select your language.
- Product Key: select **I don't have a product key** (or enter one if you have it).
- Edition: select **Windows 11 Pro**.
- Disk: choose **Drive 0 Unallocated Space → Next**.
- Continue with the normal installation.


### Important: Remove the Windows ISO After Installation

After Windows finishes installing for the first time, the VM will restart. You may get stuck at **Start boot option** if the Windows installation ISO is still mounted.

- Shut down the VM.
- Go to the VM overview and find the **CD/DVD** entries.
- Remove/eject only the **Windows installation ISO**.
- Keep `utm-guest-tools-latest.iso`.
- Start the VM again and continue the Windows setup.


## 6. First Boot & Troubleshooting

### Case 1: Message “It looks like you started an upgrade…” / `startup.nsh`
- Reason: VM boots from ISO instead of installed disk.  
- Fix: Shut down → Go to Settings → **CD/DVD → remove Windows ISO** (keep only `utm-guest-tools.iso`) → restart.  


### Case 2: Windows desktop loads, activation fails  
- Message: *“Cannot connect to my organisation's activation server.”*  
- Fix: Ignore activation. Windows works fine except:  
  - Watermark “Activate Windows”  
  - No personalization (wallpapers/themes)  
- All SAP functions work without activation.  


## 7. Guest Tools
- If *Install drivers and SPICE tools* was checked, features like auto-resize and mouse integration should already work.
- If not, Inside Windows:

```text
File Explorer
→ CD/DVD Drive
→ run the Guest Tools installer
```

Restart Windows after installation.→ open `utm-guest-tools.iso` inside Windows and install manually.  



## 8. Networking Issues

If the network does not work:

1. Install or reinstall Windows Guest Tools
2. Check that **Network Mode = Shared Network**
3. Check **Device Manager → Network adapters**
4. Confirm that the VirtIO network adapter is installed

### Bridged Network

If Shared Network still does not work, try Bridged networking.

* On your Mac Terminal → run `ifconfig` to find the correct interface (normally `en0` = Wi-Fi).

* Then in UTM:
1. Shut down VM.
2. Go to **UTM → VM → Edit → Network**.
3. Change **Network Mode** → `Bridged (Advanced)`.
4. Under *Bridged Interface*, select the interface you found (e.g. `en0`).
5. Keep **Emulated Network Card = virtio-net-pci**.

* Start the VM → open **Command Prompt** → run `ipconfig`

If the VM receives an IP address in the same subnet as your Mac, the bridged network is working.


#### Testing Network Inside VM

* Open **Command Prompt** in Windows and test:

1. Open Microsoft Edge and visit a website.

2. Test DNS:
```
nslookup www.microsoft.com
```

4. Test HTTP:
```
curl https://www.microsoft.com
```

## 9. Next Steps: SAP GUI

1. Inside Windows VM → install your university VPN client.
2. Connect VPN → ensures access to campus SAP servers.
3. Download and install **SAP GUI for Windows** (from your university IT portal).
4. Configure SAP connection settings → log in → start ABAP programming.

---

*Maintained by students of Augsburg University of Applied Sciences – updated 2026*
