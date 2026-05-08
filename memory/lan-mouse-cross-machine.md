---
name: Lan-Mouse Cross-Machine Setup
description: Working lan-mouse setup between Alia (macOS Sequoia) and Thufir (Ubuntu 24.04), with hard-won gotchas around macOS TCC, peer registration, and asymmetric modifier-key forwarding
type: project
---

# Lan-Mouse — Alia ↔ Thufir

Open-source keyboard/mouse share over LAN. Built from source on both machines (v0.10.0). Live setup as of 2026-05-08.

## Topology

- **Thufir** (Ubuntu 24.04, 192.168.10.174 / thufir.local) — sits **above** Alia
- **Alia** (macOS Sequoia, 192.168.10.135 / alia.local) — sits **below** Thufir
- Both bind UDP 4242 (peer-symmetric protocol; same port both ways)
- Mouse plugged into Alia. Single keyboard plugged into Thufir.

## What works

- Mouse on Alia drives both machines (cursor crosses up to Thufir, back down to Alia)
- Keyboard on Thufir drives both machines including modifier keys

## Hard-won gotchas

### macOS Sequoia TCC + ad-hoc-signed binaries (the killer)
Sequoia silently fails to persist Accessibility / Input Monitoring grants for bare ad-hoc-signed binaries added via the System Settings file picker. Pane shows the toggle on; `TCC.db` stays empty; CGEventTap creation fails with "event tap creation failed."

**Fix:** wrap as a proper `.app` bundle with stable bundle ID (`de.feschber.lan-mouse`) at `~/Applications/lan-mouse.app`. Grant Accessibility + Input Monitoring to the .app, not the binary. TCC then persists correctly.

### Peer must be registered on both sides
lan-mouse drops pings from unregistered sources silently — no log line, just no reply. If Alia → Thufir works at the network layer (tcpdump shows packets arriving) but no replies come back, **add the sender as a client in the receiver's GUI**. Each direction needs its own entry on the receiving machine's side.

### Modifier keys are asymmetric (Linux → macOS only)
- Thufir's keyboard, forwarded over lan-mouse to Alia: modifiers (Cmd/Ctrl/Opt/Shift) work correctly.
- Alia's keyboard, forwarded to Thufir: modifiers drop or remap.
- Working setup: single physical keyboard on Thufir, lan-mouse forwards to Alia. Don't try the reverse.
- Likely cause: Linux evdev exposes raw modifier state cleanly; macOS CGEventTap doesn't forward modifiers as cleanly outbound.

### macOS Mouse pane hides when no HID mouse is enumerated
Even when a USB mouse is physically plugged in, macOS may not surface the Mouse pane in System Settings if the HID descriptor doesn't expose `PrimaryUsage = 2`. Some Logitech receivers enumerate without registering as a pointing device until paired/woken.
- Verify via `ioreg -c IOHIDDevice -r | grep '"PrimaryUsage" = 2'` — empty means no mouse from macOS's POV.
- Workaround for scroll/etc. when pane is hidden: `defaults write -g com.apple.swipescrolldirection -bool false`, then logout.

### mDNS works regardless of DHCP search domain
Charter pushes `search.charter.net` as the DHCP search domain, making the FQDN look like `Alia.search.charter.net`. **mDNS (`Alia.local`) is unaffected** — LocalHostName lives in mDNSResponder, independent of unicast DNS. Use `.local` names in lan-mouse config; survives DHCP lease changes.

## Config

Alia `~/.config/lan-mouse/config.toml`:
```toml
port = 4242
release_bind = ["KeyA", "KeyS", "KeyD", "KeyF"]

[top]
hostname = "thufir.local"
activate_on_startup = true
port = 4242
```

Thufir: GUI-only, no on-disk config at `~/.config/lan-mouse/config.toml`. Alia must be added as a client in the GUI (position: bottom).

## Operational

- Launch Alia: `open ~/Applications/lan-mouse.app` (NOT the bare binary — TCC won't persist)
- Capture stderr: `RUST_LOG=debug open -W ~/Applications/lan-mouse.app --stdout /tmp/lan-mouse.log --stderr /tmp/lan-mouse.log`
- Kill all: `pkill -9 -f lan-mouse`
- Source clone: `~/src/lan-mouse` (tag v0.10.0)
- Rebuild: `. "$HOME/.cargo/env" && export PKG_CONFIG_PATH="/opt/homebrew/lib/pkgconfig:/opt/homebrew/share/pkgconfig" && cd ~/src/lan-mouse && cargo build --release`

## Diagnostic playbook

When connection breaks one direction:
1. `tcpdump -i <iface> -nn 'udp port 4242'` on the receiver — are packets arriving?
2. If yes but no replies: peer not registered in receiver's GUI.
3. If no: firewall (`ufw status` on Linux, macOS Firewall pane).
4. `pgrep -fl lan-mouse` and `ss -ulnp | grep 4242` (Linux) / `lsof -nP -iUDP:4242` (macOS) to confirm daemon is alive and bound.

Upstream issue tracker: lan-mouse #257 (TCC persistence on Sequoia, open).
