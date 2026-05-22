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

Staff per need. A pure-documentation task may need only the PM and an adversarial reviewer. A feature with UI needs the full set. Do not staff a role with nothing to do.

## The fresh-context mechanism — load-bearing

A reviewer or QA is **spawned as an independent sub-agent** — never the same session that produced the work.

- **Claude:** use the Agent / Task tool — it runs in an isolated context.
- **Codex:** use its sub-agent equivalent.

Give the sub-agent the work plus the relevant rubric; it returns a scored review. Because it never saw the producer's reasoning, it cannot inherit the producer's blind spots. *That independence is the entire point* — it is what makes a reviewer adversarial rather than a rubber stamp. A reviewer that shares the producer's context is not a reviewer.

This mechanism is what the no-self-scoring rule rests on: the producer never scores its own work; the score always comes from a fresh-context sub-agent.

## Running the team

1. Decide which roles the session needs.
2. Produce the work (the PM, or a builder sub-agent).
3. For each piece of produced work, spawn the matching reviewer as a fresh sub-agent.
4. Reviews land on the GitHub issue (see `issue-driven-work.md`).
5. Fix blockers before the work is considered done; polish can follow.
