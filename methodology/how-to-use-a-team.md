# How to Use a Team

A PM does not do everything alone. You assemble a team for the session — only the roles the work actually needs.

## The roster

| Role | Staff when | Reads |
|---|---|---|
| **PM** | always — that is you | this whole folder |
| **Designer** | the work has UI / UX / visual design | `design-critique.md` |
| **QA** | there is behaviour to verify | `qa.md` |
| **Adversarial code reviewer** | code was written | `code-review.md` |
| **Adversarial architecture reviewer** | a spec, plan, or architecture needs reviewing | `architecture.md` |
| **Documentation reviewer** | a pure-documentation deliverable needs reviewing | `docs-review.md` |

The **Reads** column above is the role→file map — it tells each role which methodology file to read.

Staff per need. A pure-documentation task may need only the PM and an adversarial reviewer. A feature with UI needs the full set. Do not staff a role with nothing to do.

## The fresh-context mechanism — load-bearing

A reviewer or QA is **spawned as an independent sub-agent** — never the same session that produced the work.

- **Claude:** use the Agent / Task tool — it runs in an isolated context.
- **Codex — general reviewer (architecture / QA / design):** start a fresh `codex exec` run (a non-interactive invocation) given the work plus the rubric. This is the standard path for all reviewer roles.
- **Codex — code review specifically:** `codex review` is a built-in code-review command. Use it for reviewing code diffs. For all other reviewer roles (architecture, QA, design), use the `codex exec` path above.
- **Fallback (last resort, either tool):** if no sub-agent primitive is available at all, the human opens a fresh session and hands it the work. The one requirement: the reviewer is a process that never saw the producer's reasoning.

Give the sub-agent the work plus the relevant rubric; it returns a scored review. Because it never saw the producer's reasoning, it cannot inherit the producer's blind spots. *That independence is the entire point* — it is what makes a reviewer adversarial rather than a rubber stamp. A reviewer that shares the producer's context is not a reviewer.

This mechanism is what the no-self-scoring rule rests on: the producer never scores its own work; the score always comes from a fresh-context sub-agent.

**If no fresh-context mechanism is available at all** (no sub-agent tool, no human available): autonomous mode cannot reach "done" on review-gated work. Do not skip the gate and do not self-score. Surface the block explicitly — record it on the issue (or on-disk stand-in) and send a human notification. The work stays open until a review runs.

## Running the team

1. Decide which roles the session needs.
2. Produce the work (the PM, or a builder sub-agent).
3. For each piece of produced work, spawn the matching reviewer as a fresh sub-agent.
4. Reviews land on the GitHub issue (see `issue-driven-work.md`).
5. Fix blockers before the work is considered done; polish can follow.
