---
title: MMU Config — Happy Hare ERCF v2 12-gate
updated: 2026-04-19
tags: [mmu, ercf, happy-hare, filament, multicolor]
keywords: ERCF, happy hare, MMU, 12 gate, blobifier, erec cutter, mmu_hardware, mmu_parameters, mmu_macro_vars, pre-gate sensor, post-gear sensor, toolhead sensor, sync feedback, mmu_vars
---

# MMU Config — Happy Hare ERCF v2

## Identity
- Vendor: `ERCF`, Version: `2.0`
- Gates: 12
- Config directory: `mmu/`
- Key files:
  - `mmu/base/mmu.cfg` — MCU defs and pin aliases
  - `mmu/base/mmu_hardware.cfg` — steppers, sensors, LEDs
  - `mmu/base/mmu_parameters.cfg` — Happy Hare tuning
  - `mmu/base/mmu_macro_vars.cfg` — macro variables
  - `mmu/addons/blobifier.cfg` + `blobifier_hw.cfg` — purge blob system
  - `mmu/addons/mmu_erec_cutter.cfg` + `_hw.cfg` — EREC filament cutter
  - `mmu/addons/mmu_eject_buttons.cfg` + `_hw.cfg` — eject buttons
  - `mmu_vars.cfg` — calibration state (auto-updated by Happy Hare)

## Steppers
| Stepper | run_current | Mode |
|---------|------------|------|
| mmu_gear | 1.2 A | spreadcycle (stealthchop=0) |
| mmu_selector | 0.4 A | stealthchop threshold=100 |

- Gear: `rotation_distance: 22.7316868`, gear_ratio 1:1, microsteps 16
- Selector: `rotation_distance: 40`, endstop: `^mmu:MMU_SEL_ENDSTOP`
- Selector servo: `mmu:MMU_SEL_SERVO`

## Filament Sensors
- Pre-gate sensors: gates 0–11 (`MMU_PRE_GATE_0` … `MMU_PRE_GATE_11`)
- Post-gear sensors: gates 0–11 (`MMU_POST_GEAR_0` … `MMU_POST_GEAR_11`)
- Gate sensor: `^mmu:MMU_GATE_SENSOR`
- Extruder entry: `EBB:PD0`
- Toolhead: `EBB:PA15`
- Sync feedback: tension=PA1, compression=PA3

## Encoder
- Pin: `^mmu:MMU_ENCODER`, resolution: 1.0 (calibrated, stored in `mmu_vars.cfg`)
- Clog headroom: 5.0 mm, average samples: 4

## LEDs
- Neopixel `mmu_leds`: 13 LEDs (12 gates exit + 1 status), GRBW
- Exit: gates 12→1 (reversed), status: LED 13
- Enabled: False at startup (controlled via `MMU_LED`)
- Effects: blue clockwise (loading), anticlock (unloading), red breathing (heating), rainbow (init), strobe (error)

## Addons
- **Blobifier**: purge blob at fixed position (no purge tower)
- **EREC Cutter**: cuts filament at toolhead on toolchange
- **Eject Buttons**: physical gate eject buttons

## Moonraker Integration (`moonraker.conf`)
- `[mmu_server]`: file preprocessor enabled, toolchange next pos enabled, Spoolman location update enabled

## Backup Snapshots
Timestamped `mmu-YYYYMMDD_HHMMSS/` dirs are saved by Happy Hare before updates. Latest: `mmu-20260405_103745/`.

## Suggested QMD Queries
- "MMU gate count happy hare"
- "pre-gate sensor pins"
- "blobifier purge MMU"
- "EREC cutter toolchange"
- "mmu_vars calibration"
