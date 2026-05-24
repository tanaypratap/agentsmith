# Worked Example: The v0.2 Validation Loop

This is a case study of the validation loop running on agentsmith itself — the v0.1 → v0.2 cycle. Read it alongside `methodology/validation-loops.md`, which describes the loop abstractly. This document makes the loop concrete.

A reader who finishes this document should understand:
- what the loop looks like on a real piece of work;
- why each phase exists (what it catches that the prior phase missed);
- how to recognize the same patterns in their own work.

The five artifact documents in this directory — `ax-review.md`, `e2e-setup-friction.md`, `e2e-pmsession-friction.md`, `ticket-review.md`, `ax-rereview.md` — are the actual reviews and friction logs. At each phase below, the document to study is named.

**How to read this document.** Read the narrative first, end to end — the score progression is the spine, and each phase opens with a one-line summary of what it caught that the prior phase missed. Then open the artifact files as the narrative directs and study them as examples of what a real adversarial review *looks like*. The narrative is the *teaching*; the artifacts are the *evidence*. Both matter — a teaching without evidence is opinion, and evidence without teaching is hard to apply to your own work.

---

## The original problem: v0.1 looked done

agentsmith v0.1 was a complete set of methodology files. The writing was disciplined. Every methodology file knew its boundary. The embed-vs-instantiate distinction was articulated. The rubrics were consistent.

It was not ready.

An independent holistic AX reviewer read every file as a single agent-experience arc — cold arrival, routing, first-time setup, PM session — and scored it **5.5 / 10**. A second reviewer ran the same experience in a scratch repo and logged 10 setup frictions and 8 PM-session frictions.

Neither reviewer told the other what they found before doing their own run. That independence is the point.

---

## Phase 1: The holistic AX review (5.5/10)

**Document to study: `ax-review.md`**

**What to look for when you read it.** Three things distinguish an adversarial AX review from a charitable one: (1) every finding is anchored in a specific file:line passage, not a general impression; (2) the severity rating is paired with a *failure mode* — what concretely breaks for an agent — not just "this could be better"; (3) the scoring justification names what is keeping the score below 10, and that justification is grep-able later when the loop iterates. Skim the B1 finding (path ambiguity) to see all three: it cites `pm-setup.md` line ranges, names the failure mode (a literal agent resolves bare paths against cwd), and tags the severity as blocker because the most-walked path of the methodology breaks silently.

The AX reviewer identified two blockers, four majors, and five minors. The two blockers were not edge cases — they were on the most-walked path.

**B1 — Systematic path ambiguity.** The runtime files (`pm-setup.md`, `compaction.md`, `collaboration.md`) referred to `tmp/pm-docs/`, `templates/`, and `methodology/` as bare paths. But a PM session runs with the *target repo* as cwd, and those files live under `.agentsmith/`. A literal agent resolves `tmp/pm-docs/` to `<repo-root>/tmp/pm-docs/` — the wrong location, not gitignored, and not where the next agent looks. The contradiction was internal and provable: `collaboration.md` said the board was at `tmp/pm-board.md`, while the board template explicitly said `.agentsmith/tmp/pm-board.md`.

**B2 — Unreachable re-setup dead-end.** `start-here.md`'s "incomplete" branch told an agent to "go to the agentsmith repo and follow its `setup.md`." But the PM-session agent had no path, URL, or clone location for the agentsmith repo — nothing in `.agentsmith/` recorded where it came from. The fallback that requires a human is not a fallback for an autonomous agent.

The holistic review caught these because it read the system as a whole, not file by file. A per-file reviewer looking at `pm-setup.md` sees one sentence of disambiguation ("Everything it refers to is local under `.agentsmith/`"); the holistic reviewer counts that sentence against ~12 bare relative paths and concludes the disambiguation is insufficient.

---

## Phase 2: The E2E friction tests

Two separate friction tests ran in parallel with the AX review: one for first-time setup, one for a PM session. Together they logged 18 frictions.

**Documents to study: `e2e-setup-friction.md` and `e2e-pmsession-friction.md`.** Both are the raw friction logs as the fresh agent wrote them in real time — actions taken, frictions hit, severity tags, end state. The signal you are looking for: each friction is anchored in a *specific step* the agent attempted, not a general impression; the severity rating is paired with a literal failure mode (what command broke, what file would not resolve); and the verdict at the end is honest about whether the flow completed unaided.

