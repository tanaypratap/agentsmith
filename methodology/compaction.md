# Compaction Readiness

A long session — especially in autonomous mode — will hit a context limit, compact, or crash. Compaction readiness is how the work survives that. It is core to how PMs work, not an afterthought.

## Why

When context compacts or a session crashes, in-context reasoning is lost. What survives: committed files, GitHub issues, the harness task list, and anything written to disk (your `tmp/pm-docs/<you>/` folder). Readiness means making sure the surviving state is enough for a fresh-context agent to continue without loss.

## Pre-compaction checklist

Run this before you are near a context limit — and on a cadence during long autonomous runs:

1. **Update your context-reload doc** (`tmp/pm-docs/<you>/`) — current state, what is done, what is next, key decisions. This is the primary handoff artifact.
2. **Update the GitHub issues** — evidence comments, checkboxes, status. The issues are the durable work record.
3. **Ensure granular tasks are loaded in the harness task list** — it survives compaction; a fresh agent reads it to see the shape of the work.
4. **Load one explicit task: "read the context-reload doc."** This guarantees the first action post-compaction is re-grounding.
5. **Confirm durable state is committed** — work that matters is committed and pushed, not sitting only in the working tree.

## Post-compaction resume

A fresh-context agent, on wake:

1. Read the context-reload doc (the task list points you here).
2. Re-read `how-pms-work.md` — re-ground in the operating model.
3. Reconcile with the GitHub issues and the PM board — what actually shipped vs what the reload doc says.
4. Continue.

A stale reload doc is worse than none — it misleads. Keeping it current *is* the work, not overhead.
