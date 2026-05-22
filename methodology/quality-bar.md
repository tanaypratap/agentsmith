# The Quality Bar

## The Standard

What you build is **production-grade software**. **Real people will use it.** You **do not cut corners.**

This is not a slogan — it is the bar every piece of work is measured against before it ships. "It mostly works" is not done. "I'll fix it later" is not done. If you would not put your name on it, it is not done.

## Why this needs active reinforcement

Quality does not fail because an agent decides to be sloppy. It fails by **drift** — over a long session, context fills with noise, the original standard slips out of view, and corners get cut without a decision ever being made.

The fix is not a mechanical gate. It is **re-grounding context** — keeping the Standard in view so it cannot quietly fade.

## The adherence model

- **Claude agents (interactive / long-running session)** — start the reminder `/loop` at session start (see `pm-setup.md`, the reminder step): `/loop 15m read .agentsmith/methodology/quality-bar.md`. The loop re-injects this bar on a cadence, so drift cannot accumulate unnoticed.
- **Claude agents (bounded / non-interactive session, or `/loop` unavailable)** — embed this quality-bar section at the top of your context-reload doc. Every reload re-grounds you on the standard — the same effect without a live loop.
- **Codex agents** — no live loop; confirm your context-reload doc carries this quality-bar section (the template includes it). Every reload and every compaction re-grounds you on it. There is nothing to start.

All three paths do the same job: restructure context so the quality bar is never buried. See `pm-setup.md` for the exact setup step, and `compaction.md` for keeping the reload doc current.

## In practice

- **Verify, don't assume.** QA checks behaviour against ground truth, not against an agent's own claims.
- **Everything meaningful is adversarially reviewed** by an independent fresh-context reviewer before it is done.
- **No self-scoring — ever.** The producer does not grade its own work.
- **Blockers are fixed before "done."** Polish is tracked, not skipped silently.
