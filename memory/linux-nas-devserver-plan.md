# Linux NAS + Dev Server Plan
*Capture from Claude session — March 2026*

## Hardware
- **Mac Pro #1 (NAS)** — flashed 4,1→5,1, dual Xeon, 128GB RAM, 20+TB SATA bays, 1TB NVMe, dual NIC, Broadcom WiFi/BT chip
- **Mac Pro #2 (Dev Server)** — same chassis family, will run Proxmox as hypervisor

---

## Mac Pro #1 — TrueNAS SCALE (NAS)

### Why TrueNAS SCALE
- Purpose-built NAS OS, Debian-based
- ZFS native — 128GB RAM = enormous ARC cache, great for spinning rust
- BackBlaze B2 cloud sync built in (solves the dropped macOS support problem)
- Docker/Kubernetes app layer for Plex, Nextcloud, etc.
- SMART monitoring, scrubs, snapshots built in

### ZFS Pool Topology (decide based on drive count)
| Drives | Recommendation |
|--------|---------------|
| 2 | Mirror (RAID-1 equivalent) |
| 4–6 | RAIDZ2 (2 parity, good balance) |
| 8+ | Two RAIDZ2 vdevs, or RAIDZ3 |

### Install Notes
- Boot from 1TB NVMe (TrueNAS only uses ~16GB, rest sits idle — fine)
- SATA bays present as individual disks — ZFS sees them cleanly, no HBA flashing needed
- Flashed 5,1 firmware is the more Linux-cooperative EFI behavior
- If black screen on first boot: EFI/BIOS compatibility mode issue — use rEFInd shim
- Broadcom WiFi chip (BCM94360CD) supported via `brcmfmac`/`wl` — but use wired ethernet for NAS anyway

---

## Mac Pro #2 — Proxmox + Ubuntu VMs (Dev Server)

### Architecture
```
Mac Pro #2  →  Proxmox VE (bare metal hypervisor)
                ├── Ubuntu 24.04 LTS VM: Canvas LMS + LTI dev
                ├── Ubuntu 24.04 LTS VM: Mail server (when ready)
                └── Ubuntu 24.04 LTS VM: Staging / experiments
```

### Why Proxmox
- Snapshot before you break something, roll back instantly
- Safe blast radius for learning
- Can mount NFS storage from Mac Pro #1
- Standard cloud-init Ubuntu images spin up in minutes

### Why Ubuntu Server LTS for VMs
- Canvas install docs target Ubuntu directly — no translation tax
- Largest community support surface for Canvas, LTI, mail server docs
- Certbot/Let's Encrypt tooling is Ubuntu-first

### Canvas LMS Dependencies (for reference)
- Ruby (via rbenv)
- PostgreSQL
- Redis
- Node.js
- Passenger or Nginx

---

## Domain / DNS / Cert Consolidation

### The Problem
- Domains scattered across HostGator, Bluehost, unknown third host
- Paying for wildcard certs — unnecessary
- iPhone mail broken due to cert/hostname issues

### The Fix: Cloudflare DNS + Let's Encrypt

**Step 1 — Consolidate DNS to Cloudflare (free tier)**
- Create Cloudflare account
- Add each domain — Cloudflare scans and imports existing DNS records
- Update nameservers at HostGator/Bluehost to point to Cloudflare
- Nothing breaks, everything is now in one place
- Fast propagation (seconds, not hours)

**Step 2 — Free Wildcard Certs via Let's Encrypt**
- Use Certbot + `cloudflare-dns` plugin
- DNS-01 challenge — Certbot hits Cloudflare API, creates TXT record automatically
- Issues `*.yourdomain.com` wildcard cert
- Auto-renews every 90 days via cron/systemd timer
- **Cost: $0**

```bash
# Basic workflow (Ubuntu)
apt install certbot python3-certbot-dns-cloudflare
# configure /etc/letsencrypt/cloudflare.ini with API token
certbot certonly \
  --dns-cloudflare \
  --dns-cloudflare-credentials ~/.secrets/cloudflare.ini \
  -d "*.yourdomain.com" -d "yourdomain.com"
```

### iPhone Mail Fix
Root causes (in order of likelihood):
1. Self-signed or expired cert → fixed by Let's Encrypt wildcard
2. Wrong hostname — MX record pointing somewhere different than A record
3. IMAP/SMTP ports misconfigured in iOS profile

Once DNS is consolidated in Cloudflare and a valid cert is on the mail server, iOS stops complaining.

### Mail Server Warning
Mail is the hardest thing on this list. Plan for:
- SPF, DKIM, DMARC records
- Reverse DNS (rDNS) on your static IP
- Getting off blacklists
- Deliverability to Gmail

Options:
- **iRedMail** — packages the whole stack, faster start
- **Postfix + Dovecot + Rspamd** — more learning, more suffering, more understanding

Need a static IP with clean rDNS and a domain with no spam history.

---

## Saturday Afternoon Order of Operations

1. [ ] Inventory domains — list all registrars and what's hosted where
2. [ ] Create Cloudflare account, add all domains, flip nameservers
3. [ ] Install TrueNAS SCALE on Mac Pro #1
4. [ ] Determine drive count → choose ZFS topology
5. [ ] Configure BackBlaze B2 cloud sync in TrueNAS
6. [ ] Install Proxmox on Mac Pro #2
7. [ ] Spin up first Ubuntu VM, get comfortable with the workflow
8. [ ] Canvas install on Ubuntu VM (follow Instructure official docs)
9. [ ] Mail server (iRedMail recommended for first attempt)
10. [ ] Certbot + Cloudflare plugin for wildcard certs

---

## Reference Links
- TrueNAS SCALE: https://www.truenas.com/truenas-scale/
- Proxmox VE: https://www.proxmox.com/en/proxmox-ve
- Canvas install docs: https://github.com/instructure/canvas-lms/wiki/Quick-Start
- Certbot Cloudflare plugin: https://certbot-dns-cloudflare.readthedocs.io/
- iRedMail: https://www.iredmail.org/
