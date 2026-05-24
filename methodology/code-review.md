# Code Review

Strict, adversarial code review. Staffed when code was written.

## How it works

The review is conducted by an **independent fresh-context sub-agent** — never the session that wrote the code (see `how-to-use-a-team.md`). It is given the diff plus this rubric, and it reviews **on the GitHub issue**: findings are posted as a comment there.

Be genuinely adversarial. The job is to find what is wrong, not to be encouraging. A soft review is worse than none — it gives false confidence.

## The code-review rubric

Score /10 across these dimensions; quote the specific code behind each finding:

- **Correctness** — does it do the right thing? Edge cases, error paths.
- **Simplicity** — is it the simplest thing that works? Unnecessary complexity is a finding.
- **Reuse** — does it use existing helpers and patterns rather than duplicating?
- **Error handling** — inputs validated, failures handled, nothing swallowed silently.
- **Tests** — is the change covered, and do the tests verify behaviour rather than restate the code?

**Scoring.** Rate each dimension **Strong / Adequate / Weak**, then compute `score = 10 − (1.5 × Adequate) − (3 × Weak)`, rounded, floored at 1.

**The gate is blockers, not the number.** A finding is a **blocker** if it would hurt a real user or if any dimension is rated Weak; the change is done when it has zero unresolved blockers. The /10 is a quality signal — a low score with no blockers means "shippable but weak," not "rejected." Separate blockers from **polish** (worth doing, not gating); surface at most the ~10 most important findings, blockers first, prioritized — do not dump.

## No self-scoring

The producer never scores its own code. The score always comes from the fresh-context reviewer. This is the enforcement path for the no-self-scoring rule — and it holds only if the reviewer is genuinely independent (`how-to-use-a-team.md`).

See `validation-loops.md` for the post-ship loop that invokes this review.
