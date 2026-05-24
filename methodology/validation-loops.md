# Validation Loops

The post-ship loop agentsmith proved on itself during v0.2. Run it after shipping any v0.1 of an infra piece — before declaring it good.

> **ship → holistic AX review + empirical E2E friction test → triage → adversarial ticket review → fix wave → re-verify**

For a worked example of this loop applied to agentsmith itself, see `examples/v02-validation/README.md` (in the agentsmith repo; not embedded into target repos). For the anti-patterns these loops exist to catch, see `anti-patterns.md`.

---

## 1. When to run the loop

After shipping a v0.1 of any infra piece — a methodology set, a workflow system, a bootstrappable repo, a prompt library. "Shipped" means the first complete version is in place, not that it has been validated.

Do not wait for users to find the failures. Run the loop before declaring the piece "good." The v0.1 of agentsmith scored 5.5/10 at this gate; two blockers would have silently broken every PM session in every set-up repo if the loop had not run.

---

## 2. Phase 1 — Holistic AX review

An **independent fresh-context reviewer** reads the whole piece as ONE end-to-end agent experience: cold arrival, routing, first use, daily operation. Not a per-file review. The reviewer is looking for the seams between phases — the places where one wave's output fails to connect to the next wave's expectations.

Per-wave reviews catch defects *within* a wave. The holistic review catches defects *between* waves: the file that is internally correct but addresses paths against an unstated working directory (v0.1 B1); the router whose completeness check is weaker than the setup it guards (v0.1 M2); the umbrella doc that says "read me first" but is numbered sixth (v0.1 M1).

The reviewer uses the rubric from the relevant review file for the artifact type (e.g. `architecture.md`, `code-review.md`, `design-critique.md`, `docs-review.md`, `qa.md`). It posts a scored review — findings prioritized, blockers first — on the issue. No self-scoring.

---

## 3. Phase 2 — Empirical E2E friction test

A fresh agent actually *runs* the piece in a clean worktree or scratch repo and logs every friction encountered. Reading the docs and running them surface different failures.

The v0.1 holistic review found B1 (path ambiguity) and B2 (dangling bootstrap component) in reading — but did not catch F7 (no commit step, rated major), which was immediately apparent when actually executing setup. The E2E run found 10 setup frictions and 8 PM-session frictions, some of which the read-through review entirely missed.

Frictions are logged as they occur: what the doc said, what the agent tried, what broke, severity. The log is raw evidence — not cleaned up or analyzed yet.

Both phases run before any triage. Pool the signals first; analyze together.

---

## 4. Phase 3 — Triage

Pool the findings from the AX review and the E2E log. File a fix epic plus granular sub-issues — one issue per discrete fix unit. Every finding gets a home: either a ticket, or an explicit WONTFIX with a reason. No silent omissions, even for minors.

Severity-rate every finding: blocker (would sink the thing or leave a user stuck), major (forces a guess or mid-session failure), minor (cosmetic or workaround available). The triage is itself reviewable — see phase 4.

The v0.2 triage covered all 23 source findings (10 AX + 10 setup-E2E + 8 PM-session-E2E, minus overlaps) with no dropped finding, not even a minor. That standard is the bar.

---

## 5. Phase 4 — Adversarial ticket review

An independent fresh-context reviewer audits the *triage* — not the artifact — against the source findings. The question is whether the issue set is faithful, complete, and build-ready: does every finding map to a ticket? Are severity labels defensible? Do the build boundaries avoid double-ownership? Are acceptance criteria testable?

The v0.2 ticket review scored 8.5/10 and caught 4 must-fixes: five tickets sharing `pm-setup.md` with only one sequencing note; non-embed wording owned by two tickets with a one-sided cross-link; a fidelity over-claim in one ticket's problem statement; two minor findings silently absorbed without disposition. None of those would have surfaced in the build — they would have surfaced as merge conflicts and under-delivered acceptance criteria.

Do this before the build wave begins. A bad plan built faithfully produces a bad result faster.

---

## 6. Phase 5 — Fix wave

Builders work on **disjoint file fences**: each file is owned by at most one builder per wave. Declare the fences in the epic before work begins. If a file is edited by multiple tickets, assign all those tickets to one builder in one wave — do not split them.

The v0.2 fix wave used two builders on disjoint fences: Builder A owned all path-rooting and runtime-file edits; Builder B owned setup, templates, and configuration. Zero file overlap; fully parallel-safe. Where a file needed edits from multiple angles (e.g. `pm-setup.md` needed path fixes AND re-sequencing AND step rewrites), all those tickets were assigned to one builder.

Commit per clean build. No bulk commits. Each commit is a reviewable unit.

---

## 7. Phase 6 — Wave review (+ doc-review pass for prose-heavy waves)

An independent fresh-context reviewer verifies acceptance criteria across all tickets in the wave. Did each ticket deliver what it said? Do the pieces fit together as a coherent whole? Did the wave introduce any new defects?

For waves that include substantial new prose, run a parallel **doc-review pass**: an independent reviewer applies `docs-review.md`'s rubric to the new documents. This is distinct from the wave review (which checks acceptance criteria) — the doc-review pass checks the quality of the prose as a reader experience.

The v0.2 wave review scored 8.5/10 and caught must-fix MF1: `notifications.md` used `<YOUR_NAME>` (the human's name) while `pm-setup.md` used `<your-name>` (the PM's handle). Both placeholders were technically correct within their own files — the conflation was only visible when a reviewer ran the full set against acceptance criteria as a coherent experience. That is exactly the seam a per-ticket reviewer would miss and a wave reviewer is positioned to catch.

In-wave must-fix remediation by the PM, then re-submit for wave review sign-off.

---

## 8. Phase 7 — Re-verify

Re-run the holistic AX review and both E2E friction tests against the same gates used in Phase 1–2. The bar is the target set at the start: for agentsmith v0.2, ≥ 8.0 holistic AX + no blocker-class friction per E2E.

v0.2 re-verified at 8.5/10 holistic AX; setup frictions 10 → 3; PM-session frictions 8 → 3 with 0 majors. The loop converged.

If the re-verify does not clear the bar, open new issues and run another loop — do not amend the old tickets. Each loop is a distinct improvement wave with its own triage, ticket review, and fix set.

---

## No self-scoring at any gate

Every score at every gate comes from an independent fresh-context agent. The producer does not score the AX review, does not score the ticket review, does not score the wave review, does not declare the re-verify clean. See `principles.md` principle #2 and `how-to-use-a-team.md` for the fresh-context mechanism.

The loops above catch the anti-patterns listed in `anti-patterns.md`.
