---
title: Probing & Leveling — Cartographer V3
updated: 2026-04-19
tags: [probe, cartographer, bed-mesh, z-offset, touch]
keywords: cartographer, scanner, bed mesh, CARTOGRAPHER_TOUCH, touch mode, z_virtual_endstop, backlash, KAMP, adaptive mesh, bicubic, 30x30
---

# Probing & Leveling

## Cartographer V3 Scanner Probe
- Config: `carto.cfg`
- MCU alias: `scanner`, CAN UUID: `c80bfdee3354`
- Mode: **touch** (set in SAVE_CONFIG block)
- Firmware: CARTOGRAPHER V3 6.1.0
- X offset: 0, Y offset: 15 (nozzle to coil center)
- Backlash compensation: 0.01380
- Touch threshold: 2500, touch speed: 3

## Bed Mesh
- Grid: 30×30 points, bicubic algorithm
- Range: X 30–310, Y 30–310 mm
- Zero reference: (175, 175) — bed center
- Speed: 400 mm/s, `horizontal_move_z: 5`
- Mesh runs: 2 passes for accuracy
- Adaptive mesh via KAMP (`KAMP/KAMP_Settings.cfg`)

## Z Homing
- `endstop_pin: probe:z_virtual_endstop` on all Z steppers
- `CARTOGRAPHER_TOUCH` called in PRINT_START after QGL and mesh
- Nozzle held at 150°C for touch (thermal stability, avoids Cartographer TOUCH offset issues)
- `SET_GCODE_OFFSET Z=0.0` at start of every print to clear babystep offset

## PID — Bed Heater
- Saved values: Kp=41.221, Ki=1.117, Kd=380.268
- Heater pin: PB4 (SSR), max_power: 0.60, sensor: Generic 3950 on PB0
- `verify_heater heater_bed`: max_error=180, check_gain_time=90

## Resonance Testing
- Chip: adxl345 on Cartographer MCU (SPI bus 1, PA3)
- Probe point: (175, 175, 20)

## Suggested QMD Queries
- "cartographer touch setup"
- "bed mesh probe count settings"
- "Z offset touch mode cartographer"
- "adaptive mesh KAMP"
