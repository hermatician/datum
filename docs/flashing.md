# datum — flashing guide

---

## Requirements

- USB-C cable (must be a data cable, not charge-only)
- Metal tweezers for resetting
- Both `cradio_left` and `cradio_right` `.uf2` firmware files from the [Actions tab](../../actions)

---

## Getting the firmware

1. Go to [github.com/hermatician/datum](https://github.com/hermatician/datum)
2. Click the **Actions** tab
3. Click the latest successful build run
4. Scroll to the bottom and download the **firmware** artifact
5. Unzip — you will find:
   - `cradio_left-nice_nano__zmk-zmk.uf2`
   - `cradio_right-nice_nano__zmk-zmk.uf2`

---

## Entering bootloader mode

The Nice!nano enters bootloader mode when the reset pads are shorted twice quickly.

1. Connect the half you want to flash via USB-C
2. Watch `dmesg` in a terminal:
```bash
sudo dmesg -w
```
3. Short the two reset pads on the PCB twice quickly with metal tweezers
4. Watch for this in dmesg:
```
usb: Product: nice!nano v2
usb-storage: USB Mass Storage device detected
sd: [sda] Attached SCSI removable disk
```

That confirms bootloader mode is active and the controller is ready to flash.

---

## Flashing

```bash
sudo cp ~/Downloads/cradio_left-nice_nano__zmk-zmk.uf2 /dev/sda
sync
```

Then unplug and replug the USB-C cable. Watch dmesg for:
```
usb: Product: datum
```

That confirms the firmware was applied successfully.

Repeat for the right half using `cradio_right-nice_nano__zmk-zmk.uf2`.

---

## After flashing both halves

Power cycle in this order:

1. Power off both halves
2. Power on **right half first**
3. Wait 10 seconds
4. Power on **left half**

The halves will find each other automatically over Bluetooth within 60 seconds.

---

## Re-pairing with your computer

If the keyboard fails to connect after flashing:

```bash
bluetoothctl
remove <MAC address of datum>
scan on
```

Wait for datum to appear, then:

```bash
pair <MAC>
trust <MAC>
connect <MAC>
```

If pairing fails with `AuthenticationFailed`, clear Linux's Bluetooth bond data:

```bash
sudo systemctl stop bluetooth
sudo rm -rf /var/lib/bluetooth/8C:C6:81:14:16:AC/
sudo mkdir /var/lib/bluetooth/8C:C6:81:14:16:AC/
sudo systemctl start bluetooth
```

Then try pairing again.

---

## Notes

- Always flash the left half before the right half
- Use `cp` not `dd` — `dd` may not reliably write the UF2 bootloader
- If `dmesg` shows `error -71`, try a different USB-C cable or USB port
- The bodge wire on the right half's inner thumb key (SW17/B5) is fragile — handle with care
