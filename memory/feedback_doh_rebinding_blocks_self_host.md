---
name: Browser Secure DNS (DoH) blocks self-hosted services with public-DNS / private-IP setups
description: Chrome/Edge "Secure DNS" filters RFC1918 IPs from public-hostname lookups (DNS rebinding protection). Self-hosted services on a LAN IP behind a public A record (like canvas.wilsonidt.com → 192.168.10.174) will silently fail to resolve.
type: feedback
---

When a self-hosted service uses a **public hostname** with an A record pointing to an **RFC1918 LAN IP** (e.g. `canvas.wilsonidt.com → 192.168.10.174`), browsers with Secure DNS / DNS-over-HTTPS enabled will fail to load the page — they look unreachable even though the network path is fine.

**Why:** DoH providers (Google Public DNS, Cloudflare, NextDNS, etc.) apply **DNS rebinding protection** — they strip private IPs (10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16) from responses to public hostnames. This is a real security feature, not a bug. But it breaks the common self-hosting pattern of "register a real domain, point it at a LAN IP, hit it from inside the LAN."

Observed 2026-05-10: From Alia (macOS), `https://canvas.wilsonidt.com` returned `ERR_ADDRESS_UNREACHABLE` in Chrome. Fix was disabling Chrome's Secure DNS setting — Chrome then deferred to the OS resolver, which doesn't filter. Diagnosed with help from `fabian@alia`.

**How to apply:**

- If a self-hosted service works from one device but not another on the same LAN, **first check whether Secure DNS / DoH is enabled in the failing browser** (Chrome: Settings → Privacy and security → Security → "Use secure DNS"; Firefox: about:preferences#privacy → DNS over HTTPS).
- Same applies to system-level DoH (macOS DNS-over-HTTPS profiles, iOS Encrypted DNS, Android Private DNS).
- Permanent fixes for this pattern:
  1. **Run a local DNS server** (Pi-hole, AdGuard Home, dnsmasq) that returns the LAN IP for the public hostname; configure clients to use it
  2. **Use a `.local` mDNS name + self-signed cert** — but Canvas / many web apps complain
  3. **Public-route everything** (port-forward + WAN A record + real cert) — defeats the point of LAN-only
- The Pi-hole project on Jefe's list (see `feedback_imscc_unpublished.md` siblings) would solve this — it'd be the canonical LAN DNS, intercept `canvas.wilsonidt.com`, and serve the LAN IP without anyone needing to flip browser settings.
