---
name: user-wiley-history
description: "Jefe's relationship to Wiley & the broader publishing industry — informs his stance on WileyML, OER, and what to suggest for the Happenstance rewrite"
metadata: 
  node_type: memory
  type: user
  originSessionId: f9675d30-5405-4d26-b05b-6db42e1fe12e
---

John Wiley & Sons was Happenstance's biggest client for ~13 years
(~$50K/yr typesetting their series). They moved their entire operation
to India in 2013 and dropped Happenstance. Jefe quote: "they are
fucking evil and I will do everything in my power to undermine them for
the rest of my life."

Practical implications for collaboration:

- **OER (Open Educational Resources) FTW** — this is part of Jefe's
  professional motivation now (he teaches at Crafton Hills). When he
  builds tools that touch educational content, OER-friendliness is
  inherently valued, not a feature to be argued for.
- **Don't propose preserving WileyML in the Swift rewrite.** Drop it
  outright. The 240-line `postWileyML` sub and ~100 entity mappings in
  the existing Perl are dead weight that map onto a former client he
  has no interest in supporting again.
- **Don't suggest courting Wiley-shaped clients** ("you could pitch
  this to publishers like X"). Read the room.
- The broader frame: publishing has been dying for years; Wiley's
  offshoring was an early signal. Affects what kinds of tooling are
  worth building — favor the surviving artisanal/indie publisher
  niche (NAB, similar) over big-publisher workflows.

See [[publishing-decline-timeline]] for the running industry
timeline Jefe is building.
