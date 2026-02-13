# MacBook Pro 2018 15": macOS Sequoia -> Sonoma + Fedora Dual Boot

> Machine: MacBook Pro 2018 15" 256GB with dedicated AMD GPU (MacBookPro15,1)
> Goal: Clean install Sonoma, partition, dual boot with Fedora Linux
> Requirements: WiFi, Bluetooth, keyboard, trackpad, GPU drivers all working

---

## Machines and USB — naming used throughout this guide

| Label         | What it is                                                                                                      |
| ------------- | --------------------------------------------------------------------------------------------------------------- |
| **2018 MBP**  | Your MacBook Pro 2018 15" — the target machine                                                                  |
| **Other Mac** | Your second Mac — used to prepare USB drives                                                                    |
| **USB**       | Your USB flash drive (32GB+) or external HDD — reused for both Sonoma and Fedora. 16GB is too small for Sonoma. |

---

## USB lifecycle

The single USB gets reused. Here's what's on it at each phase:

```
Steps 2-3:  USB = Sonoma installer        (created on Other Mac)
Step 5:     USB = Sonoma installer        (plugged into 2018 MBP, used to install)
Steps 6-7:  USB = still Sonoma installer  (not needed — sitting idle, can unplug)
Step 8:     USB = erased, flashed Fedora  (on Other Mac or 2018 MBP)
Step 9:     USB = Fedora installer        (plugged into 2018 MBP, used to install)
Step 10+:   USB = no longer needed        (done)
```

WiFi firmware is stored on the **EFI partition of the internal SSD** (step 6), so it's never on the USB and survives the USB being erased.

---

## Table of Contents

