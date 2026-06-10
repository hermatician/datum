# datum — firmware guide

---

## Overview

datum uses ZMK firmware. The keyboard presents as the Cradio shield to ZMK.

Firmware is built automatically via GitHub Actions whenever you push changes to `config/`. No local toolchain needed — the `.uf2` files are downloadable directly from the Actions tab.

---

## Getting the firmware

1. Go to [github.com/hermatician/datum](https://github.com/hermatician/datum)
2. Click the **Actions** tab
3. Click the latest successful build run
4. Scroll to the bottom and download the **firmware** artifact
5. Unzip — you will find two files:
   - `cradio_left-nice_nano-zmk.uf2`
   - `cradio_right-nice_nano-zmk.uf2`

---

## Flashing

1. Connect the Nice!nano via USB-C
2. Double-tap the reset button to enter bootloader mode — a USB drive called **NICENANO** appears
3. Copy the `.uf2` file onto the drive
4. The controller reboots automatically

Flash the left half first using `cradio_left-nice_nano-zmk.uf2`, then the right half using `cradio_right-nice_nano-zmk.uf2`.

---

## Modifying the keymap

The keymap lives at `config/cradio.keymap` in the datum repo. Edit it and push — GitHub Actions will build new firmware automatically.

---

## Base layout — Gallium (columnar stagger variant)

datum uses the Gallium layout, optimised specifically for columnar stagger keyboards. Gallium is designed to minimise same-finger bigrams, reduce lateral stretch, and balance finger load — all of which matter more on a 34-key board than on a full-size keyboard.

```
b  l  d  c  v  |  j  y  o  u  ,
n  r  t  s  g  |  p  h  a  e  i
x  q  m  w  z  |  k  f  '  ;  .
          NAV SPC | BSPC SYM
```

Home row left: `n r t s` — the most common consonants in English, all on strong fingers.
Home row right: `p h a e i` — high frequency letters, all on strong fingers.

### Why Gallium for datum

- Lowest same-finger bigrams of the mainstream alt layouts on columnar stagger
- `h` on the vowel hand index breaks up vowel clusters — critical for Rust identifiers
- Comma and period on the right hand bottom row — natural for Rust's `,` and `..` syntax
- Hand balance close to 50/50 for extended sessions

### Learning Gallium

Gallium is a significant departure from QWERTY. Budget 2–4 weeks to reach comfortable typing speed. Use [Monkeytype](https://monkeytype.com) with a custom Gallium layout for daily practice.

A useful progression:
1. Learn home row first: `n r t s` / `p h a e i`
2. Add top row: `b l d c v` / `j y o u ,`
3. Add bottom row: `x q m w z` / `k f ' ; .`
4. Only then add layers and combos

---

## Layer design

### Base layer — Gallium

```
b  l  d  c  v  |  j  y  o  u  ,
n  r  t  s  g  |  p  h  a  e  i
x  q  m  w  z  |  k  f  '  ;  .
          NAV SPC | BSPC SYM
```

- Left thumb tap: **Space** / hold: **NAV layer**
- Right thumb tap: **Backspace** / hold: **SYM layer**

### Layer 1 — Navigation + Numbers

```
1    2    3    4    5    |  6     7     8     9    0
TAB  HOME UP   END  PGUP |  BSPC  LEFT  DOWN  RIGHT DEL
ESC  —    —    —    PGDN |  —     —     —     —    RET
              [held] SPC |  BSPC  FN
```

### Layer 2 — Symbols

```
!   @   #   $   %   |  ^   &   *   (   )
`   -   =   [   {   |  }   ]   +   _   ;
~   |   \   <   >   |  ::  "   '   :   ?
          NAV SPC   |  BSPC [held]
```

Note: `::` as a single key — typed constantly in Rust.

### Layer 3 — Function + System

```
F1  F2  F3  F4  F5  |  F6  F7   F8    F9   F10
F11 F12  —   —  CAPS|  —   MUTE VOLD  VOLU  —
BOOT —   —   —   —  |  —    —    —     —     —
          [held] SPC |  BSPC [held]
```

**BOOT** = ZMK bootloader mode for flashing. Both thumbs held activates this layer.

---

## Home row mods

On a 34-key board, modifiers live on the home row as tap-hold keys. This keeps your fingers on the home row at all times.

```
Left hand:   n=GUI  r=ALT  t=SHIFT  s=CTRL
Right hand:  p=GUI  h=ALT  a=SHIFT  e=CTRL
```

ZMK tap-hold settings used in datum:

```
tapping-term-ms: 200
flavor: balanced
require-prior-idle-ms: 150
```

The `require-prior-idle-ms` setting is the most important — it prevents accidental modifier activation during fast typing by requiring a brief pause before a hold registers.

---

## Combos

```
J + K simultaneously  →  ::
M + ,  simultaneously  →  ->
, + .  simultaneously  →  =>
```

These target the most common Rust-specific sequences that would otherwise require layer switching.

---

## Build details

| Property | Value |
|---|---|
| Shield | cradio (Ferris Sweep / Cradio) |
| Board | nice_nano//zmk (Nice!nano V2, Zephyr 4.1+) |
| ZMK revision | main |
| Build trigger | push to config/ or workflow_dispatch |

---

## Resources

- ZMK docs: [zmk.dev/docs](https://zmk.dev/docs)
- Gallium layout: [github.com/GalileoBlues/Gallium](https://github.com/GalileoBlues/Gallium)
- Monkeytype for practice: [monkeytype.com](https://monkeytype.com)
- datum repo: [github.com/hermatician/datum](https://github.com/hermatician/datum)
