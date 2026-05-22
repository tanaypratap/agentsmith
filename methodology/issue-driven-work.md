# Issue-Driven Work

All real work is tracked on GitHub issues. No side-channel `Tickets.md` files; no work that exists only in a chat log.

## Epic + sub-issues

Big work is decomposed:

- **Epic issue** — the umbrella. Describes the whole, carries the design summary, links the sub-issues.
- **Sub-issues** — coherent, individually buildable pieces. Not so granular that there are forty micro-tickets; not so coarse that one ticket is unreviewable.

## Linkage convention

- The **epic body** carries a checklist referencing each sub-issue by number: `- [ ] #N`.
- Each **sub-issue body** opens with `Part of #<epic>`.

That is the standard — use it, so the relationship is machine-visible and unambiguous.

## The issue is the shared surface

Everything about a piece of work lives on its issue:

- the **spec** — in the issue body;
- the **implementation evidence** — as comments (what was built, what was verified);
- the **review** — as comments.

This is what lets a reviewer, another PM, or a future agent pick up the issue and have the whole story. Work that lives only in a session context is lost the moment that context ends.

## A well-formed ticket

An agent will be handed this ticket cold — it must set that agent up to succeed:

- **Scope** — unambiguous; one coherent deliverable.
- **Deliverables** — concrete; named files / outputs.
- **Context** — enough that the agent does not have to guess intent.
- **Acceptance criteria** — testable; "done" is not a matter of opinion.

If two competent agents handed the same ticket would build meaningfully different things, the ticket is not done.

## Boundary with review

This file owns *that* work happens on issues and *how* issues are linked. The format and rubric of a review comment is owned by the review files (`code-review.md`, `architecture.md`, `qa.md`, `design-critique.md`).
