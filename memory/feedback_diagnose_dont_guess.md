---
name: feedback_diagnose_dont_guess
description: When troubleshooting, determine the real cause before proposing fixes. Re-check my own prior notes/flags first; don't spray guesses at the user — it burns their real time.
metadata:
  type: feedback
---

# Diagnose the real cause — don't guess at the user

## What happened (2026-07-07)
Jeff spent ~2 hours because a TestFlight build wouldn't appear for external
testing. I cycled through guesses — export compliance, missing Test
Information, UI quirks, "upload a new build" — instead of recalling the actual
cause. The build had been uploaded via Organizer's **"TestFlight Internal
Only"** option, which makes a build eligible for internal testing ONLY. I had
**explicitly flagged that exact tradeoff to Jeff back at build 1** and then
forgot it. The fix was re-archiving and distributing via "App Store Connect."

## Why this matters
Rapid-fire hypotheses without verification waste the user's finite, valuable
time (even though wasted time doesn't cost me anything — which makes it worse,
not better) and erode trust. Guessing *feels* like progress but isn't.

## How to apply
1. **Re-read my own project memory/flags FIRST.** The answer is often something
   I already said. Before searching the web or theorizing, grep my notes for
   what I've already established about this system.
2. **State ONE most-likely cause + how to confirm it** — not a spray of
   "maybe it's X, or Y, or Z." If I'm uncertain which, say so and go verify.
3. **When I don't know, verify before advising** — read the authoritative doc,
   or inspect the actual system state — rather than emit a confident guess.
4. **For platform/tooling quirks (App Store Connect, Xcode, signing), check my
   earlier session notes before anything else.** These have known gotchas I've
   usually already recorded.
5. If I catch myself on the 2nd+ hypothesis for the same symptom, STOP and
   switch to systematic diagnosis instead of another guess.

Related: [[feedback_verify_compute_target_first]], [[feedback_weight_explicit_prompts]].
