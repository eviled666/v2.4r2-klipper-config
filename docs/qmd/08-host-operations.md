---
title: Host Operations — HeavensDoor Voron 2.4r2
updated: 2026-08-02
tags: [host, network, ssh, git, backup, monitoring, heavensdoor]
keywords: HeavensDoor, 172.16.10.68, Raspberry Pi, NetworkManager, SSH, git backup, autocommit, Moonraker, Mainsail, Prometheus, Loki
---

# Host Operations

## Host Identity and Network

- Hostname: `HeavensDoor`
- Role: Raspberry Pi Klipper host inside the Voron 2.4r2 350 mm printer
- Interface: `wlan0`
- Wi-Fi MAC: `dc:a6:32:94:3f:22`
- Static IPv4 address: `172.16.10.68/24`
- Gateway: `172.16.10.3`
- DNS: `10.133.7.1`
- Search domain: `lan.derphouse.org`
- NetworkManager profile: `CoolFridge Lounge`, IPv4 method `manual`

The Brocade VLAN 10 DHCP server excludes `172.16.10.1-99`, so `.68` is outside the dynamic pool. The static address is configured on the Pi; it does not depend on a DHCP reservation.

## SSH Access

The operational SSH account is `hermes` and uses a dedicated local key:

```bash
ssh -o IdentitiesOnly=yes \
  -i ~/.ssh/hermes_heavensdoor_voron_172_16_10_68_ed25519 \
  hermes@172.16.10.68
```

The `hermes` account can inspect the `edwin` printer data and manage NetworkManager through polkit. It does not have passwordless sudo and cannot write the `edwin`-owned live Git repository.

## Runtime Paths and Health Checks

- Printer data: `/home/edwin/printer_data/`
- Live repository: `/home/edwin/printer_data/config`
- Active config: `/home/edwin/printer_data/config/printer.cfg`
- Logs: `/home/edwin/printer_data/logs/`
- G-code storage: `/home/edwin/printer_data/gcodes/`
- Klippy socket: `/home/edwin/printer_data/comms/klippy.sock`
- Moonraker API: `http://172.16.10.68:7125`
- Mainsail/nginx: `http://172.16.10.68/`

```bash
systemctl is-active klipper moonraker
curl http://127.0.0.1:7125/printer/info
```

A healthy Moonraker response reports `state: ready` and `state_message: Printer is ready`. For CAN communication faults, inspect `klippy.log` for `bus_state`, `rx_error`, `tx_error`, and retry counters.

## Repository Backup

- GitHub repository: `git@github.com:eviled666/v2.4r2-klipper-config.git`
- Default branch: `master`
- Printer-side autocommit script: `/home/edwin/scripts/git-autocommit.sh`
- Hermes recovery clone: `/home/clawstache/workspace/heavensdoor-klipper-config`

The printer-side script stages all changes, creates an `Auto backup YYYY-MM-DD HH:MM:SS` commit, and pushes `master`. `mmu/mmu_vars.cfg` is runtime-generated state and commonly triggers these backups.

### 2026-08-02 Recovery

The live repository was ten commits ahead of GitHub because the `edwin` account's GitHub public-key authentication had failed since April 2026. The live `mmu/mmu_vars.cfg` also had an uncommitted MMU revision/statistics change.

Recovery steps:

1. Clone GitHub on the Hermes machine.
2. Fetch the printer's authoritative `master` directly over SSH.
3. Fast-forward through the ten printer-side commits.
4. Copy the live `mmu/mmu_vars.cfg` and compare SHA-256 checksums.
5. Commit the current MMU state using the existing auto-backup convention.
6. Push through the `clawstache` collaborator account.
7. Verify local `HEAD`, GitHub `master`, and `git ls-remote` agree.

Verified recovery commit:

- `e0abd220c012ea097c1208495a3800c6b9d8f145`
- <https://github.com/eviled666/v2.4r2-klipper-config/commit/e0abd220c012ea097c1208495a3800c6b9d8f145>

### Remaining Backup Risk

The GitHub branch is current through the recovery commit, but printer-side automatic pushes remain broken until `edwin` receives a valid GitHub or repository deploy key. Do not put a GitHub token in this public repository, the remote URL, the autocommit script, or its log.

Repair acceptance checks:

```bash
ssh -T git@github.com
git -C /home/edwin/printer_data/config fetch origin
git -C /home/edwin/printer_data/config status --short --branch
git -C /home/edwin/printer_data/config push origin master
```

A repair is complete only when the live checkout can push and the local and remote `master` SHAs match.

## Monitoring

Central monitoring on MoodyBlues targets Moonraker at `172.16.10.68:7125` with labels `printer=heavensdoor` and `location=print-room`.

- Grafana dashboard: `Klipper / HeavensDoor Overview`
- Dashboard UID: `klipper-heavensdoor-overview`
- Dashboard URL: `http://172.16.10.14:3300/d/klipper-heavensdoor-overview/klipper-heavensdoor-overview`

Because this Debian 11/aarch64 host cannot run newer Promtail builds and the `hermes` account has no sudo, logs are shipped to Loki by a user-space Python process:

```text
/home/hermes/.local/bin/run-heavensdoor-loki-shipper
```

It is persisted by an `@reboot` entry in the `hermes` user crontab.

## Suggested QMD Queries

- "HeavensDoor static IP and SSH key"
- "printer_data paths and Moonraker health check"
- "Klipper config Git backup recovery"
- "why HeavensDoor automatic git push fails"
- "HeavensDoor Grafana Loki monitoring"
