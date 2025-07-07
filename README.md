# 🍎 Hackintosh: macOS Sequoia on Beelink SER5 Pro (Ryzen 7 5850U)

A complete guide for running **macOS Sequoia 15.5** on a **Beelink SER5 Pro** using OpenCore. Includes Wi-Fi/Bluetooth fixes for Intel AX200, EFI setup, USB creation, and post-install tweaks.

---

## 🖥️ Hardware Specs

| Component        | Detail                          |
|------------------|----------------------------------|
| Model            | Beelink SER5 Pro                |
| CPU              | AMD Ryzen 7 PRO 5850U           |
| GPU              | Integrated AMD Radeon Graphics  |
| Wi-Fi            | Intel Wi-Fi 6 AX200             |
| Bluetooth        | Integrated via AX200            |
| Storage          | NVMe SSD (macOS) + SATA SSD     |
| RAM              | 16 GB DDR4 3200 MHz             |

---

## 📦 Tools You'll Need 
(Some tools are already in the Installer if you downlaoded the installer image from Olarila, you'll see "Install macOS Sequoia" on the desktop, open it and you'll see a folder called "FILES".)

| Tool | Purpose |
|------|---------|
| `balenaEtcher` | Flash `.raw` to USB |
| [MountEFI](https://github.com/corpnewt/MountEFI) | Mount EFI partitions |
| [OCAuxiliaryTools](https://github.com/ic005k/OCAuxiliaryTools) | Modify EFI, kexts, config.plist |
| [HeliPort](https://github.com/OpenIntelWireless/HeliPort) | GUI frontend for itlwm Wi-Fi |
| [Hackintool](https://github.com/headkaze/Hackintool) | System info and patch validation |

---

## 📥 Downloading the macOS Sequoia Image

> ⚠️ **Note:** The installer image was **not** downloaded from Apple.  
> Instead, we used a `.raw` image from **[Olarila.com](https://olarila.com/)**.

1. Go to: [https://olarila.com/forum/69-macos-iso/](https://olarila.com/forum/69-macos-iso/)
2. Create a free account and sign in.
3. Download the **macOS Sequoia 15.5 RAW image** (not a VM image).

---

## 💿 Creating the USB Installer

1. Flash the `.raw` file to a **32GB+ USB** using **balenaEtcher**.
2. Mount the EFI partition using **MountEFI** or **Clover Configurator**.
3. Replace the USB's `EFI` folder with the custom one provided in this repo.
   - Be careful of **nested folders**. You want:
     ```
     /EFI/BOOT/
     /EFI/OC/
     ```
   - Not: `/EFI/EFI/BOOT/...`

4. Plug the USB into your Beelink and boot into BIOS:
   - Disable **Secure Boot**
   - Set **USB** as first boot
   - Enable **AHCI**

---

## 🖥️ Installing macOS

1. Boot into the macOS installer from USB.
2. In **Disk Utility**:
   - Select your SSD
   - Erase as **APFS** and **GUID Partition Map**
3. Start the **macOS Installation**
   - May pause at "XX minutes remaining” — this is normal, it actually took me a really long time to get it installed. Maybe 45 minutes? maybe longer? I think it was under an hour though. Don't panic, have patience.
4. After rebooting, complete macOS setup.

---

## ⚠️ Important Post-Install Step: EFI Transfer

> Once macOS is installed, you **must copy the EFI folder** from your USB to the internal drive’s EFI partition.

**How:**
1. Use **MountEFI** to mount the internal EFI (usually `disk0s1`).
2. If you don’t see the partition:
   ```bash
   diskutil list
   sudo diskutil mount disk0s1
   ```
3. Copy your working `EFI` folder from USB to the mounted volume.

📝 Your USB may show up as an **Install macOS Sequoia** icon on the desktop. Inside, you'll find a `FILES` folder with useful post-install tools — keep this for reference.

---

## 📡 Wi-Fi Fix: Intel AX200 (No AirportItlwm for Sequoia)

Because **AirportItlwm.kext** doesn't support Sequoia yet, we used the **itlwm.kext + HeliPort** workaround.

**Steps:**

1. Download the latest `itlwm.kext` from [OpenIntelWireless](https://github.com/OpenIntelWireless/itlwm).
2. Add it to your EFI’s `/EFI/OC/Kexts/` folder.
3. NOTE: for me even though I dropped the itlwn.kext into the EFI folder, it did not show up in the OCAuxiliaryTools app for me to configure, so I had to:
4. In **OCAuxiliaryTools**:
   - Drag and drop the `itlwm.kext` file into the Kernel section (where all the .kext files are listed).
   - Make sure it appears and is enabled.
5. Download and install **HeliPort.app** in macOS.
6. Reboot and connect to Wi-Fi via HeliPort.

🎥 Video tutorial: [Watch at 5:59](https://youtu.be/d7F5d7EF334?t=359)

---

## 🔵 Bluetooth Fix (AX200)

We followed this guide: [https://youtu.be/c1ZxTMtGQSk](https://youtu.be/c1ZxTMtGQSk)

1. Add these kexts to your EFI:
   - `IntelBluetoothFirmware.kext`
   - `IntelBluetoothInjector.kext`
2. Use **OCAuxiliaryTools** to drag and drop these into your config.
3. Reboot — Bluetooth should now detect and pair with devices.

---

## 🐛 Common Problems & Fixes

| Problem | Fix |
|--------|-----|
| Chrome lags, stutters | Launch with: <br> `open -a "Google Chrome" --args --disable-gpu` |
| Can’t find EFI partition on internal disk | Use Terminal: `sudo diskutil mount disk0s1` |
| Bluetooth not working | Recheck kext order, ensure no duplicate injector |
| Sandboxed APFS resize error | Grant Terminal full disk access via **System Settings > Privacy & Security > Full Disk Access** |

---

## 📁 EFI Folder

> ✅ EFI provided in this repo under `/EFI.zip`  
> ✅ Built for macOS Sequoia 15.5  
> ✅ Configured via OCAuxiliaryTools with all kexts added properly  
> ✅ Supports AMD Ryzen + Intel AX200

Make sure you always **back up your working EFI** before making changes.

---

## ✅ Recap

- macOS Sequoia installed and bootable
- Wi-Fi works via itlwm + HeliPort
- Bluetooth works using Intel kexts
- Chrome GPU issue resolved with flag
- EFI transferred to internal SSD
- System stable and responsive
