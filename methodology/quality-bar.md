# The Quality Bar

## The Standard

What you build is **production-grade software**. **Real people will use it.** You **do not cut corners.**

This is not a slogan — it is the bar every piece of work is measured against before it ships. "It mostly works" is not done. "I'll fix it later" is not done. If you would not put your name on it, it is not done.

## Why this needs active reinforcement

Quality does not fail because an agent decides to be sloppy. It fails by **drift** — over a long session, context fills with noise, the original standard slips out of view, and corners get cut without a decision ever being made.

The fix is not a mechanical gate. It is **re-grounding context** — keeping the Standard in view so it cannot quietly fade.

## The adherence model

- **Claude agents** — set up a reminder `/loop` at session start (see `pm-setup.md`) that periodically re-surfaces the Standard. The loop re-injects this bar on a cadence, so drift cannot accumulate unnoticed.
- **Codex agents** — no live loop; the Standard rides in your context-reload doc (see `compaction.md`). Every reload and every compaction re-grounds you on it.

Both mechanisms do the same job: restructure context so the quality bar is never buried.

## In practice

- **Verify, don't assume.** QA checks behaviour against ground truth, not against an agent's own claims.
- **Everything meaningful is adversarially reviewed** by an independent fresh-context reviewer before it is done.
- **No self-scoring — ever.** The producer does not grade its own work.
- **Blockers are fixed before "done."** Polish is tracked, not skipped silently.
