---
title: Toolhead & Extruder — EBB36 GEN2
updated: 2026-04-19
tags: [toolhead, extruder, ebb36, canbus, led, stealthburner]
keywords: EBB36, CAN bus, extruder, PT1000, orbiter, rotation_distance, neopixel, jw_leds, led_effect, hotend fan, part fan, lis2dw
---

# Toolhead & Extruder

## MCU: BTT EBB36 GEN2
- Config: `ebb36v2.cfg`
- CAN UUID: `19638abde83a`
- Chip: STM32G0B1, CAN on PB12/PB13

## Extruder
- Step/dir/enable: EBB:PB14 / EBB:PA8 / EBB:PB6
- `rotation_distance: 4.637` (direct drive, likely Orbiter/LGX-lite style)
- Microsteps: 16, nozzle: 0.4 mm, filament: 1.75 mm
- Heater: EBB:PB4
- Sensor: PT1000, `pullup_resistor: 2200`, pin EBB:PA3
- PID (saved): Kp=25.844, Ki=2.101, Kd=79.472
- TMC2209: `run_current: 0.650`, stealthchop enabled

## Fans
| Fan | Pin | Notes |
|-----|-----|-------|
| Part cooling | EBB:PA5 | `[fan]` |
| Hotend fan | EBB:PD3 | kicks on at 50°C extruder temp, 60% speed |

## Accelerometer
- LIS2DW on EBB36 (software SPI: SCLK=EBB:PB10, MOSI=EBB:PB11, MISO=EBB:PB2)
- Used for resonance testing

## Filament Sensors (MMU-connected)
- Extruder entry: `EBB:PD0` → `extruder_switch_pin` in `mmu_hardware.cfg`
- Toolhead: `EBB:PA15` → `toolhead_switch_pin` in `mmu_hardware.cfg`

## Toolhead LEDs — `jw_leds` Neopixel
- Pin: EBB:PC7, chain count: 3, GRBW
- LED 1,2: Nozzle LEDs | LED 3: Logo LED
- Effects defined in `ebb36v2.cfg`:
  - `STATUS_HOMING`, `STATUS_HEATING`, `STATUS_LEVELING`, `STATUS_MESHING`, `STATUS_PRINTING`, `STATUS_READY`, `STATUS_OFF`
  - `jw_logo_*` and `jw_nozzle_*` named effects (breathing animations)
  - `jw_critical_error`: red strobe on error (`run_on_error: true`)
  - `rainbow`: autostart on boot

## Chamber Sensor
- NTC 100K MGB18-104F39050L32, pin: PC3, gcode_id: C (`printer.cfg`)

## Suggested QMD Queries
- "extruder rotation distance EBB36"
- "toolhead LED status effects"
- "PT1000 sensor configuration"
- "filament sensor toolhead extruder"
