# Reverting TP-Link Routers (Tested on Archer C20 v5) from OpenWRT to Stock OEM Firmware

> A complete, beginner-friendly guide to restore your TP-Link router back to the original OEM firmware after using OpenWRT — **tested and confirmed on Archer C20 v5**.

---

## 📌 About This Guide

This guide is for users who:

* Installed OpenWRT on their TP-Link router (especially Archer C20 v5)
* Want to go back to the stock TP-Link firmware
* Tried but failed with TFTP or firmware stripping tools
* Are looking for a clean, working step-by-step tutorial that actually works

> ⚠️ While this guide is tested specifically on Archer C20 v5, it may work on other TP-Link routers with similar architecture. Proceed carefully and check device-specific forums or Wikis before flashing.

---

## ✅ What You Need

* TP-Link router (confirmed on **Archer C20 v5**)
* A PC with **Windows or Linux**
* Working **OpenWRT installed** on the router
* An Ethernet cable to connect PC ↔ router (recommended)
* A stripped version of the TP-Link OEM firmware (`tplink-stripped.bin`)

> ❗ You can strip the firmware using a Linux VM or use the TP-Link firmware and remove the header manually.

---

## 🧰 Step 1: Get the OEM Firmware and Strip It

1. Go to TP-Link official site → Support → Your Router Model (e.g., Archer C20 v5) → Download the latest firmware
2. **Use one of the following methods to strip the firmware header (first 131584 bytes):**

### 🟢 Web App (Recommended)
- Use the browser-based tool to strip firmware easily:
  > [https://tplink-firmware-stripper.vercel.app](https://tplink-firmware-stripper.vercel.app)
- Upload your OEM `.bin` file
- It will download a ready-to-flash stripped firmware.

### 🐧 In Linux:
```bash
dd if=ArcherC20v5_*.bin of=tplink-stripped.bin skip=257 bs=512
```
> Replace "ArcherC20v5_*.bin" with name of your firmware file.



### 🪟 In Windows (Alternative)
- Use WSL, a Linux VM, or tools like HxD to manually remove the first 131584 bytes

Save this `tplink-stripped.bin` for flashing.
> Tip:Keep short understandable names for the files to navigate easily and avoid confuison.
---

## 🔌 Step 2: Transfer the Stripped File to Router

1. Boot into OpenWRT and connect via SSH:

```bash
ssh root@192.168.1.1
```

2. Use **WinSCP** or **SCP** to transfer the file to `/tmp/` of the router:

```bash
scp tplink-stripped.bin root@192.168.1.1:/tmp/
```

> Note: SCP might not work if `/usr/libexec/sftp-server` is missing, so use WinSCP with SCP mode.
> Note: Replace "tplink-stripped.bin" with your stripped firmware name.

---

## ⚠️ Step 3: Flash the OEM Firmware via MTD

After transferring the file, SSH into the router and run:

```bash
cd /tmp
mtd write tplink-stripped.bin firmware
```
> Note: Replace "tplink-stripped.bin" with your stripped firmware name.
> Wait for the write process to complete and then restart the router using reboot command. If you see `reboot: I/O error`, don’t panic , you can perform manual reboot.

---

## 🔁 Step 4: Manual Reboot

After flashing:

* **Manually power off** the router (unplug it)
* Wait 5–10 seconds
* **Power it back on**

Watch the LEDs:

* Power LED solid → Wi-Fi LEDs blinking → good sign

---

## 🌐 Step 5: Access OEM Interface

Once rebooted:

* Connect your PC to the router via LAN/Wi-Fi
* Visit: [http://192.168.0.1](http://192.168.0.1) or [http://tplinkwifi.net](http://tplinkwifi.net)

🎉 You should now see the TP-Link web interface!

Surprisingly, **old saved settings might be restored** automatically — TP-Link keeps them in a separate partition sometimes.

---

## 🛡️ Optional Tips

* Use the TP-Link web UI to create a **settings backup**
* Don’t update firmware from web UI again unless needed
* Keep the stripped `.bin` file handy for future restores

---

## 🧠 Why This Works (in simple terms)

TP-Link OEM firmware has a bootloader header. OpenWRT cannot flash it directly.

* We remove that header using `dd`
* Then we flash the clean image using OpenWRT’s `mtd` command

When OpenWRT reboots, the new firmware loads — just like magic.

> ⚠️ **This method only works on some TP-Link models.** Flashing the wrong file or skipping the strip step may brick your device. Always research your model before applying this!

---

## 🧵 Thread on Reddit

\[Coming soon: r/openwrt thread link will go here.]

---

## 🙌 Credits

* Inspired by hours of trial, error, and brick recovery 😅

---

## 📬 License

This project is open-sourced under the MIT License.
