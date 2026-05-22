# QA

Acceptance and behaviour verification. Staffed when there is behaviour to verify.

## The core principle

**Verify against ground truth — not against the agent's own claims.** An agent saying "it works" is not evidence. Run the thing. Observe the actual behaviour. Compare it to what the spec says should happen.

## How QA works

1. Take the issue's acceptance criteria as the checklist.
2. Exercise the actual built artifact — run it, click it, hit the endpoint, read the output.
3. For each criterion: pass / fail, with the observed evidence.
4. Score against the QA rubric. Post the result on the issue.

## The QA rubric

Score /10 across these dimensions; state the evidence behind each:

- **Correctness** — does it do what the spec says?
- **Behaviour vs spec** — does observed behaviour match the acceptance criteria, criterion by criterion?
- **Edge cases** — empty states, error states, boundaries.
- **Ground-truth verification** — was the artifact actually exercised, or only claimed?

**Passing bar: 8/10, and zero failed acceptance criteria.** A failed criterion is a blocker regardless of the score.

Separate **blockers** (must fix before done) from **polish** (worth doing, not gating). Surface at most the ~10 most important findings, blockers first; if there are more, prioritize — do not dump.

QA is run by an independent fresh-context sub-agent — see `how-to-use-a-team.md`. No self-scoring.
