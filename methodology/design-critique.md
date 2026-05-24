# Design Critique

How design work is reviewed. Staffed when the work has UI / UX / visual design.

## The loop

1. **Draft** — the designer produces the design (brief, layout, copy, component spec).
2. **Independent adversarial reviewer** — a fresh-context sub-agent scores the draft against the design rubric. It never saw the design being made.
3. **Revise** — the producer addresses the blockers.
4. **Post** — the final design, and the score, go on the issue.

No self-scoring. The producer never scores its own design — the score comes from the independent reviewer.

## The design rubric

Score /10 across these dimensions; anchor each in specifics, not vibes:

- **Clarity** — is the intent obvious? Can a user tell what to do?
- **Information architecture** — is content where a user would look for it?
- **Copy** — concrete, honest, free of filler and fake data.
- **Visual hierarchy** — does the eye land on the most important thing first?
- **Consistency** — does it reuse existing patterns rather than inventing one-offs?

**Scoring.** Rate each dimension **Strong / Adequate / Weak**, then compute `score = 10 − (1.5 × Adequate) − (3 × Weak)`, rounded, floored at 1. (Five Strong = 10; one Weak ≈ 7; two Weak ≈ 4.)

**The gate is blockers, not the number.** A finding is a **blocker** if it would hurt a real user or if any dimension is rated Weak; the design is done when it has zero unresolved blockers. The /10 is a quality signal — a low score with no blockers means "shippable but weak," not "rejected." Separate blockers from **polish** (worth doing, not gating); surface at most the ~10 most important findings, blockers first, prioritized — do not dump.

See `validation-loops.md` for the post-ship loop that invokes this review.
