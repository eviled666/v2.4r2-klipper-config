---
title: Moonraker & Services — Voron 2.4r2
updated: 2026-04-19
tags: [moonraker, services, updates, notifications, crowsnest, timelapse]
keywords: moonraker, mainsail, timelapse, crowsnest, discord webhook, update_manager, octoprint_compat, power, gpio, spider, KlipperScreen, KAMP, cartographer, happy-hare, led_effect
---

# Moonraker & Services

## Moonraker Server (`moonraker.conf`)
- Host: 0.0.0.0, port 7125
- Klippy socket: `/home/edwin/printer_data/comms/klippy.sock`
- Trusted networks: 172.16.0.0/16, 10.0.0.0/8, 192.168.0.0/16 (local networks)
- API key enabled

## Power Control
- `[power spider]`: GPIO 26 (active-low), controls Spider MCU power
  - Off on shutdown, locked while printing, restarts Klipper when powered, 4s restart delay

## Update Managers
| Name | Type | Branch/Channel |
|------|------|---------------|
| mainsail | web | stable |
| timelapse | git_repo | main |
| Klipper-Adaptive-Meshing-Purging | git_repo | dev |
| led_effect | git_repo | — |
| cartographer | git_repo | stable |
| happy-hare | git_repo | main |

- Global update channel: `dev`
- Refresh interval: 168 hours (weekly)

## Notifications
- Discord webhook: `[notifier discord]` (all events: started, complete, error, cancelled, paused, resumed)
- Webcam snapshot attached: `http://172.16.20.122/webcam/?action=snapshot`

## Timelapse
- Output: `~/timelapse/`, frames: `/tmp/timelapse/`
- FFmpeg: `/usr/bin/ffmpeg`

## OctoPrint Compat
- UFP enabled (Cura uploads), webcam stream: `http://172.16.20.122/webcam/?action=stream`

## MMU Server
- `[mmu_server]`: file preprocessor on, toolchange next-pos tracking, Spoolman location update

## Camera (`crowsnest.conf`)
- Crowsnest streaming; two config files exist (`crowsnest.conf`, `crowsnest.conf1`)

## KlipperScreen
- Config: `KlipperScreen.conf`

## Suggested QMD Queries
- "moonraker update manager list"
- "discord notification webhook"
- "power control gpio spider MCU"
- "timelapse output path"
- "webcam stream URL"
