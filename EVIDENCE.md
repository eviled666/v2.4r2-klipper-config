# HeavensDoor issue 4 evidence

| ID | Requirement | Source | Acceptance check | Implementation | Evidence | Status |
|---|---|---|---|---|---|---|
| R-HH-001 | Happy Hare must exclusively own PAUSE, RESUME, and CANCEL_PRINT while preserving Mainsail-required Klipper infrastructure | Ed approval; Happy Hare Installation/client macros | Klipper config loads; runtime exposes BASE_PAUSE/BASE_RESUME/BASE_CANCEL_PRINT rather than Mainsail's PAUSE_BASE chain | `printer.cfg`, `mainsail-base.cfg` | Offline Klippy load completed for 20 seconds with no config/template exception; live runtime deferred because printer power `spider` is off | pass/offline; runtime pending |

## Scope

- Do not alter `sensorless.cfg` or issue 1 behavior.
- Do not edit upstream/read-only `mainsail.cfg` or Happy Hare client macros.
- Replace the active `mainsail.cfg` include with a minimal non-conflicting infrastructure include.

## 2026-08-03T18:20:55-04:00 R-HH-001 — Happy Hare client macros selected

- Scope: changed active `printer.cfg`; added `mainsail-base.cfg`; left `sensorless.cfg`, `mainsail.cfg`, and Happy Hare files untouched.
- Checks:
  - Read back deployed files and compared SHA-256 hashes with staged files.
  - Inspected remote `git diff`: only `printer.cfg` modified and `mainsail-base.cfg` added.
  - Ran Klippy against a writable copy of the complete active config for 20 seconds.
  - Queried Moonraker device power state.
- Result: **PASS (offline configuration); RUNTIME BLOCKED**.
- Evidence:
  - Klippy reached `Starting Klippy...` / `Start printer` and ran until the deliberate 20-second timeout (`124`) with no config, template, or unhandled exception.
  - `printer.cfg`: `1262abf3be904c54ab2041702c9c64439bcb616c41ac6af7a13af4f282ea6c88`.
  - `mainsail-base.cfg`: `ce7cba46382b266ba9b664cd9b37e04d10e431c570e7d38dbd2374578b259e5e`.
  - `sensorless.cfg` remained `b2b868eb51c4e54ac329e23f61fd1c4416e5aac2ed07a98c14904d663584d9a2`, matching the reviewed snapshot.
  - Moonraker reports power device `spider` as `off`; `klipper.service` was already inactive before the change.
- Risks/limits: active runtime macro names and physical pause/resume/cancel behavior cannot be checked until the printer is powered on. The printer was not powered on because this config-only request did not authorize changing its physical power state.
- Next: on the next normal power-on, confirm Klipper reaches `ready`, check for `BASE_PAUSE`, `BASE_RESUME`, and `BASE_CANCEL_PRINT`, then perform controlled pause/resume and cancel tests.
