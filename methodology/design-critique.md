# Design Critique

How design work is reviewed. Staffed when the work has UI / UX / visual design.

## The loop

1. **Draft** — the designer produces the design (brief, layout, copy, component spec).
2. **Independent adversarial scorer** — a fresh-context sub-agent scores the draft against the design rubric. It never saw the design being made.
3. **Revise** — the producer addresses the blockers.
4. **Post** — the final design, and the score, go on the issue.

No self-scoring. The producer never scores its own design — the score comes from the independent scorer.

## The design rubric

Score /10 across these dimensions; anchor each in specifics, not vibes:

- **Clarity** — is the intent obvious? Can a user tell what to do?
- **Information architecture** — is content where a user would look for it?
- **Copy** — concrete, honest, free of filler and fake data.
- **Visual hierarchy** — does the eye land on the most important thing first?
- **Consistency** — does it reuse existing patterns rather than inventing one-offs?

**Passing bar: 8/10, zero blockers.**

Separate **blockers** from **polish**. Surface at most the ~10 most important findings, blockers first; prioritize, do not dump.
