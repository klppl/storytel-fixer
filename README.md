# Storytel Reader Fixer (`storytel-fixer`)

Utilities and ramdisk patches for unlocking, rooting, and optimizing the Storytel Reader (1st and 2nd generation).

---

### 📖 The Backstory (or: Saving My Wife's Sanity)

This project was born out of domestic necessity: my wife's Storytel Reader was acting possessed and driving us both crazy. Before this patch, it had a lovely habit of:
1. **Ghost touches** on the screen randomly flipping pages.
2. **Constantly dropping into idle/sleep mode** mid-sentence.
3. **...and/or stubbornly staying awake all night**, nuking the battery.

Chances are, someone else is out there right now dealing with a buggy Storytel reader—and an increasingly frustrated reader/wife ready to chuck it out the window. Hopefully, this repo saves another e-reader (and relationship). This patch fixes all of those annoyances—plus a bunch more under the hood.


## Features

- **ADB & Root Access**: Enables ADB by default, disables ADB authorization popups, and includes Magisk root support.
- **Sleep & Standby Fix**: Resolves sleep/wake issues by enforcing a reliable 5-minute idle standby delay (`sys.onyx.idledelay 300000`).
- **Memory & Swap Optimization**: Tunes zRAM swapping, single-page clustering, and directory caching for 1GB RAM hardware.
- **Power & Radio Fixes**: Disables cellular radio crash loops (`ril-daemon`) and background GPS daemons; optimizes Wi-Fi scan intervals.
- **Network Keepalive**: Prevents aggressive background termination of Wi-Fi and Bluetooth connections.

---

## Requirements

- **Linux / macOS / Windows**
- **Python 3.7+**
- **rkflashtool**
  - **Debian / Ubuntu**: `sudo apt install rkflashtool`
  - **macOS**: `brew install rkflashtool`
  - **Windows**: Use prebuilt binary in `tools/windows-x86_64/` with WinUSB via Zadig.
- **Android Platform Tools** (`adb`)

---

## Usage

### 1. Enter Maskrom Mode

1. Power off the device (blue LED off) and unplug USB.
2. Press and hold the **orange button** below the screen.
3. While holding the button, connect the reader to your PC via a USB-A to USB-C (or micro-USB for Gen 1) data cable.
4. Release the button after 3–5 seconds (the screen remains off).
5. Verify connection:
   ```bash
   rkflashtool p
   ```

### 2. Dump and Patch Boot Partition

#### Storytel Reader Gen 2 (USB-C / Android 6)
```bash
# Dump the active boot partition
rkflashtool r boot > boot.img

# Repack and apply optimization patches
python3 tools/repack.py --boot_img boot.img --out_img boot-patched.img --mods_dir mods/gen2/

# Flash patched boot image
rkflashtool w boot < boot-patched.img

# Reboot the device
rkflashtool b
```

#### Storytel Reader Gen 1 (Micro-USB / Android 4.2)
```bash
# Dump the active boot partition
rkflashtool r boot > boot.img

# Repack and apply optimization patches
python3 tools/repack.py --boot_img boot.img --out_img boot-patched.img --mods_dir mods/gen1/

# Flash patched boot image
rkflashtool w boot < boot-patched.img

# Reboot the device
rkflashtool b
```

---

## Post-Boot Recommendations

### Disable Background OTA Updates
To prevent official OTA services from overwriting the patched boot image:
```bash
adb shell pm disable-user com.onyx.android.onyxotaservice
adb shell pm hide com.onyx.android.onyxotaservice
```

### Install Custom Launcher & Reader
Sideload open-source E-ink software such as [RelaunchX](https://f-droid.org/en/packages/com.gacode.relaunchx/) and [KOReader](https://koreader.rocks/):
```bash
adb install RelaunchX.apk
adb install KOReader.apk
```

---

## License

Open source under standard MIT / AOSP / Rockchip tool licensing where applicable.
