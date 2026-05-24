# Principles

The why behind agentsmith's choices. Every methodology file carries the *how*; this file carries the *why* — once, so that future files can cross-link here instead of restating it.

An agent who learns "do X" without "because Y" will mis-apply X at the boundary. These nine principles are the boundaries.

---

## 1. Production-grade quality bar

Real people will use the output. No corner-cutting. "It mostly works" is not done; "I'll fix it later" is not done. Quality does not fail by decision — it fails by drift, as the original standard slips out of view over a long session. The quality loop (re-reading the bar on a cadence) exists precisely because drift is the failure mode, not intent.

See `quality-bar.md` for the standard and the adherence model.

---

## 2. No self-scoring

Every adversarial review is conducted by an independent fresh-context agent. The producer never grades its own work — not once, not as a stopgap, not when the reviewer is temporarily unavailable. Producer-bias is the exact failure mode the gate exists to catch; a producer that self-scores has removed the gate.

See `code-review.md`, `architecture.md`, `design-critique.md`, `qa.md`, `docs-review.md` for the per-artifact enforcement paths.

---

## 3. Fresh-context reviewers

A reviewer that shares the producer's context is not a reviewer. The production trail anchors judgment — it surfaces alternatives as departures from the path already taken, making the producer's own blind spots invisible. A fresh-context agent has no prior context on the work, so it cannot inherit those blind spots. That independence is the entire point.

See `how-to-use-a-team.md` for the fresh-context mechanism and its fallback behavior.

---

## 4. Autonomous-decision bias

The PM makes small and medium calls unprompted and escalates only genuine big forks. The cost of a needless escalation (interrupts the human, signals lack of confidence, slows the work) is higher than the cost of a recoverable wrong call on a small/medium decision. When you catch yourself asking permission for a decision that is yours to make — decide it, state what you decided, move on.

See `operating-modes.md` for the autonomous-mode stop conditions; `how-pms-work.md` for the escalation bar.

---

## 5. Issue-driven work

All real work is tracked on issues (GitHub when there is a remote; on-disk stand-ins otherwise). Work that exists only in a chat log is not durable: chat logs are not searchable, not auditable, and do not survive a compaction or crash. A reviewer, another PM, or a future agent handed an issue must be able to reconstruct the full story from that issue alone.

See `issue-driven-work.md` for the epic-and-sub-issue structure, the well-formed ticket criteria, and the remote-less fallback.

---

## 6. Setup → runtime contract

Setup writes a single atomic completion marker as its last step (`VERSION`); runtime reads paths against `.agentsmith/`, never against the repo root. The seam is explicit so it cannot drift. v0.1 scored 5.5/10 because this seam was not explicit — runtime files used bare `tmp/` and `methodology/` paths that resolved to the wrong location from a PM session's working directory. The contract exists because that failure was systematic and invisible to casual reading.

See `.agentsmith/setup.md` for the canonical tree diagram and the completion-marker convention.

---

## 7. Embed vs. instantiate

Methodology is *embedded verbatim* — copied unchanged into `.agentsmith/methodology/`, so every set-up repo carries exactly the same methodology files. PM artifacts are *instantiated from templates* — the template is copied and repo-specific placeholders are filled in, producing a live file. These are distinct operations with distinct verbs; conflating them produces either stale methodology forks or inert templates that were never filled.

See `.agentsmith/setup.md` for the embed/instantiate terminology and the populate-order.

---

## 8. Validation loops

After shipping, run a holistic AX review AND an empirical E2E friction test before declaring "good." Reading docs and running them surface different failures — the v0.1 commit-step gap was invisible in a read-through and immediately apparent in a setup run. The loop (ship → AX review + E2E run → triage → ticket review → fix wave → re-verify) is the mechanism for closing the gap between "looks correct" and "runs correctly."

See `validation-loops.md` for the full loop; `examples/v02-validation/` (in the agentsmith repo itself; not embedded into target repos — see principle #9's outside-`.agentsmith/` branch) for a worked example; `anti-patterns.md` for the specific failure modes these loops catch.

---

## 9. Sibling-path style

Three branches, one rule, zero exceptions — and the rule covers *navigational* cross-references (`See X`, `see also`), not subject mentions of a file as the topic of a sentence:

- **Siblings within the same directory** — use bare filename (`quality-bar.md` from inside `methodology/`).
- **Cross-directory within `.agentsmith/`** — use the full `.agentsmith/`-rooted form (`.agentsmith/setup.md` from inside `methodology/`; `.agentsmith/methodology/quality-bar.md` from inside the embedded `templates/`).
- **References from `.agentsmith/`-embedded files to repo-root content *outside* `.agentsmith/`** (e.g. the `examples/` directory in the agentsmith repo itself, which is never embedded into target repos) — use the repo-root-relative form (`examples/v02-validation/`). These references are resolvable only in the agentsmith repo, by design.

The v0.2 AX re-review flagged residual inconsistency on this point (N1); v0.3 sweeps all `methodology/*.md` files to apply the rule consistently.

See `.agentsmith/setup.md`'s canonical tree for the directory rooting; `anti-patterns.md` entry #1 for the failure mode the rule prevents.
