---
name: Splat Business
description: Mac-native Insta360-to-Gaussian-splat app for real estate. Summer 2026 build
type: project
---

Jeff is building a Mac-native Gaussian Splatting app for Insta360 X4
owners, beachhead market real estate agents. Service-first business
(Jeff captures + delivers), software layered on. Full summer 2026 push.

**Working docs in /Volumes/Stilgar/Sync/Documents/ClaudeCode/splat-business/:**
- `STRATEGY.md` — Business strategy, market, pricing, names/TLDs
- `TECHNICAL.md` — Open source ecosystem, dependencies, architecture
- `XCODE-LAYOUT.md` — Swift project structure, 30-day build progression

**Market gap:** as of 2026, no US-based professional Gaussian splatting
service exists. International competitor is Visualskies (UK/EU/NYC).
Zillow has SkyTours but vendor-locked; Matterport is mesh-based and
architecturally slow to pivot.

**Key technical discoveries:**
- Insta360 ships official open-source SDK
  (Insta360Develop/Desktop-MediaSDK-Cpp)
- msplat (rayanht/msplat) trains splats on M4 Max in ~70 sec —
  comparable to RTX 4090 CUDA. Apple Silicon is viable, not a
  compromise
- MetalSplatter is the de facto Metal splat renderer for Apple
  platforms
- Apple invested: SHARP model, LGTM research, visionOS 26 spatial

**Product vision:** "iMovie for Gaussian splats." Plug Insta360, drag
clips into rooms, hit splat button. Output: web tour + Vision Pro
bundle + PLY. User never sees COLMAP or gsplat parameters.

**Service pricing:** $300-1500 per property depending on size and
luxury tier. Solo operator at half capacity: $90K-175K/year.

**Domain strategy:** avoid .af (Taliban-controlled registry, business
risk). Safe characterful TLDs: .at, .ai, .io, .house, .estate, .app.
Candidates: SplatHaus, spl.at, splat.house. Cloudflare Registrar for
at-cost pricing.

**Build phases:**
1. Months 1-2: CLI + service business (Bekins as test data)
2. Months 3-4: Swift app alpha
3. Months 5-6: Polish, ship indie/shop SKUs
4. Year 2: SaaS hosted tours

**Why apply:** Summer 2026 is the focused build window. Bekins
captures are the first real test dataset. Don't suggest reintroducing
elements that were specifically researched away (CUDA-only tools,
Splatica/Splatware paid services as primary, Windows-only stacks).
The bet is Apple-native, Insta360-native, real-estate-focused.

**Related:** project_bekins_hall_vr.md — Bekins is the first real
test case for this pipeline.
