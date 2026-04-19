---
title: Repo Overview — Voron 2.4r2 Klipper Config
updated: 2026-04-19
tags: [overview, hardware, voron, klipper]
keywords: voron 2.4r2, 350mm, klipper, corexy, spider, mmu, ercf, mainsail, crowsnest, KAMP
---

# Repo Overview

Klipper firmware configuration for a **Voron 2.4r2 350mm CoreXY** enclosed printer.

## Printer Identity
- Frame: Voron 2.4r2, 350 × 350 × 340 mm build volume
- Kinematics: CoreXY with 4-Z (quad gantry level)
- Main MCU: Mellow Spider (CAN bus, `canbus_uuid: 0c5a285e7f21`)
- Host MCU: Raspberry Pi (`/tmp/klipper_host_mcu`)
- Toolhead MCU: BTT EBB36 GEN2 (CAN, `canbus_uuid: 19638abde83a`)
- Probe MCU: Cartographer V3 (CAN, `canbus_uuid: c80bfdee3354`)
- MMU: ERCF v2, 12 gates, Happy Hare firmware

## Key Config Files
| File | Purpose |
|------|---------|
| `printer.cfg` | Master config — includes, motion, bed, fans, display |
| `macros.cfg` | PRINT_START, END_PRINT, utility macros |
| `ebb36v2.cfg` | EBB36 CAN toolhead board (extruder, fans, LEDs) |
| `carto.cfg` | Cartographer scanner probe + bed mesh settings |
| `mmu/base/mmu.cfg` | MMU MCU and pin aliases |
| `mmu/base/mmu_hardware.cfg` | MMU steppers, sensors, LEDs |
| `mmu/base/mmu_parameters.cfg` | Happy Hare tuning parameters |
| `mmu/base/mmu_macro_vars.cfg` | MMU macro variables |
| `mmu/addons/blobifier.cfg` | Blobifier purge tower |
| `mmu/addons/mmu_erec_cutter.cfg` | EREC filament cutter |
| `mmu/addons/mmu_eject_buttons.cfg` | MMU eject buttons |
| `moonraker.conf` | Moonraker API server, update managers |
| `crowsnest.conf` | Camera streaming (Crowsnest) |
| `KlipperScreen.conf` | KlipperScreen UI settings |
| `nevermore.cfg` | Nevermore carbon filter fan |
| `topleds.cfg` | Top LED strip |
| `stealthburner.cfg` | Stealthburner toolhead config (reference) |
| `sensorless.cfg` | Sensorless homing tuning |
| `sonar.conf` | Moonraker sonar (update check) |

## Symlinked External Repos
- `KAMP/` → `~/Klipper-Adaptive-Meshing-Purging/Configuration`
- `mainsail.cfg` → `~/mainsail-config/mainsail.cfg`
- `timelapse.cfg` → `~/moonraker-timelapse/klipper_macro/timelapse.cfg`

## MMU Backup Snapshots
`mmu-YYYYMMDD_HHMMSS/` directories are timestamped Happy Hare config snapshots. Active config is `mmu/`.

## Suggested QMD Queries
- "printer hardware overview voron"
- "which config files are included"
- "mmu snapshot backup directory"