1. [What You Need](#1-what-you-need)
2. [Download macOS Sonoma Installer](#2-download-macos-sonoma-installer)
3. [Create Bootable Sonoma USB](#3-create-bootable-sonoma-usb)
4. [Disable Secure Boot](#4-disable-secure-boot)
5. [Clean Install macOS Sonoma](#5-clean-install-macos-sonoma)
6. [Extract WiFi/Bluetooth Firmware](#6-extract-wifibluetooth-firmware)
7. [Partition the Disk for Linux](#7-partition-the-disk-for-linux)
8. [Download and Flash Fedora T2 ISO](#8-download-and-flash-fedora-t2-iso)
9. [Install Fedora](#9-install-fedora)
10. [Post-Install: Kernel and Core Drivers](#10-post-install-kernel-and-core-drivers)
11. [Post-Install: WiFi and Bluetooth Firmware](#11-post-install-wifi-and-bluetooth-firmware)
12. [Post-Install: Audio](#12-post-install-audio)
13. [Post-Install: Hybrid Graphics (AMD dGPU)](#13-post-install-hybrid-graphics-amd-dgpu)
14. [Post-Install: Fan Control](#14-post-install-fan-control)
15. [Post-Install: Touch Bar](#15-post-install-touch-bar)
16. [Post-Install: Suspend Fix](#16-post-install-suspend-fix)
17. [Choosing What to Boot (Dual Boot)](#17-choosing-what-to-boot-dual-boot)
18. [Troubleshooting](#18-troubleshooting)

---

## 1. What You Need

- **2018 MBP** — the target machine
- **Other Mac** — to prepare USBs and as your workhorse while the 2018 is being set up
- **USB** — 1x **32GB+** flash drive or external HDD (16GB is not enough for Sonoma — gets reused for both installs)
- A **USB-C to USB-A adapter/hub** if your USB is USB-A
- Internet connection on both Macs
- Time: ~2-3 hours total

---

## 2. Download macOS Sonoma Installer

> **ON: Other Mac** | **USB: not needed yet**

### Option A: App Store (easiest)

1. Open this link: https://apps.apple.com/us/app/macos-sonoma/id6450717509
2. Click **Get** — it will open System Settings / Software Update and download
3. The ~14GB installer will appear in `/Applications/Install macOS Sonoma.app`
4. **Do NOT proceed with the install wizard if it auto-opens** — just close it

### Option B: Terminal

```bash
# List available full installers — find the latest 14.x version
softwareupdate --list-full-installers

# Download it (replace with the latest 14.x version from the list above)
softwareupdate --fetch-full-installer --full-installer-version 14.x.x
```

The installer lands in `/Applications/Install macOS Sonoma.app`.

---

## 3. Create Bootable Sonoma USB

> **ON: Other Mac** | **USB: plugged into Other Mac**

1. Plug in your USB drive (32GB+) to the **Other Mac**
2. Open **Disk Utility**
3. Select the USB drive (the physical device, not a partition) from the sidebar
   - Click **View > Show All Devices** if you don't see it
4. Click **Erase**:
   - Name: `MyVolume`
   - Format: **Mac OS Extended (Journaled)**
   - Scheme: **GUID Partition Map**
5. Click **Erase**, then **Done**

6. Open **Terminal** and run:

```bash
sudo /Applications/Install\ macOS\ Sonoma.app/Contents/Resources/createinstallmedia --volume /Volumes/MyVolume
```

7. Type your password, then type `Y` to confirm
8. Wait ~20-30 minutes. When it says "Install media now available", the USB is ready.
9. Eject the USB from Other Mac.

---

## 4. Disable Secure Boot

> **ON: 2018 MBP (rebooting into Recovery)** | **USB: not needed**

Apple's Secure Boot blocks booting from external media and will block both the Sonoma USB installer and Linux later. Disable it **before** attempting to boot from the USB.

1. On the **2018 MBP** (still running Sequoia), shut it down
2. Power on while holding **Command (⌘) + R** to enter **macOS Recovery**
3. When Recovery loads, go to **Utilities > Startup Security Utility** from the menu bar
4. Authenticate with your admin password
5. Set:
   - **Secure Boot**: select **No Security**
   - **Allowed Boot Media**: select **Allow booting from external or removable media**
6. Close the utility and restart

---

sudo dd if=path/to/fedora-t2.iso of=/dev/rdisk7 bs=1m

## 5. Clean Install macOS Sonoma

> **ON: 2018 MBP** | **USB: move it from Other Mac -> plug into 2018 MBP**

1. Plug the Sonoma USB into the **2018 MBP**
2. Shut down the MacBook completely
3. Power on while holding **Option (⌥)** key
4. In Startup Manager, select the orange **Install macOS Sonoma** drive
5. Wait for it to boot — you'll see a **macOS Utilities** window with several options

### Erase the internal drive first:

6. In the macOS Utilities window, click **Disk Utility** and click **Continue**
7. In Disk Utility, click **View > Show All Devices**
8. Select the top-level internal drive (e.g., "APPLE SSD") — not a volume or container, the physical disk at the top
9. Click **Erase**:
   - Name: `Macintosh HD`
   - Format: **APFS**
   - Scheme: **GUID Partition Map**
10. Click **Erase**, wait for it to finish, then **Done**
11. Close Disk Utility (**Disk Utility > Quit Disk Utility** from the menu bar) — this returns you to the macOS Utilities window

### Install Sonoma:

12. Back in the macOS Utilities window, click **Install macOS Sonoma**, then click **Continue**
13. Agree to the license, select `Macintosh HD` as the target
14. Wait for install (~30-45 min, will reboot multiple times — don't touch anything)
15. Complete the setup assistant (create account, connect to WiFi, etc.)
16. You're now booted into Sonoma on the **2018 MBP**. You can unplug the USB now.

---

## 6. Extract WiFi/Bluetooth Firmware

> **ON: 2018 MBP (running Sonoma)** | **USB: not needed — unplug it / set it aside**

The T2 Mac's WiFi/Bluetooth uses Broadcom chips with Apple-specific firmware. Linux needs this firmware extracted from macOS.

> **Can I do this later?** Yes. Since you're dual-booting, Sonoma is always there. You can skip this step now, install Fedora without WiFi, and come back anytime — just reboot, hold **Option (⌥)**, pick **Macintosh HD**, run the firmware script, then reboot back into Fedora. The guide puts it here because you're already in Sonoma and it's convenient to knock it out before erasing the USB for Fedora.

### Download and run the firmware script

1. On the **2018 MBP** running Sonoma, open **Terminal**
2. Download and run the t2linux firmware script:

```bash
curl -s "https://wiki.t2linux.org/tools/firmware.sh" -o ~/firmware.sh
chmod +x ~/firmware.sh
cd ~
./firmware.sh
```

3. The script is **interactive** — it will show you a menu of methods. Choose **"Copy to EFI"** (Method 1):
   - This copies the firmware files to your EFI partition on the internal SSD
   - The EFI partition is shared between macOS and Linux — Fedora will be able to read it
   - The script may install dependencies (like python3) — let it

4. When it finishes, the firmware is stored on the EFI partition. You'll retrieve it from Fedora in step 11.

### Fallback: Tarball method

If the EFI method fails for any reason, select **"Create tarball"** (Method 2) instead:

- It creates `firmware.tar` in your **Downloads** folder
- You'll need to copy this somewhere accessible from Linux (e.g., AirDrop it to your Other Mac, cloud storage, or email it to yourself)
- You **cannot** rely on putting it on the USB since you'll be erasing the USB for Fedora in step 8

> **Note**: Without this firmware, WiFi and Bluetooth won't work in Fedora. But since Sonoma is always available via the boot menu (step 17), you can come back and do this whenever you want.

---

## 7. Partition the Disk for Linux

> **ON: 2018 MBP (running Sonoma)** | **USB: not needed**

1. Open **Disk Utility**
2. Select **Macintosh HD** in the sidebar (the volume, not the container)
3. Click **Partition** in the toolbar
4. Click the **+** button below the pie chart
5. Select **Add Partition** (NOT "Add Volume" — this is important!)
6. Configure:
   - Name: `Linux`
   - Format: **ExFAT** (will be reformatted during Linux install; just avoid APFS)
   - Size: however much you want for Linux
7. Click **Apply**, then **Partition**

> **Warning**: You cannot resize these partitions later without data loss. Plan accordingly.
> Recommended split for 256GB: ~80GB macOS, ~170GB Linux (macOS doesn't need much if it's not your daily driver).

---

## 8. Download and Flash Fedora T2 ISO

> **ON: Other Mac** (recommended — easier to download there while 2018 MBP is idle)
> **USB: plugged into Other Mac**

You can also do this on the 2018 MBP from Sonoma — either works. Instructions below assume Other Mac.

### Download

1. Go to: https://github.com/t2linux/fedora-iso/releases
2. Download the **f42.2** release (the 43.0 ISO is currently broken — install f42.2 and upgrade later)
3. If the ISO is split into multiple files (e.g., `.iso.00`, `.iso.01`), combine them:

```bash
cat Fedora-*.iso.* > fedora-t2.iso
```

If it's a single `.iso` file, just use it directly.

### Flash to USB

**Make sure you've already done step 6 (firmware to EFI partition) before erasing the USB!** (Or skip it and do it later from Sonoma — see step 6 note.)

1. Plug the USB into the **Other Mac**
2. Find the device identifier:

```bash
diskutil list
```

Look for your USB (e.g., `/dev/disk2`)

3. Unmount it:

```bash
diskutil unmountDisk /dev/diskX
```

(Replace `X` with your disk number)

4. Flash the ISO:

```bash
sudo dd if=path/to/fedora-t2.iso of=/dev/rdiskX bs=1m
```

> Use `rdisk` (not `disk`) for faster write speeds. **Double-check the disk number** — `dd` will happily erase the wrong drive.

5. Wait for it to finish, then eject:

```bash
diskutil eject /dev/diskX
```

---

## 9. Install Fedora

> **ON: 2018 MBP** | **USB: move it from Other Mac -> plug into 2018 MBP**

1. Plug the Fedora USB into the **2018 MBP**
2. Shut down completely
3. Power on while holding **Option (⌥)**
4. Select the orange **EFI Boot** option
5. Fedora Live environment will boot

### In the Fedora Installer (Anaconda):

6. Select language, keyboard, timezone
7. Click **Installation Destination**
8. Select your internal disk
9. Choose **"Share disk with other operating system"** (this is the automatic option)
10. Click **Reclaim additional space**
11. Select the `Linux` (ExFAT) partition you created in macOS and **Delete** it
12. Fedora will use the freed space for its partitions
13. Click **Reclaim space**
14. Click **Begin Installation**
15. Wait for install to complete
16. When done, click **Finish Installation**
17. Shut down, remove the USB — **you're done with the USB for good**

### First boot into Fedora:

18. Power on while holding **Option (⌥)**
19. Select **EFI Boot** (not the macOS option)
20. Fedora should boot — complete the first-run setup (user account, etc.)

---

## 10. Post-Install: Kernel and Core Drivers

> **ON: 2018 MBP (running Fedora)** | **USB: not needed**

The T2 Fedora ISO should already come with the T2 kernel and drivers, but verify and ensure everything is set up:

### Verify T2 kernel

```bash
uname -r
```

You should see a kernel version with `t2` in the name. If not, install it:

```bash
sudo dnf copr enable sharpenedblade/t2linux
sudo dnf swap --from-repo="copr:copr.fedorainfracloud.org:sharpenedblade:t2linux" kernel kernel
```

### Load the apple-bce module (keyboard, trackpad, audio, Touch Bar)

```bash
# Make apple-bce load on every boot
echo apple-bce | sudo tee /etc/modules-load.d/t2.conf
```

### Set required kernel parameters

```bash
sudo grubby --args="intel_iommu=on iommu=pt pcie_ports=compat" --update-kernel=ALL
```

Reboot:

```bash
sudo reboot
```

After reboot (hold **Option ⌥**, pick **EFI Boot** again), verify:

```bash
cat /proc/cmdline
# Should contain: intel_iommu=on iommu=pt pcie_ports=compat
```

---

## 11. Post-Install: WiFi and Bluetooth Firmware

> **ON: 2018 MBP (running Fedora)** | **USB: not needed**

### If WiFi already works:

The T2 Fedora ISO may handle firmware automatically. If WiFi is working after first boot, skip this step.

### If you used the EFI method (step 6, Method 1):

> **No internet?** If WiFi isn't working yet, you need connectivity to download the script. Options:
>
> - USB ethernet adapter
> - Phone USB tethering (iPhone: `Settings > Personal Hotspot > USB`)
> - Download the script on **Other Mac**, put it on the USB, plug USB into 2018 MBP

1. Download the same firmware script on Fedora:

```bash
curl -s "https://wiki.t2linux.org/tools/firmware.sh" -o ~/firmware.sh
chmod +x ~/firmware.sh
~/firmware.sh
```

2. The script will detect you're on Linux and show Linux-side options. Choose **"Retrieve from EFI"** — it will find the firmware you stashed in step 6 and install it.

3. Reload the driver:

```bash
sudo modprobe -r brcmfmac_wcc brcmfmac
sudo modprobe brcmfmac
```

### If you used the tarball method (step 6, fallback):

Get the `firmware.tar` onto the 2018 MBP (via USB from Other Mac, download, etc.), then:

```bash
sudo tar -v -xC /lib/firmware/brcm -f /path/to/firmware.tar
sudo modprobe -r brcmfmac_wcc brcmfmac
sudo modprobe brcmfmac
```

### Verify:

```bash
sudo journalctl -k --grep=brcmfmac
```

You should see a line like:

```
brcmfmac: brcmf_c_preinit_dcmds: Firmware: BCM4364/3 ...
```

WiFi and Bluetooth should now work. Connect to your network via the desktop network manager.

---

## 12. Post-Install: Audio

> **ON: 2018 MBP (running Fedora)**

```bash
git clone https://github.com/kekrby/t2-better-audio.git /tmp/t2-better-audio
cd /tmp/t2-better-audio
sudo ./install.sh
rm -rf /tmp/t2-better-audio
```

PipeWire is recommended over PulseAudio (Fedora uses PipeWire by default, so you're good).

Reboot and test speakers + headphone jack.

---

## 13. Post-Install: Hybrid Graphics (AMD dGPU)

> **ON: 2018 MBP (running Fedora)**

Your 2018 15" has both an Intel iGPU and AMD dGPU. By default, the AMD GPU may run hot and drain battery.

### Set Intel iGPU as primary (recommended for battery life):

```bash
echo "options apple-gmux force_igd=y" | sudo tee /etc/modprobe.d/apple-gmux.conf
```

This lets the AMD dGPU sleep when idle. You can still use it on demand:

```bash
# Run any app on the dedicated AMD GPU
DRI_PRIME=1 glxinfo | grep "OpenGL renderer"
# Should show your AMD GPU

# Run an app on iGPU (default)
glxinfo | grep "OpenGL renderer"
# Should show Intel
```

### If you experience thermal/freeze issues:

```bash
echo low | sudo tee /sys/bus/pci/drivers/amdgpu/0000:0?:00.0/power_dpm_force_performance_level
```

### AMD GPU drivers

The `amdgpu` kernel driver is built into the Linux kernel — no proprietary drivers needed. It should work out of the box with the T2 kernel.

---

## 14. Post-Install: Fan Control

> **ON: 2018 MBP (running Fedora)**

Without fan control, the fans may not spin up properly under load.

```bash
sudo dnf copr enable sharpenedblade/t2linux   # already done if you did step 10
sudo dnf install t2fanrd
sudo systemctl enable --now t2fanrd
```

### Configure (optional):

```bash
sudo nano /etc/t2fand.conf
```

Adjust temperature thresholds and fan curves to your preference, then:

```bash
sudo systemctl restart t2fanrd
```

---

## 15. Post-Install: Touch Bar

> **ON: 2018 MBP (running Fedora)**

Install `tiny-dfr` for Touch Bar function key support:

```bash
sudo dnf install tiny-dfr
```

Configuration file: `/etc/tiny-dfr/config.toml`

### Without tiny-dfr (basic mode switching):

```bash
# Mode 0: only show special keys (brightness, volume, etc.)
# Mode 1: only show F1-F12
# Mode 2: special keys with Fn toggle for F1-F12
echo "options hid-appletb-kbd mode=2" | sudo tee /etc/modprobe.d/tb.conf
```

---

## 16. Post-Install: Suspend Fix

> **ON: 2018 MBP (running Fedora)**

After macOS Sonoma, S3 suspend on T2 Macs requires a workaround.

Create a systemd service:

```bash
sudo tee /etc/systemd/system/suspend-fix-t2.service << 'EOF'
[Unit]
Description=Suspend fix for T2 Macs
Before=sleep.target
StopWhenUnneeded=yes

[Service]
Type=oneshot
RemainAfterExit=yes
ExecStart=/usr/sbin/modprobe -r apple-bce
ExecStop=/usr/sbin/modprobe apple-bce

[Install]
WantedBy=sleep.target
EOF

sudo systemctl enable suspend-fix-t2.service
```

This unloads and reloads the `apple-bce` module on suspend/wake.

---

## 17. Choosing What to Boot (Dual Boot)

After everything is installed, your 2018 MBP has both macOS Sonoma and Fedora on the internal SSD. You can boot into either one at any time.

### How to choose at boot time

1. Power off (or restart) the **2018 MBP**
2. Power on while holding **Option (⌥)** — keep holding until you see the boot menu
3. You'll see:
   - **Macintosh HD** — boots macOS Sonoma
   - **EFI Boot** (or **Fedora**) — boots Fedora Linux
4. Use arrow keys to select, press **Enter**

> You can do this every single time. Both OSes are fully independent. Booting into one does not affect the other.

### Set a default OS

If you don't want to hold Option every time:

**To default to macOS Sonoma:**

- Boot into Sonoma, go to **System Settings > General > Startup Disk**
- Select **Macintosh HD**, click **Restart**
- From now on, it boots Sonoma by default. Hold **Option (⌥)** when you want Fedora.

**To default to Fedora:**

- Boot into Fedora — GRUB is the bootloader
- Fedora will boot by default without holding any keys
- GRUB may also show a macOS entry you can select
- If GRUB doesn't list macOS, install os-prober:
  ```bash
  sudo dnf install os-prober
  sudo grub2-mkconfig -o /boot/grub2/grub.cfg
  ```
- You can always hold **Option (⌥)** to override GRUB and pick macOS directly from Apple's boot menu

### Quick-switching cheat sheet

| I'm in... | I want to go to... | Do this                                                |
| --------- | ------------------ | ------------------------------------------------------ |
| Fedora    | Sonoma             | Reboot, hold **Option (⌥)**, pick **Macintosh HD**     |
| Sonoma    | Fedora             | Reboot, hold **Option (⌥)**, pick **EFI Boot**         |
| Either    | The other one      | Restart + hold **Option (⌥)** — works 100% of the time |

### Can I still access Sonoma anytime?

**Yes, always.** The Fedora install does not touch or modify the macOS partition. Sonoma stays exactly as you left it. You can:

- Boot into Sonoma to check something, use macOS-only apps, run macOS updates
- Boot into Sonoma to re-extract WiFi firmware if needed (step 6)
- Use Sonoma as your daily driver and only switch to Fedora when needed
- Go back and forth as much as you want — there's no limit

The **Option (⌥)** boot menu is baked into the Mac's firmware (not controlled by macOS or Linux). It always works, even if one OS breaks.

---

## 18. Troubleshooting

### Keyboard/trackpad not working in Fedora

- Ensure `apple-bce` module is loaded: `lsmod | grep apple_bce`
- If not: `sudo modprobe apple-bce` and check step 10

### No WiFi after install

- Check firmware: `sudo journalctl -k --grep=brcmfmac`
- If "firmware not found", redo step 11
- Use a USB ethernet adapter or phone USB tethering as a temporary workaround
- Or boot into Sonoma (hold Option ⌥ at boot), re-run the firmware script (step 6), then boot back to Fedora

### Screen black after suspend/wake

- Try adding `i915.enable_guc=3` to kernel parameters:
  ```bash
  sudo grubby --args="i915.enable_guc=3" --update-kernel=ALL
  ```
- If that doesn't help, try `i915.enable_guc=2`

### System runs hot / fans always spinning

- Check step 13 (hybrid graphics) — set iGPU as primary
- Check step 14 (fan control) — install t2fanrd

### No sound

- Verify kernel parameters are set (step 10)
- Verify `apple-bce` is loaded
- Run the audio config from step 12
- Make sure PipeWire is running: `systemctl --user status pipewire`

### GRUB doesn't show macOS

- You can always use the **Option (⌥)** boot menu — this always works regardless of GRUB
- Or install `os-prober`: `sudo dnf install os-prober` then `sudo grub2-mkconfig -o /boot/grub2/grub.cfg`

### Can't reach boot menu / boots wrong OS

- Make sure you're holding **Option (⌥)** immediately from power-on (before the Apple logo)
- If it consistently boots the wrong OS, set the default via step 17

### Want to upgrade from Fedora 42 to 43

After your system is stable:

```bash
sudo dnf upgrade --refresh
sudo dnf install dnf-plugin-system-upgrade
sudo dnf system-upgrade download --releasever=43
sudo dnf system-upgrade reboot
```

---

## Quick Reference: What Makes T2 Hardware Work

| Hardware            | Driver/Package                  | Status                       |
| ------------------- | ------------------------------- | ---------------------------- |
| Keyboard & Trackpad | `apple-bce` kernel module       | Works with T2 kernel         |
| WiFi                | `brcmfmac` + macOS firmware     | Works after firmware install |
| Bluetooth           | `brcmfmac` + macOS firmware     | Works after firmware install |
| Audio (speakers)    | `apple-bce` + t2-better-audio   | Works after config           |
| Touch Bar           | `tiny-dfr` or `hid-appletb-kbd` | Works                        |
| Fan Control         | `t2fanrd`                       | Works                        |
| Intel iGPU          | `i915` (in-kernel)              | Works out of box             |
| AMD dGPU            | `amdgpu` (in-kernel)            | Works out of box             |
| Suspend             | systemd service workaround      | Works with fix               |

---

## Sources

- [T2 Linux Wiki](https://wiki.t2linux.org/)
- [T2 Linux Fedora ISO releases](https://github.com/t2linux/fedora-iso/releases)
- [T2 Linux WiFi/Bluetooth guide](https://wiki.t2linux.org/guides/wifi-bluetooth/)
- [T2 Linux pre-install guide](https://wiki.t2linux.org/guides/preinstall/)
- [T2 Linux post-install guide](https://wiki.t2linux.org/guides/postinstall/)
- [T2 Linux audio config](https://wiki.t2linux.org/guides/audio-config/)
- [T2 Linux hybrid graphics](https://wiki.t2linux.org/guides/hybrid-graphics/)
- [T2 Linux fan control](https://wiki.t2linux.org/guides/fan/)
- [Apple: Create a bootable installer for macOS](https://support.apple.com/en-us/101578)
- [macOS Sonoma on App Store](https://apps.apple.com/us/app/macos-sonoma/id6450717509)
