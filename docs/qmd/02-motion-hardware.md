---
title: Motion Hardware — Voron 2.4r2
updated: 2026-04-19
tags: [motion, steppers, tmc, sensorless, kinematics]
keywords: stepper_x, stepper_y, stepper_z, tmc2209, sensorless homing, stallguard, SGTHRS, quad gantry level, rotation_distance, 0.9 degree
---

# Motion Hardware

## Kinematics
- Type: `corexy`
- Max velocity: 450 mm/s
- Max accel: 8000 mm/s²
- Max Z velocity: 30 mm/s, Max Z accel: 350 mm/s²
- Input shaper: X=2hump_ei @ 75.6 Hz, Y=mzv @ 42.4 Hz (`printer.cfg` SAVE_CONFIG block)

## X/Y Steppers (Mellow Spider main MCU)
| Axis | Step pin | Motor type | Run current | Sensorless SGTHRS |
|------|----------|-----------|-------------|-------------------|
| X (B motor) | PE11 | 0.9° (400 steps) | 1.2 A | 150 |
| Y (A motor) | PD8 | 0.9° (400 steps) | 1.2 A | 140 |

- X: sensorless homing via `tmc2209_stepper_x:virtual_endstop` (diag: PB14)
- Y: physical endstop at `^PA2`
- Both: `rotation_distance: 40`, `microsteps: 32`

## Z Steppers (4× TMC2209, 80:16 gear ratio)
| Stepper | Position | Step pin |
|---------|----------|----------|
| stepper_z | Front Left | PD14 |
| stepper_z1 | Rear Left | PE6 |
| stepper_z2 | Rear Right | PE2 |
| stepper_z3 | Front Right | PD12 |

- `rotation_distance: 40`, `gear_ratio: 80:16`, `microsteps: 16`, `run_current: 0.7 A`
- Z endstop: `probe:z_virtual_endstop` (Cartographer touch)
- Z max: 340 mm, `homing_retract_dist: 0`

## Quad Gantry Level
- Gantry corners: (-60,-10) / (410,420)
- Probe points: (20,5), (20,310), (320,310), (320,5)
- Speed: 400 mm/s, `retry_tolerance: 0.0055`, `retries: 5`
- Two-stage QGL: coarse pass (tolerance 1.0) then fine pass at `horizontal_move_z: 2.0` — `macros.cfg`

## Sensorless Homing
- Config in `sensorless.cfg`
- X SGTHRS: 150, Y SGTHRS: 140
- `homing_retract_dist: 0` for sensorless axes

## Electronics Cooling
- `electronics1` fan: PA13, host temp sensor, watermark at 30°C
- `electronics2` fan: PB2, host temp sensor, watermark at 30°C, shuts down at error

## Suggested QMD Queries
- "stepper motor current settings"
- "sensorless homing configuration SGTHRS"
- "quad gantry level tuning"
- "input shaper frequencies"
