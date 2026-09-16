# datum — keymap reference

OS layout: **Danish**

---

## Thumb cluster

```
[ esc/SYM ]  [ spc/NAV ]  |  [ bspc/EXT ]  [ ent/MEDIA ]
```

| Combo | Layer |
|---|---|
| esc + spc | SETTINGS |
| spc + bspc | FN |

---

## Base layer — QWERTY Danish

```
q    w    e    r    t    |    y    u    i    o    p
a    s    d    f    g    |    h    j    k    l    å
z    x    c    v    b    |    n    m    æ    ø    ,
          esc/SYM  spc/NAV  |  bspc/EXT  ent/MEDIA
```

**Home row mods** (hold for modifier, tap for letter):

| Key | Hold |
|---|---|
| s | GUI (Super) |
| d | ALT |
| f | SHIFT |
| g | CTRL |
| h | CTRL |
| j | SHIFT |
| k | ALT |
| l | GUI (Super) |

---

## NAV — Navigation + Numbers

> Activate: hold **spc**

```
1    2    3    4    5    |    6    7    8    9    0
tab  home  ↑   end  pgup |  bspc   ←    ↓    →   del
esc                pgdn  |                        ret
          [  ]  [held]   |  [  ]   [  ]
```

---

## SYM — Symbols

> Activate: hold **esc**

```
!    @    #    $    %    |    ^    &    *    (    )
`    -    =    [    {    |    }    ]    +    _    |
~    \    <    >    /    |    ?    "    :    ;    .
         [held]  [  ]   |  [  ]   [  ]
```

---

## EXT — Extended navigation + modifiers

> Activate: hold **bspc**

```
esc                      |  pgup  home   ↑   end  caps
alt  gui  shft ctrl ralt |  pgdn   ←     ↓    →   del
               tab       |        bspc
          [  ]  ctrl     |  [held] [  ]
```

**Note:** Modifiers on the left hand are **sticky** — tap to apply to the next key.

---

## FN — Function keys

> Activate: hold **spc** + **bspc** simultaneously

```
F1   F2   F3   F4   F5   |   F6   F7   F8   F9   F10
F11  F12            caps |        mute vol- vol+
          [  ]  [  ]     |  [  ]   [  ]
```

---

## MEDIA — Media controls

> Activate: hold **ent**

```
                         |
     prev play next      |        mute vol- vol+
          [  ]  [  ]     |  [  ]  [held]
```

---

## SETTINGS — Bluetooth

> Activate: hold **esc** + **spc** simultaneously

```
boot          bt clr bt0 |  bt3        unstk       boot
                    bt1  |  bt4
                    bt2  |  bt5
          [  ]  [  ]     |  [  ]   [  ]
```

| Action | Key |
|---|---|
| Clear all bonds | bt clr |
| Select profile 0-5 | bt0 – bt5 |
| Unstick modifiers | unstk |
| Enter bootloader | boot |

---

## Notes

- `[ ]` = empty / transparent key (passes through to base layer)
- `[held]` = the key being held to activate this layer
- Sticky modifiers (EXT layer left hand) apply once and release automatically
