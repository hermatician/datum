# datum — build guide

**made by Christian Faurholt**

---

## Before you start

### Critical notes

**The PCB is reversible.**
The same PCB is used for both left and right halves. Jumper pads determine which side it becomes. You must close the correct jumper pads before soldering anything else.

**Battery polarity is critical.**
Reversed polarity will instantly and permanently damage the Nice!nano. Always verify with a multimeter before soldering.

**Never solder the Nice!nano directly.**
Always use Mill-Max sockets. This allows you to remove the controller for firmware flashing or replacement.

---

## Jumper pads

The datum PCB has jumper pads that configure it as either a left or right half.

- **Left half** — close the jumper pads on the **top side** of the PCB
- **Right half** — close the jumper pads on the **bottom side** of the PCB

Bridge the pads with a small amount of solder. Verify with a multimeter in continuity mode that they are correctly bridged before proceeding.

---

## Soldering order

Always solder in this order. Components soldered out of order can be impossible to rework later.

### Step 1 — Power switches

Place the Alps SPDT power switch matching the footprint outline and solder all three legs. Switch orientation matters — match the silkscreen.

Verify with a multimeter: in the OFF position there should be no continuity between BAT+ and RAW.

### Step 2 — Reset buttons

Place the Panasonic SMD reset button matching the footprint and solder all four legs. No orientation required — it is symmetric.

### Step 3 — Mill-Max sockets

1. Insert sockets into the PCB from the **top side**
2. Place tape over the socket openings to prevent solder wicking into the pins
3. Solder from the **underside** of the PCB
4. Remove tape after soldering
5. Verify each socket is flush and not at an angle

Do not insert the Nice!nano yet.

### Step 4 — Battery

**Verify polarity with a multimeter before soldering.**

- Red wire = BAT+ (positive)
- Black wire = BAT- (negative / ground)

Measure wire length before cutting. The battery sits flat under the Nice!nano. Trim to just enough slack to tuck flat — leave a few mm of margin.

Solder red wire to BAT+ and black wire to BAT-. Double-check polarity one more time before proceeding.

### Step 5 — Switches

Solder all 17 switches per half. Press each switch firmly into the PCB until it clicks into place, then solder both pins.

Before soldering, verify each switch position by shorting the pads with tweezers — this saves desoldering later if you find a dead connection.

### Step 6 — Nice!nano

Insert the Nice!nano into the Mill-Max sockets:

- Components face **down**, toward the PCB
- USB-C port faces toward the **inner edge** of the board

Do not solder. The Nice!nano sits in the sockets and can be removed.

---

## Tenting

Start at 10–15 degrees tenting angle. Lock the Manfrotto angle firmly — the SKUF silicone feet prevent the board from sliding on your desk.

---

## Troubleshooting

**A key does not register**
Check the switch pins are fully soldered. Short the switch pads with tweezers — if the key registers, the switch itself is faulty.

**An entire row or column does not register**
Check the solder joints on the controller pads in that row or column.

**The keyboard does not power on**
Check the power switch orientation and verify the battery polarity.

**The halves do not connect wirelessly**
Ensure both halves are powered on. The left half is central — it connects to the computer. The right half connects to the left half. If pairing fails, clear the ZMK bonds and re-pair.

**Nice!nano not detected when flashing**
Double-tap the reset button quickly. If the bootloader does not appear, try a different USB-C cable — some cables are charge-only.
