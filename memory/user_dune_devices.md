---
name: Dune device naming
description: Current device map across Jefe's household — Dune naming convention
type: user
---

# Dune Device Map

Jefe names all devices after Dune characters. **Shai-Hulud is a species name, not an individual** — there are many sandworms in Dune, so multiple devices may carry the name Shai-Hulud and that's lore-correct, not a collision. Individual-character names (Alia, Muad'Dib, Gurney Halleck, Gom Jabbar, etc.) stay unique to one device. Disambiguate Shai-Huluds in conversation by device type, not by inventing new names.

This memory supersedes any older device list.

## Macs

- **Shai-Hulud (M5 MacBook Pro)** — portable. Main travel/anywhere dev machine. Hostname `Shai-Hulud` at all macOS layers. Sync drive: **Stilgar** (1TB external, USB).
- **Alia (M1 Mac mini)** — office. Driving two 4K monitors. Bonjour name `alia.local`; system hostname currently still `Macmini` (not renamed at OS level). Sync drive: **Ghola**.

Both have `~/sync` symlinked to their local sync drive's `Sync/` directory. Paths in memory and scripts (`~/sync/Documents/ClaudeCode/...`) resolve identically on either machine. See `feedback_sync_symlink.md`.

## Servers

- **Thufir** — 2009 Mac Pro flashed to 5,1, now Ubuntu 24.04 LTS dev/server box. SSH `jeff@thufir.local`. RX 560 GPU.
  - **Foshay** — VM running on Thufir. Hosts Docker + Canvas backend services. Dev sandbox for the self-testing Claude pipeline.
- **Muad'Dib** — 2009 Mac Pro converted to TrueNAS SCALE 25.10 storage server. Web UI http://192.168.10.179 or muadib.local. Pools: `tabr` (5TB mirror), `jacurutu` (12TB stripe). Boot pool mirrored, 2FA via Duo, SSH key-only as `truenas_admin`.

## iPads & iPhone

- **Shai-Hulud (12" iPad Pro gen 1)** — teleprompter. Also a sandworm.
- **Gom Jabbar (M3 iPad Air)** — active. Old memory listed this name as a retired Intel i9 Mac — that's wrong; the name was reassigned.
- **Gurney Halleck (iPhone 13)** — phone.

## Peripherals & Other

- **Stilgar** — 1TB external sync drive for the M5 Shai-Hulud.
- **Ghola** — sync drive for Alia (M1 mini).
- **Mentat** — AirPods.
- **Doctor Yueh** — LRC thumb drive. Other thumb drives also have Dune character names (not all catalogued).

## Retired

- **Old Shai-Hulud** — 2010 Mac Pro (flashed to 5,1, Metal, 128GB RAM, 27TB spinning disks). Retired. (Another sandworm.)

## Notes on Speech-to-Text Artifacts

Jefe sometimes captures notes via dictation. STT can mangle Dune names — e.g. "Topher" appeared in a 2026-05-14 triage note and was an STT ghost (likely "Foshay"). If an unfamiliar name shows up in dictated notes, do not invent a device for it — confirm with Jefe first.