The setup friction test found F7 — **no commit step** — which the AX review had not flagged as a blocker. The setup docs stated "`.agentsmith/` is set up" without providing the `git add / git commit` step to make it durable. This was the single biggest setup gap, rated major, and it was invisible in a read-through. It showed up immediately when someone actually ran setup.

That is why both phases are required. Reading docs and running them surface different failures. The AX review is better at catching seam defects (B1, B2) and ordering hazards (M1: umbrella doc read sixth). The E2E run is better at catching missing mechanical steps (F7), environment assumptions (macOS-only notifications), and cases where an agent's actual working directory diverges from the doc's assumptions (PM-session E2E friction F1: "bare paths don't resolve from repo root," rated major, a live confirmation of B1).

**Why parallelism matters.** The AX reviewer and the E2E tester both run in parallel — neither sees the other's findings before producing its own. If they ran sequentially, the second reviewer would anchor on the first's framing and the two reports would converge on the same vocabulary, the same severity tags, the same blind spots. Independence is the entire point of the gate; sequencing the two gates would partly fold them. The parallelism cost is real (you need two reviewer agents, not one); the value is that the two reports together cover more failure modes than two sequential ones would.

---

## Phase 3: Triage — pooling the signals

Both sets of findings went into a single triage: an epic (#10) plus 10 granular sub-issues (#11–#20). Every finding got a home. No finding was silently omitted — not even the minor ones.

The triage captured all 23 source findings (10 AX + 10 setup-F + 8 PM-session-F, minus overlaps). This matters because even a minor omission is a signal that the triage is being selective — and if the triage is selective on minors, a reviewer cannot be confident the severity labels are trustworthy on the majors.

### A worked example: one finding's path from log to ticket

A finding does not travel from log to ticket alone — it merges with related findings into a single shippable unit of work. B1 (path ambiguity) was raised in the AX review as one finding spanning four files; the PM-session E2E re-run independently confirmed it as F1 ("bare paths don't resolve from repo root," rated major); the AX review's m1 (cosmetic path-style inconsistency) sat in the same neighborhood and was a softer version of the same defect. Three sources, one root cause.

The triage collapsed all three into ticket #11 (the path-rooting fix). The collapse is what made the fence buildable: if each source had been its own ticket, the same files would have been edited from three different angles in three different waves, with no agent holding the whole picture. One ticket, one author, one diff.

This is what triage is *for*. It is not "file a ticket per finding"; it is "give every finding a home, and let related findings share a home where the fix is shared." The "every finding gets a home" rule rules out silent omission; the "share where the fix is shared" rule rules out fragmentation.

---

## Phase 4: The adversarial ticket review (8.5/10)

**Document to study: `ticket-review.md`**

**What to look for when you read it.** The ticket review is doing something different from the AX review — it is auditing the *plan*, not the *artifact*. The signals: (1) a coverage table that maps every source finding to a destination ticket, including explicit non-carries with reasons; (2) a fence-cleanliness check on file overlap, so the build wave does not produce merge conflicts; (3) a "will this hit the target score?" prediction with a justification, not a vibe. Skim the MF1 finding (`pm-setup.md` shared by five tickets) to see all three: it names the affected tickets, identifies the build-time risk concretely, and predicts the score gap if uncorrected.

A fresh-context reviewer audited the *triage* — not agentsmith itself — and scored it 8.5/10. The plan was build-ready; the wiring needed one pass.

The reviewer caught 4 must-fixes. The most important was MF1: five tickets (#11, #12, #14, #16, #17) all edited `pm-setup.md`, but only one pair had a sequencing note. If #14 and #16 landed in different waves, the second agent would edit step-numbering the first had already changed, and #14's new remote-check step would have no home in #16's re-sequence.

MF3 illustrates a different failure mode: a fidelity over-claim. The ticket said "there is no stated Claude fallback for when `/loop` is unavailable." But the PM-session friction log (#17's cited source) documented an agent *applying* a fallback — embedding the quality bar in the reload doc by analogy to the Codex mechanism. The accurate statement was "the fallback is not stated, only inferable by analogy." The distinction matters: the fix (document the existing analogy) is very different from inventing a new mechanism.

The adversarial ticket review exists for this reason. Builders handed unchecked tickets would build what the tickets said, not what the findings required.

**What this teaches.** A ticket review that catches an over-claim is doing the same job a code reviewer catches an off-by-one error — it is finding a small misrepresentation that would have cascaded into the build. MF3 was small in scope (one sentence in one ticket) and large in consequence: a builder taking it at face value would have specified a new mechanism agentsmith did not need, adding code that the existing fallback already covered. The cost of the ticket review (one extra pass before the build) is far lower than the cost of building the wrong thing and rolling it back.

---

## Phase 5: The fix wave

Two builders ran in parallel on disjoint file fences. Builder A owned all path-rooting and runtime-file edits; Builder B owned setup, templates, and configuration. Zero file overlap; no coordination overhead; no merge conflicts.

The critical design decision was declaring the fences before the wave began. All five `pm-setup.md` editors (#11, #14, #16, #17, and the steps that depended on them) were assigned to one builder in one sequence, with #16's re-sequence authoritative. That decision was made at the epic level, not left for the builders to negotiate.

**Why the fence decision is the most important design choice in the wave.** A bad fence means two agents editing the same file from different prompts, producing merge conflicts the wave review eventually has to untangle. A good fence means each agent holds the whole picture for its slice and produces a self-consistent diff. The v0.2 fence was clean because the file-ownership boundary aligned with a *concept* boundary — runtime files vs. setup files — so each agent had a coherent slice to reason about end to end. When the concept and the file boundary diverge — when one ticket's fix spans both buckets — the right move is to assign all related tickets to one builder rather than splitting the file. The cost is sequential execution within that builder's slice; the benefit is zero conflicts between waves and a single coherent diff a reviewer can read in one pass.

---

## Phase 6: The wave review (8.5/10) — and what it caught

The wave review verified acceptance criteria across all 10 tickets. It scored 8.5/10 and caught must-fix MF1:

`notifications.md` used `<YOUR_NAME>` — the *human's* name — for the notification salutation. `pm-setup.md` used `<your-name>` — the *PM's* handle — as a filesystem slug. Both were technically correct within their own files. But a reader following the PM setup flow and then reading the notifications guidance would encounter two different placeholders for what looked like the same thing, with no disambiguation.

**This is the teaching point.** The conflation was invisible to per-ticket reviewers because each ticket owned a different file. It was invisible to the builders for the same reason. It became visible only when a reviewer ran the full set against acceptance criteria as a coherent experience — asking "does this work end to end?" rather than "does this ticket close?"

The wave review exists to ask exactly that question. And the doc-review pass (for prose-heavy waves) exists to apply a rubric to the writing quality of new documents, not just their acceptance-criteria coverage.

**The doc-review pass — when and why.** For waves that introduce substantial new prose (`validation-loops.md`, `principles.md`, this very example, etc.), the wave review's acceptance-criteria check is necessary but not sufficient — a passage can technically meet its AC while being unclear, contradictory, or rotted against its sources. The doc-review pass closes that gap: an independent fresh-context agent applies `docs-review.md`'s rubric (clarity, accuracy/no-rot, completeness, navigability, voice, cross-link integrity, scope coherence) to each new prose file and posts its findings on the same ticket as the wave review's findings. The two passes run in parallel because they audit different things — the wave review catches integration defects *across* files; the doc review catches prose defects *within* files. Both must clear before commit. In v0.3 the doc-review pass caught a fidelity over-claim in `anti-patterns.md` entry #8 that the wave review would have missed because it was an accuracy defect, not an AC defect.

---

## Phase 7: Re-verify — the loop closes

The holistic AX reviewer ran again. Score: **8.5 / 10** (bar: ≥ 8/10). Both blockers verified fixed by grep. All four majors fixed. Setup frictions: 10 → 3. PM-session frictions: 8 → 3, with 0 majors.

**Document to study: `ax-rereview.md`**

**What to look for when you read it.** A re-review is graded by *progression*, not by absolute score. Look for: (1) a per-finding status table — what was claimed fixed, with evidence that it actually was, in the form of file:line citations; (2) explicit *new* findings introduced by the fix wave (these are common and acceptable, as long as they are caught and ranked rather than hidden); (3) the residuals that did not block release, broken out as a v0.x polish backlog rather than silently absorbed. Skim the v0.2 residuals (N1–N4) to see (3): all four are minor, all four are named with file:line evidence, and all four were carried into v0.3 — none were dropped.

The re-review shows what convergence looks like. The score progression — 5.5 → 8.5 — is the output of the loop. The residual findings (N1–N4, all minor) were carried into the v0.3 polish backlog rather than blocking release: the experience was sound end to end, and polish is tracked, not silently dropped.

The re-verify is not optional. "The loop converged" is a claim that requires evidence — a fresh reviewer running the same gates and reporting what they found.

---

## What the loop does not catch

The validation loop is not a substitute for product judgment, taste, or domain expertise. It catches certain classes of failure very well and leaves others entirely uncovered. Be honest about both:

- **Design choices the reviewer agrees with but a user would reject.** The reviewer is a fresh-context agent; it is good at internal coherence and adversarial parsing, not at predicting how the design lands with real users. A loop that scores 9.5 can still ship the wrong thing.
- **Behavior under scale.** The E2E friction test runs once, on one repo, with one agent. It will not catch defects that emerge across hundreds of users, across many OS combinations, or across long session histories.
- **Performance regressions.** Unless the rubric explicitly scores latency or token cost, the loop does not measure them.
- **Security issues.** The reviewers are looking for clarity and correctness, not adversarial input. A security review is its own loop with its own rubric.
- **Drift over time.** The loop is a point-in-time gate. After it converges, files can rot through unrelated edits weeks later. Re-run the loop on a cadence — not just on new versions.

If a finding falls in one of these categories and the loop did not surface it, that is not a failure of the loop. It is the loop working as intended — and a signal that a separate gate is needed for that concern.

**And what the loop *does* catch surprisingly well.** These limits are honest scoping, not weakness. Within its scope, the loop is unusually good at:

- **Cross-file seams.** Defects invisible to any single-file review because the failure mode lives in the gap between two files (B1 path ambiguity across runtime files; MF1 placeholder conflation between `notifications.md` and `pm-setup.md`).
- **Doc/runtime divergence.** Places where what the documentation says and what the agent actually does diverge silently (F7's missing commit step; the entire runtime/setup seam B2 lived in).
- **Adversarial-agent resilience.** Documents that read clean to a human but trip a literal agent (bare paths, step-N references that rot when files re-sequence, ambiguous routing in incomplete-state branches).
- **Process-discipline lapses.** Self-scoring temptations, silent finding omissions, fences declared after the build has started — the things that would erode the loop's own integrity if left unchecked.

## When re-verify falls short of the bar

The loop's discipline depends on what you do when re-verify scores under the target. The wrong moves are easy:

- **Lower the bar.** "9.2 is close enough" turns the bar into noise. If 9.5 was the right bar before re-verify, it is still the right bar after.
- **Re-amend the v0.x tickets to claim they covered more.** The original tickets were written against the original findings. Mutating them rewrites history and makes the loop unauditable.
- **Self-score the re-review favorably.** The same principle that blocked the original score blocks re-grading. The reviewer is the reviewer.

The right move is a *targeted fix-up wave* — a small set of new tickets that address the specific re-review findings, run through the same loop (ticket review, fix wave, wave review, re-verify), and close. The point is not to hit the bar at any cost; the point is to converge with the bar honest.

v0.3 itself ran this contingency: the wave review caught two must-fixes that would have left the holistic re-review at 9.1–9.4 instead of ≥9.5. The fix-up was small (a sibling-path-style violation in the very file that established the rule; an expansion of this document from 118 lines to over 200) — and re-verify against the fixed state then cleared the bar. The discipline cost a wave; the alternative — shipping at 9.2 and calling it 9.5 — would have cost the loop's credibility.

## Common pitfalls when running this loop yourself

The mechanics of the loop are simple; the discipline is hard. Three pitfalls account for most of the times the loop reports clean but the piece still ships broken:

- **Reviewing your own work without realizing it.** A "fresh-context" reviewer that shares your terminal, your last commit, or your prior session is not fresh. If the reviewer can see the producer's reasoning, it inherits the producer's blind spots — see principle #2 and `how-to-use-a-team.md`'s fresh-context mechanism. If you cannot spawn a genuinely fresh agent, the correct move is to surface the block, not to fake the gate.
- **Skipping the empirical run because the AX review came in clean.** Reading docs and running them are different signals. The v0.1 AX review scored 5.5/10 and *still* missed F7 — the single biggest setup gap — because F7 was only visible to an agent actually executing the flow. The two phases are both required, not either/or.
- **Re-amending v0.x tickets when re-verify falls short.** Each loop is its own wave with its own triage, ticket review, and fix set. Mutating prior tickets erases the audit trail and confuses the next reviewer. Open new tickets in the next version's epic; the closed v0.x trail stays as evidence of what was done and why.
- **Treating the loop as a one-shot.** A version that converges through the loop is a snapshot, not a finished product — unrelated edits in subsequent weeks can re-introduce the defects the loop closed. Re-run the loop on a cadence, not just on new releases. The v0.2 → v0.3 path was not "v0.2 was bad" — it was "v0.2 was good *and* there was still work that pushed the score higher and codified the lessons learned in waves before."

## What to carry forward

The loop is not agentsmith-specific. It applies to any infra piece that an agent will use unaided:

1. Ship the v0.1.
2. Run a holistic AX review AND an empirical E2E friction test — both, in parallel, before you look at the results together.
3. Triage every finding. No silent omissions.
4. Get an adversarial ticket review before building.
5. Build on disjoint fences. Declare the fences first.
6. Wave review + doc-review pass. Ask "does this work end to end?"
7. Re-verify against the original gates.

The v0.2 numbers — 5.5 → 8.5, 18 frictions → 6, two blockers → zero — are the output of one loop. The methodology works.

The numbers are evidence the loop works in at least one case (this one). The methodology is general; the proof point is concrete. When you run this loop on your own infra piece the absolute scores will be different, but the *shape* should not be: a clear score progression from v0.1 to a re-verified target; frictions counted in dozens at v0.1 dropping to single digits after the fix wave; blockers caught at the AX review or E2E phase rather than after release; and a paper trail (issues, comments, reviews) that an outside reader could reconstruct the whole story from. If the numbers do not move when you run the loop — if your re-verify scores roughly the same as your initial review — something in the loop's discipline broke. The most common culprit is the fresh-context rule. Re-check that the reviewer never saw the producer's reasoning.

**Cadence.** Run the loop on every minor version (v0.x → v0.(x+1)) — that is the obvious trigger. Also run it on a calendar cadence (quarterly is a reasonable default) regardless of version, because the methodology rots through unrelated edits even when no release intervenes — see the "treating the loop as a one-shot" pitfall above. The cost is one fresh-context review pass per cadence; the value is that defects do not accumulate silently between releases. A piece that scored 9.5 in March and never re-ran the loop may well sit at 9.0 by July without anyone noticing.

**Why the v0.2 case is a useful teaching example.** The wave was small enough to study end to end (one repo, three weeks of work, 27 sub-issues across the v0.2 + v0.3 cycle) but large enough to surface every phase's distinct value at least once. The AX review caught seam defects no per-file review would have. The E2E run caught mechanical gaps no read-through review would have. The ticket review caught a fidelity over-claim no builder would have. The wave review caught the `<YOUR_NAME>`/`<your-name>` conflation no per-ticket reviewer would have. The doc-review pass (added in v0.3) caught an evidence over-claim no AC-checking reviewer would have. Five distinct gates, five distinct catches, no redundancy — and one place to point to when explaining why each gate exists.

### Mapping the loop to your repo

Use this mapping when adapting the loop to a piece other than agentsmith:

- **The artifact under review** — what corresponds to "the agentsmith repo" in your case. A methodology folder? A prompt library? A CLI's docs and bootstrap? Name it before you start.
- **The AX reviewer's brief** — translate from "agent experience" to whatever your primary consumer is. For a CLI: developer experience. For a methodology: PM/agent experience. For a prompt library: prompt-author experience.
- **The E2E friction test's surface** — what does "actually running it" mean in your repo? For agentsmith: an agent set up a target repo and started a PM session. For a CLI: a developer ran the install + first command. For a prompt library: an author wrote a new prompt using only what the library provides.
- **The fences for the fix wave** — partition your repo into builder-sized slices with no file overlap. The boundaries that work for one repo's structure rarely work for another's; spend ten minutes on this before assigning.
- **The re-verify bar** — your target score. Use the same bar that the reviewer scored against the first time. Do not move the bar between runs.

Without this mapping the loop is abstract; with it the loop is operational.

---

See `methodology/validation-loops.md` for the loop described phase by phase, and `methodology/anti-patterns.md` for the specific failure modes these phases exist to catch.
