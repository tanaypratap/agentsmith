# Architecture Review

Adversarial review of a spec, a plan, or an architecture — *before* it is built.

## Why review before building

The cheapest place to catch a structural flaw, a gap, or a contradiction is in the plan — not in the code. An architecture review reads the epic + sub-issues (or the spec) and asks whether the thing is sound and buildable.

## How it works

An **independent fresh-context sub-agent** (see `how-to-use-a-team.md`) reviews the tickets/spec against this rubric and posts a scored review on the issue. A confirmation re-review is bounded — it checks that fixes landed; it does not re-litigate settled, signed-off decisions.

## The architecture rubric

Score /10 across these dimensions; anchor each in specific ticket/spec text:

- **Soundness** — is the design coherent? No structural flaws.
- **Coherence** — do the pieces fit; no contradictions between tickets/components.
- **Gaps & overlaps** — is anything needed but unowned? Do two pieces own the same thing?
- **Agent experience** — handed a ticket cold, is an agent set up to succeed? Clear scope, concrete deliverables, testable acceptance.
- **Dependencies** — is the build order right, and declared?

**Scoring.** Rate each dimension **Strong / Adequate / Weak**, then compute `score = 10 − (1.5 × Adequate) − (3 × Weak)`, rounded, floored at 1.

**The gate is blockers, not the number.** A finding is a **blocker** if it would sink the build or if any dimension is rated Weak; the plan is build-ready when it has zero unresolved blockers. The /10 is a quality signal — a low score with no blockers means "buildable but weak," not "rejected." Separate blockers from **polish** (fix in-flight); surface at most the ~10 most important findings, blockers first, prioritized — do not dump.

## No self-scoring

The producer of the spec never scores it. The score comes from the fresh-context reviewer.

See `validation-loops.md` for the post-ship loop that invokes this review.
