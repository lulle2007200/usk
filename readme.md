# RP2040 Switch Modchip Firmware

Firmware for RP2040-based Nintendo Switch modchips (e.g. Picofly).

## Overview

The initial payload (IPL) included with this firmware is able to load any `payload.bin`, up to a maximum size of **127.5 KB**.

A menu with various tools is available by holding **VOL+** while booting.

## Installation

The firmware should ideally be flashed via **USB**.

If you don't want to (or can't) flash via USB:

1. Place `update.bin` on the root of your SD card.
2. Launch `sdloader.bin` (e.g. via Hekate) while holding **VOL+**.
3. Use the included Toolbox to install the firmware (see `FW Update` below).

> **Note:** This *may* not work, depending on the currently installed firmware version. If it doesn't, the chip will simply stay on the current firmware.

### `no_extras` Flavour

An additional flavour (`no_extras` suffix) is provided. It does **not** include the UMS tool or the Toolbox, and should be installable regardless of the currently installed modchip firmware. Remember to rename to `update.bin` first.

Once installed, you should then be able to install the full version.

## Menu Reference

```
Power Off
Reboot OFW
More
├── UMS
│   ├── SD CFG
│   ├── GPP CFG
│   ├── BOOT0 CFG
│   ├── BOOT1 CFG
│   ├── Start
│   └── Reload
├── Launch Payload
└── Toolbox
    ├── FW Info
    ├── FW Reset
    ├── FW Update
    ├── FW Rollback
    ├── IPL Update
    ├── IPL Settings
    │   ├── Payload vol.
    │   ├── Boot action
    │   └── OFW Combo
    └── BL Update
```

### Top Level

| Item | Description |
|---|---|
| **Power Off** | Powers off the system. |
| **Reboot OFW** | Reboots the system and boots original firmware. |
| **More** | Opens additional options and features. |

### More

| Item | Description |
|---|---|
| **UMS** | USB Mass Storage tool, used to mount the SD card or eMMC. |
| **Launch Payload** | Launches `payload.bin`. If a payload volume isn't explicitly set (see IPL Settings), the chip searches for it in the root of a FAT32/exFAT partition in this order: BOOT1, BOOT1 (at a 1MB offset), SD card, eMMC. |
| **Toolbox** | Tools to manage firmware and settings. |

### UMS

Mounts storage devices over USB. Each storage device (SD, GPP/eMMC, BOOT0, BOOT1) has an identical set of config options.

| Item | Description |
|---|---|
| **SD CFG** | Configure if/how to mount the SD card. |
| **GPP CFG** | Configure if/how to mount eMMC (GPP). |
| **BOOT0 CFG** | Configure if/how to mount BOOT0. |
| **BOOT1 CFG** | Configure if/how to mount BOOT1. |
| **Start** | Starts the UMS tool. By default, only the SD card is mounted, as read/write. |
| **Reload** | Reinitializes eMMC and SD card. Use this after inserting an SD card. |

#### Storage Config Options (SD/GPP/BOOT0/BOOT1 CFG)

| Option | Description |
|---|---|
| **Mount** | `RW` (read-write), `RO` (read-only), or `--` (do not mount). |
| **Mode** | `Offset + Size` (mount a specific sector range) or `Part` (mount a specific partition number). By default, the entire storage device is mounted as a single disk. |
| **Part.** | The partition number to mount, when Mode is `Part`. |
| **Offset** | Start of the range to mount, in sectors, when Mode is `Offset + Size`. |
| **Size** | Size of the range to mount, in sectors, when Mode is `Offset + Size`. |
| **TSize** | Displays the total size of the selected storage device. |

### Toolbox

| Item | Description |
|---|---|
| **FW Info** | Shows info about the currently installed firmware. |
| **FW Reset** | Forces a rewrite of the initial payload and clears the persistent storage area on next boot. |
| **FW Update** | Updates the modchip firmware. Searches for `update.bin` in the root of a FAT32/exFAT filesystem, in this order: BOOT1, BOOT1 (1MB offset), SD card, eMMC. |
| **FW Rollback** | Reverts to the previous firmware. |
| **IPL Update** | Updates the initial payload. Searches for `payload.enc` in the root of a FAT32/exFAT filesystem, in this order: BOOT1, BOOT1 (1MB offset), SD card, eMMC. |
| **IPL Settings** | Change initial payload settings (see below). |
| **BL Update** | Updates the modchip bootloader. **Do not use this.** Searches for `bl_update.bin` in the root of a FAT32/exFAT filesystem, in this order: BOOT1, BOOT1 (1MB offset), SD card, eMMC. |

#### IPL Settings

| Option | Description |
|---|---|
| **Payload vol.** | Storage to load `payload.bin` from. One of: `auto` (search as described under Launch Payload), `SD`, `BOOT1(1MB)`, `BOOT1`, `GPP`. |
| **Boot action** | What to do on boot. One of: `Payload` (load `payload.bin`), `OFW` (boot original firmware), `Menu` (start the menu). |
| **OFW Combo** | Enable/disable the VOL+ + VOL- key combo to boot to original firmware. |

## Persistent Storage

`0x100` bytes at offset `0x100` in block `0x1FFF` of BOOT0 are persistent and will **not** be overwritten, unless the firmware is updated or a reset command is issued (`FW Reset`).

This region may be used by initial payloads to store persistent configuration.