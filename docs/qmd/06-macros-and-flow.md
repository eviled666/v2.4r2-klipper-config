---
title: Macros & Print Flow — Voron 2.4r2
updated: 2026-04-19
tags: [macros, print-start, print-end, gcode, flow]
keywords: PRINT_START, END_PRINT, WIPE_NOZZLE, QGL, BED_MESH_CALIBRATE, CARTOGRAPHER_TOUCH, heatsoak, chamber temp, FINAL_HEAT, MMU, M109, M190, PA_CAL, TEST_SPEED, G32
---

# Macros & Print Flow

All macros live in `macros.cfg` unless noted.

## PRINT_START
- Required params: `BED=<temp> EXTRUDER=<temp>`
- Optional: `CHAMBER=<temp>` (default 45), `SOAK_MS=<ms>` (default 0), `FINAL_HEAT=0|1` (default 0)
- Flow:
  1. `SET_GCODE_OFFSET Z=0.0` (clear babystep)
  2. Conditional home (`_CG28`)
  3. `BED_MESH_CLEAR`, preheat nozzle to 100°C
  4. Heat bed to target
  5. If `BED > 90`: heatsoak path — part fan on, Nevermore on, wait for chamber sensor
  6. If `BED ≤ 90`: PLA path — bed temp + `SOAK_MS` dwell
  7. Part fan off, nozzle to 150°C for probing
  8. `QUAD_GANTRY_LEVEL`, `G28 Z`
  9. `BED_MESH_CALIBRATE` (KAMP adaptive)
  10. `WIPE_NOZZLE`
  11. `CARTOGRAPHER_TOUCH`
  12. If `FINAL_HEAT=1`: heat to target extruder temp (skip for MMU — MMU does it after loading initial tool)

## END_PRINT
- Safe Z lift (capped at Z max - 2)
- Park: center X (175), front Y (5)
- Fan cooldown: 20s at full blast then off via `_END_PRINT_FAN_OFF` delayed gcode
- `TURN_OFF_HEATERS`, `M84`
- Optional LED hooks: `STATUS_OFF`, `STATUS_READY`, `SET_LED_EFFECT topleds_complete`

## WIPE_NOZZLE
- Brush position: X 75–115, Y 360, Z 5 (hardcoded defaults)
- 2 passes × 6 strokes at 120 mm/s
- Requires hotend ≥ 140°C (raises error otherwise)
- All params overridable: `X_START`, `X_END`, `Y`, `Z`, `SPEED`, `PASSES`, `STROKES`

## QUAD_GANTRY_LEVEL (overridden)
- Renamed original to `_QUAD_GANTRY_LEVEL`
- Two-stage: coarse pass (tolerance 1.0) if not yet applied, then fine pass at `HORIZONTAL_MOVE_Z=2.0`

## M109 / M190 (overridden)
- Both renamed from originals (`M99109` / `M99190`)
- M109: uses `TEMPERATURE_WAIT` within ±1°C
- M190: uses `TEMPERATURE_WAIT` within +0/+4°C (bed overshoots)

## G32
- `BED_MESH_CLEAR` → `G28` → `QUAD_GANTRY_LEVEL` → `G28Z` → move to center (175,175,30)

## Utility Macros
| Macro | Purpose |
|-------|---------|
| `_CG28` | Conditional home (skips if already homed) |
| `FAKE_POSITION` | Force-sets kinematic position to (10,10,10) |
| `PA_CAL` | Pressure advance calibration print (20 segments) |
| `TEST_SPEED` | Speed/accel stress test with position verification |
| `_TOOLHEAD_PARK_PAUSE_CANCEL` | Park helper for PAUSE/CANCEL_PRINT |

## Slicer Setup Notes
- Use `PRINT_START BED={bed_temperature} EXTRUDER={nozzle_temperature}` in slicer start gcode
- For MMU prints: set `FINAL_HEAT=0` — MMU macro calls `M109` after loading first tool
- `END_PRINT` should be paired with Happy Hare `MMU_END` in slicer end gcode

## Suggested QMD Queries
- "PRINT_START parameters chamber soak"
- "END_PRINT park position"
- "wipe nozzle brush location"
- "pressure advance calibration macro"
- "MMU FINAL_HEAT slicer setup"
