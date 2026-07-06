---
name: project_niche_app_strategy
description: Tactical playbook for shipping a portfolio of cheap niche iOS utilities — platform, monetization (free + $4.99 honor unlock), discovery, and app-selection criteria. Derived from [[reference_app_economics_2026]].
metadata:
  type: project
---

# Niche App Portfolio — Tactical Plan

Jeff's play: a portfolio of small, single-purpose iOS utilities (¡Poof! is the template) that solve one real annoyance, cost near-zero to serve at scale, and grow his creator audience. Evidence base: [[reference_app_economics_2026]]. Optimizing for **audience + creative satisfaction + modest recurring income**, not revenue maximization.

## The stack (decided)
- **Platform:** iOS-only by default. Native **SwiftUI** (cheapest since not shipping Android; best AI-assist support; already fluent). No cross-platform framework until a specific app earns an Android port.
- **Build loop:** AI-assisted "vibe coding" → device-test on a real iPhone (Vision/camera don't run in Simulator) → TestFlight Internal → ship. ¡Poof! proved this loop end-to-end.
- **Fees:** Apple Small Business Program = 15%. A $X app nets ~85% minus income tax. Portfolio math: many small apps > one big bet.

## Monetization (decided): free core + optional $4.99 "support" unlock
Rejected crippleware paymium (breaking the core until you pay) — wrong for a time-saving tool. Rejected *pure* honor-tip (too low conversion alone). **Chosen hybrid:**

> **Free forever, fully functional.** A one-time **$4.99 "Support [app]"** (non-consumable IAP, stays in 15% tier) that: (a) retires a gentle, infrequent "if this saved you time, consider supporting" nudge, and (b) unlocks one or two *genuine* bonus niceties (e.g. batch mode, a supporter theme/badge).

- The core **always works** — that's the brand and the discovery engine.
- The nudge is the carrot's stick; the bonus feature is the carrot. Converts better than pure guilt while staying true to the open-source spirit.
- Framing example: *"Free forever. If ¡Poof! saves you time, $4.99 once keeps it going — and unlocks batch mode."*
- **Why not upfront-paid:** no free tier = no discovery at-bats in a store flooded with 557k/yr new apps. Free downloads are how you get *found*; the unlock converts value-convinced users.

## Discovery is the real work — and Jeff's edge
Building is no longer the moat. **Owned-audience distribution beats ASO.** For each app:
1. **Design around a narrow, searchable problem** — be *the* obvious result for a specific query, not a broad category.
2. **Lead with Jeff's channels** — polymathery.com, wilsonidt.com, teaching, CSUSB IDT cohort, build-in-public content. Each app doubles as content.
3. **Quality signal** — a real Swift build clears Apple's spam bar (Guideline 4.3) cleanly; don't look like slop.

## App-selection filter (vet every idea against all four)
1. **Narrow** — solves ONE annoyance, not a suite.
2. **Searchable** — someone would type a specific query looking for exactly this.
3. **Audience-reachable** — Jeff can put it in front of people who have this problem (his channels/students/niche).
4. **Value-obvious** — the payoff is clear in one screenshot; "worth $4.99" lands instantly.

¡Poof! passes all four (background-remove → white square for resellers; reachable via reseller/creator content; obvious value).

## Next actions
- Keep ¡Poof! in beta; use it as the reference architecture + the first "build in public" content piece.
- Start a vetted idea list (each scored against the 4-point filter).
- On the next app, wire the free-core + $4.99 support-unlock pattern once and reuse it.
