# Live Music Streaming Production — Reference Doc

**Context:** Monthly 4-hour live music event. Currently A1 with 4-camera IMAG to LED wall. Goal: legally stream, monetize via sponsorships/ads/tickets, build a producer brand around it. Self-owned distribution, no platform dependency on Twitch/YouTube to avoid Content ID and DMCA takedowns.

---

## 1. Legal & Licensing

### The two rights layers

| Right | What it covers | When triggered |
|---|---|---|
| **Composition** (song itself) | Melody, lyrics, structure | Any performance — covers included |
| **Sound recording** (master) | The specific recorded version | Only if recorded music is used (walk-on tracks, samples, backing tracks) |

Cover performances need composition rights only. If the show is purely live with no recorded music in the stream, sound recording rights / SoundExchange are not in play.

### The four PROs (US)

All four needed because covers will hit all of them across a typical setlist.

- **ASCAP** — broad pop/rock/jazz catalog
- **BMI** — country, R&B, hip-hop, rock
- **SESAC** — invitation-only; includes Bob Dylan, Adele, Neil Diamond
- **GMR** — Pharrell, Lennon estate, Drake, Springsteen, Smokey Robinson

### License product needed

**Audio-Visual Streaming License** (ASCAP) / **Music in Streaming License** (BMI). SESAC and GMR negotiated directly — no public rate cards.

Pricing typically % of revenue (~1.7%–5%) with annual minimums. Rough total minimum across all four for a small commercial webcaster: **$2,500–$5,000/year**, scaling with revenue.

### What PRO licenses do NOT cover

- **Sync rights** (music paired with video, especially for on-demand replay/archive). Live transmission is generally interpreted as covered; permanent archives and clip distribution require song-by-song sync clearance from publishers — expensive, sometimes refused.
  - *Practical workaround:* Stream live and ephemeral. Keep recordings internal/archival only. Promo clips kept short.
- **Venue's live performance license** does not cover the simultaneous stream. Streaming is a separate "public performance by digital transmission."

### Action plan

1. Call ASCAP and BMI business licensing lines. Describe operation honestly (event frequency, attendance, projected stream viewers, revenue model). Get written quotes.
2. Then SESAC and GMR. Tell them you're already in conversation with the others.
3. Music attorney reviews any contract before signing.
4. Build setlist reporting into the A1 workflow — PROs require usage reporting from licensed streamers so writers get paid correctly.
5. Optional: licensing aggregator/broker if the multi-PRO admin overhead becomes a drag.

### Risk math

Statutory damages for unlicensed public performance: **$750–$30,000 per work** (up to $150,000 for willful infringement). One claim eclipses years of clean licensing fees.

---

## 2. Streaming Tech Stack

### Goal

Self-owned pipeline with no automated content matching. Platform absorbs no licensing risk — *you* hold the licenses, infrastructure stays neutral.

### Recommended stack (minimum viable)

```
OBS (already in workflow)
  ↓ RTMP push
Bunny.net Stream  ← ingest + transcode + HLS + global CDN
  ↓ signed HLS URL
Video.js / HLS.js player
  ↓ embedded in
Your own web app (Next.js)
```

### Why this works

- **Bunny.net Stream** and **Cloudflare Stream** do not run automated audio fingerprinting. Their TOS still require you to have rights — which you will, via PROs.
- Cost ballpark: **$50–$150 per event** in delivery for ~1,000 concurrent HD viewers on Bunny.
- Scales horizontally without re-architecting.

### Alternative origin servers (if self-hosting ingest)

| Tool | Notes |
|---|---|
| Nginx + nginx-rtmp-module | Free, battle-tested, most common |
| MediaMTX | Modern, multi-protocol |
| Ant Media Server (Community/Enterprise) | Better scaling, paid tier for production features |
| OvenMediaEngine | Free, low-latency focused |

A single VPS chokes around ~50 concurrent viewers — CDN is non-negotiable past that.

### What you give up vs. YouTube/Twitch

- No platform-driven discoverability (must drive own audience)
- No built-in monetization tools (must build own ads/tickets/donations)
- Real per-event delivery cost (vs. free hosting)

For a sponsor-and-ticket producer model, this is the right tradeoff. The audience and data are yours.

---

## 3. Custom App Tech Stack

### Goal

Membership/ticketing site with gated player, invite system, sponsor ad serving, analytics. This is a standard SaaS pattern — well-trodden.

### Stack

| Layer | Choice | Why |
|---|---|---|
| Frontend + backend | **Next.js** | Single project, React-based, mature ecosystem |
| Database + auth | **Supabase** (Postgres) | Auth, DB, storage in one. Generous free tier. |
| Payments | **Stripe** | Industry standard. Subscriptions, one-time tickets, refunds. |
| Email | **Resend** or **Postmark** | Transactional invites, receipts, notifications. Good deliverability. |
| Video delivery | **Bunny.net Stream** | Signed URLs for gating. |
| Player | **Video.js** or **HLS.js** | Free, embeddable, full control. |
| Analytics | **PostHog** | Open source, session recording, funnels, feature flags. |
| Hosting | **Vercel** or **Railway** | Drop-in Next.js deployment. |

Total infra cost at small scale: **$20–$50/month** + per-event delivery.

### Core entities (rough schema)

```
users               — auth identity, profile, role
events              — date, title, stream URL, status
tickets             — user_id, event_id, tier, purchased_at, stripe_id
invites             — code, created_by, used_by, expires_at, tier
view_sessions       — user_id, event_id, started_at, ended_at, peak_quality
sponsors            — name, logo, contract_value, slot_count
sponsor_placements  — sponsor_id, event_id, slot_type, scheduled_at
ad_impressions      — placement_id, user_id, served_at, completed
```

### Build phases

| Phase | Scope | Time |
|---|---|---|
| **1 — MVP** | Auth + signed-URL stream gating + Stripe one-time tickets + invite codes | 2–3 weekends |
| **2** | Subscriptions, ticket tiers, basic analytics dashboard, sponsor logo overlay | ~1 month |
| **3** | Pre-roll/mid-roll sponsor ads, viewer chat, email automation, replay access (sync rights question lives here) | ~2 months |
| **4** | Whatever audience and sponsors actually ask for — resist building in advance | Ongoing |

### Ad serving — two paths

- **Direct sponsor model** (recommended for start): rotate sponsor video files via own logic — pre-roll, between sets, lower-thirds. Sell directly, keep 100%, simple.
- **Ad network**: Google Ad Manager, Publica, SpringServe, Primis. More inventory, more complexity, takes a cut. Defer until volume justifies.

### Reference codebases

- Cal.com — production-quality Next.js + auth + Stripe
- Documenso — Next.js + Supabase + multi-tenancy patterns
- Plane — open source SaaS reference
- Supabase + Next.js SaaS starters (many on GitHub)

---

## 4. The Honest Bottleneck

Tech is the easy lift. The actual producer job:

- Booking sponsors
- Growing an audience
- Managing PRO relationships and reporting
- Customer service, refunds, edge cases
- Email deliverability hygiene

The app and stream pipeline support that work. They don't replace it.

---

## 5. Open Questions to Resolve

- Will any recorded music play during the stream (walk-ons, between-set music, backing tracks)? Determines whether SoundExchange/sound recording rights enter the picture.
- Archive strategy: live-only, time-limited replay window, or permanent archive? Drives sync rights conversation.
- Single-event tickets vs. monthly subscription vs. season pass vs. tiered (free + paid VIP)?
- Geographic scope of stream — US only simplifies licensing significantly vs. global.
- Sponsor inventory model — flat sponsor packages per event, or impression-based?
