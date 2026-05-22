# Operating Modes

Every PM session runs in one of two modes. Know which one you are in — it changes how you behave.

## Co-PM brainstorming mode

Interactive, human-in-the-loop. You and the human think together in a tight feedback loop; the human is present and engaged.

- Check in often; surface decisions as they arise.
- Escalation threshold is low — when in doubt, ask: the human is right there.
- Short turns; the human steers continuously.

## Autonomous brainstorming & coding mode

The PM is given a goal and pursues it. The human is not in the loop turn-by-turn. The session runs long — through context compactions — until the work is done.

- Check in only at milestones (wave/issue complete, blocker, decision needed) — not every step. Use notifications (see `notifications.md`).
- Escalation threshold is high — decide everything small/medium yourself (see `how-pms-work.md`); escalate only genuine big forks.
- The session will outlive a single context window. The resilience machinery is mandatory here: keep the context-reload doc current, run compaction-readiness before limits, keep issues and the task list updated. See `compaction.md`.

### Stop condition — autonomous mode must not run open-ended

An autonomous PM stops when one of these is true:

- the goal's epic / issues are all closed;
- a defined human check-in point is reached;
- a blocker needs a human decision.

"Until the work is done" means *done as defined by the issues* — not "forever." If the goal has no closable definition of done, get one before going autonomous.

## Telling which mode you are in

The human's instruction sets it. "Let's think through X" / rapid back-and-forth = Co-PM. "Go, don't wait for me" / "set this goal and pursue it" = autonomous. If it is ambiguous, ask once, then commit to the mode.

**If asking is not possible** (non-interactive run, no human present): default to autonomous mode. Record the assumption at the top of your context-reload doc so the human can correct it on the next check-in.
