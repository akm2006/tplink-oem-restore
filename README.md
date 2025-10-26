# Reverting TP-Link Routers (Tested on Archer C20 v5) from OpenWRT to Stock OEM Firmware

> A complete, beginner-friendly guide to restore your TP-Link router back to the original OEM firmware after using OpenWRT — **tested and confirmed on Archer C20 v5**.

## 📌 About This Guide

This guide is for users who:
- Installed OpenWRT on their TP-Link router (especially Archer C20 v5)
- Want to go back to the stock TP-Link firmware
- Tried but failed with TFTP or firmware stripping tools
- Are looking for a clean, working step-by-step tutorial that actually works

> ⚠️ While this guide is tested specifically on Archer C20 v5, it may work on other TP-Link routers with similar architecture. Proceed carefully and check device-specific forums or Wikis before flashing.

***

## ✅ What You Need

- TP-Link router (confirmed on **Archer C20 v5**)
- A PC with **Windows or Linux**
- Working **OpenWRT installed** on the router
- An Ethernet cable to connect PC ↔ router (recommended)
- A stripped version of the TP-Link OEM firmware (`tplink-stripped.bin`)

> ❗ You can strip the firmware using a Linux VM, Windows, or a [web app](https://tplink-firmware-stripper.vercel.app/). Always use the stock firmware for your exact model!

***

## 🧰 Step 1: Get the OEM Firmware and Strip It

1. Go to TP-Link official site → Support → Your Router Model (e.g., Archer C20 v5) → Download the latest firmware.

2. Strip the firmware header (first 131584 bytes) by one of these methods:

### 🟢 Web App (Recommended)
- Use the browser-based tool: [tplink-firmware-stripper.vercel.app](https://tplink-firmware-stripper.vercel.app/)
- Upload your OEM `.bin` file.
- Download the ready-to-flash stripped firmware.

### 🐧 Linux Command:
```bash
dd if=ArcherC20v5_*.bin of=tplink-stripped.bin skip=257 bs=512
```
*(Replace "ArcherC20v5_*.bin" with the name of your firmware file.)*

### 🪟 Windows Methods

#### A. Using HxD (GUI)
- Open the OEM firmware in [HxD](https://mh-nexus.de/en/hxd/)
- Press `Ctrl+E` (Edit → Select Block)
- Length: `1FFA9` (hex)
- Delete selection.
- Save As: `tplink-stripped.bin`

#### B. Swiss File Knife (CLI)
```bash
sfk partcopy ArcherC20v5_*.bin -allfrom 0x1FFA9 tplink-stripped.bin -yes
```
*(Replace filename as needed.)*

> **Credit:** Thanks to [u/Economy_Post_8574](https://www.reddit.com/user/Economy_Post_8574/) for Windows instructions!

**Keep your `tplink-stripped.bin` handy for flashing in the next step.**

***

## 🔁 Step 2: Transfer the Stripped Firmware to Your Router

You need to move your stripped firmware (`tplink-stripped.bin`) to the router's `/tmp` directory.

**Preferred method: SCP**
```bash
scp /path/to/tplink-stripped.bin root@192.168.1.1:/tmp/
```
*(Replace paths and IP as needed.)*

**If SCP fails (rare, e.g., on Lubuntu): Stream via SSH/cat**
```bash
cat /path/to/tplink-stripped.bin | ssh root@192.168.1.1 'cat > /tmp/tplink-stripped.bin'
```

- `/tmp` is best for temporary files when flashing.
- Ensure you have SSH access to the router's OpenWRT shell.

> **Community Tip:** If on other platforms, use above cat trick—works anywhere SSH is available!.[1]

***

## ⚠️ Step 3: Flash the OEM Firmware via MTD

SSH into the router and run:
```bash
cd /tmp
mtd write tplink-stripped.bin firmware
```
*(Change filename to match your stripped file.)*

Wait for the process. Then reboot:
- Run `reboot`.  
- If you see `reboot: I/O error`, power-cycle the router manually.

***

## 🔁 Step 4: Manual Reboot

- Power off (unplug) your router.
- Wait 5–10 seconds.
- Plug it back in.

Watch for LEDs:
- Power LED solid, Wi-Fi LEDs blinking → good sign.

***

## 🌐 Step 5: Access OEM Interface

- Connect PC via LAN/Wi-Fi.
- Visit [http://192.168.0.1](http://192.168.0.1/) or [tplinkwifi.net](http://tplinkwifi.net/).
- You should see the TP-Link web UI!

**Note:** Sometimes old settings and Wi-Fi credentials are auto-restored—TP-Link keeps these in a separate partition.

***

## 🛡️ Optional Tips

- Make a settings backup in TP-Link UI.
- Don't update firmware from the web interface unless needed.
- Keep your stripped file for future restores.

***

## 🧠 Why This Works

TP-Link's OEM firmware has a bootloader/header OpenWRT can't flash directly.
- We remove the header with the strip methods above.
- Once flashed with `mtd`, OEM firmware loads on reboot.

> ⚠️ Works for some TP-Link models ONLY. Flashing wrong files or skipping stripping can brick your device. Always check device-specific guides!

***

## 🧵 Further Reading & Feedback

- [Reddit Thread & Discussion](https://www.reddit.com/r/openwrt/comments/1mdcuhv/successfully_reverted_tplink_archer_c20_v5_from/)
- [GitHub repo](https://github.com/akm2006/tplink-oem-restore)

Share feedback, try this on other models, and improve the guide for all!

***

## 🙌 Credits

- Inspired by countless trial, error, and many router bricks 😅
- Thanks to [Indie_Maverick](https://www.reddit.com/user/Indie_Maverick/) for SCP/cat tip.
- Thanks to OpenWRT community—every bit helped!

***

## 📬 License

MIT License

***
