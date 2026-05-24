# agentsmith v0.2 fix-wave — adversarial review of the issue set

Independent, fresh-context review of the v0.2 triage: epic #10 plus sub-issues
#11–#20. The job is to audit the **triage**, not agentsmith itself — is this a
faithful, complete, build-ready plan? Read-only review; no issues or repos
modified.

Source docs audited against:
- `ax-review.md` — holistic AX review, 5.5/10, findings B1, B2, M1–M4, m1–m5.
- `e2e-setup-friction.md` — first-time-setup friction log, F1–F10.
- `e2e-pmsession-friction.md` — PM-session friction log, F1–F8.

---

## 1. Summary

This is a strong triage. All 23 source findings (10 AX + 10 setup-F + 8
pm-session-F, less overlaps) are accounted for — there is **no dropped finding**,
not even a minor. Severity labels are mostly defensible, the two genuine
blockers (path ambiguity, the routing/`bootstrap` seam) are correctly elevated,
and the epic frames the diagnosis ("the mechanical seam between setup and
runtime") accurately. The acceptance criteria are unusually concrete for a
triage — several are literally `grep`-checkable.

The defects are at the margins, not the core. The most serious is a **boundary
hazard the triage names but does not fully fence**: #11, #12, #14, #16, #17 all
edit `pm-setup.md`, but only the #11↔#16 pair carries a sequencing note — #14
and #17 also edit `pm-setup.md` and are not cross-linked to that wave. There is
one **fidelity drift** (#17 over-claims that pm-session F4 documents a "no
stated Claude fallback" — the friction log actually says the Codex-style
fallback *was* applied by analogy; minor but worth tightening). And the
**non-embed wording is assigned to two tickets** (#12 and #18) with a
coordination note in #18 but not in #12 — correct intent, asymmetric pointer.
Setup-F3 and pm-session-F8 are absorbed into larger tickets without an explicit
mention, which is defensible but should be confirmed as deliberate.

Net: build-ready after a handful of cross-reference and wording tweaks. None of
the gaps would silently lose a finding; they create merge friction or let an
agent under-deliver one sub-point.

---

## 2. Score

**8.5 / 10.**

Justification: Completeness is essentially perfect — every source finding maps
to a ticket, severity-weighted with no blocker or major lost. Fidelity is high;
the tickets generally state the finding *better* than the source (e.g. #11
folds in AX m1's intra-file inconsistency explicitly). Acceptance criteria are
verifiable. The epic's "Done when" is measurable (re-review ≥ 8, no
blocker-class friction, VERSION bump). What keeps it off a 9–10: the
`pm-setup.md` multi-editor boundary is under-fenced (4–5 tickets, one note); the
non-embed wording is double-owned with a one-sided cross-link; one fidelity
over-claim in #17; and two findings (setup-F3, pm-session-F8) are silently
absorbed. These are real build hazards — two agents on #14 and #16 in different
waves would both rewrite `pm-setup.md` step 6 region — but they are fixable with
edits to the issue text, not a re-think. The plan is sound; the wiring needs one
pass.

---

## 3. Findings

### MUST-FIX

**MF1 — `pm-setup.md` is edited by five tickets; only one pair is sequenced.**
Issues #11, #12, #14, #16, #17 all modify `pm-setup.md`:
- #11 — rewrites every path in it (steps 3–6, the intro line).
- #12 — `start-here.md` mainly, but its completeness-check fix is defined
  against what `setup.md`/`pm-setup.md` produce; #12 also touches the
  bootstrap-exclusion wording that #18 edits (see MF2).
- #14 — adds remote-detection logic to `pm-setup.md` onboarding.
- #16 — re-orders the entire step list 1→6 (moves the umbrella-doc read).
- #17 — rewrites `pm-setup.md` step 5 (the `/loop` / quality-bar step).

Only #16 carries a sequencing note ("this issue and #11 both edit `pm-setup.md`
heavily — sequence them onto the same agent / same wave"). #14 and #17 also edit
`pm-setup.md` and have **no** such note. #16 *renumbers* the steps; #17 rewrites
"step 5" by number; #14 adds a remote check that #16's re-sequence ("umbrella →
mode → team → board → reload doc → reminder loop → work") does not even list a
slot for. If #14, #16, #17 land in different waves, the second agent edits a
step-numbering the first already changed, and #14's new remote-check step has no
home in #16's sequence.
*Fix:* Make the epic (or each of #11/#14/#16/#17) state that **all
`pm-setup.md` edits land in one wave on one agent** — #11, #14, #16, #17
together, with #16's re-sequence applied first and #14's remote check slotted
into the agreed sequence. Add the cross-link to #14 and #17, not just #16. Also
have #16 explicitly say where the remote-check step (#14) and the reminder step
(#17) sit in the new order, so the re-sequence is authoritative.

**MF2 — the `bootstrap/`/`README.md` non-embed wording is owned by two tickets
with a one-sided cross-link.** #12's fix says "State explicitly in `setup.md`
that `bootstrap/` (and `README.md`) are intentionally NOT embedded." #18's fix
says "State that `README.md` and `bootstrap/` are intentionally not embedded
(coordinate the wording with #12)." Both tickets will edit the *same passage* of
`setup.md` §3/§3.1. #18 points at #12; **#12 does not point back at #18**. An
agent taking #12 first writes the wording, an agent on #18 later rewrites it —
or worse, both run in parallel and conflict. Acceptance criterion duplication
confirms the overlap: #12 AC "states the `bootstrap/` + `README.md` non-embed as
deliberate" and #18 AC "Non-embedded files (`README.md`, `bootstrap/`) are
explicitly listed as deliberate exclusions" are the *same* deliverable.
*Fix:* Assign the non-embed wording to exactly **one** ticket (recommend #18,
since it owns the broader §3 terminology cleanup) and have #12 reference #18 for
it, dropping #12's own AC line or marking it "delivered by #18." Add the
reciprocal cross-link to #12.

**MF3 — #17 over-claims a source finding (fidelity).** #17's Problem says "There
is no stated Claude fallback for when `/loop` is unavailable." pm-session F4
(its cited source) actually says the tester *applied* a fallback — "Applied the
Codex-style mechanism by analogy — embedded the quality bar verbatim at the top
of the context-reload doc" — and rates F4 **minor** precisely because "the
*intent* is achievable via the reload doc." The AX review M3 agrees the
Codex/reload-doc path "mostly works." So the accurate statement is *"the Claude
fallback is not stated, only inferable by analogy to the Codex mechanism"* —
not that no fallback exists. The ticket's framing risks an agent inventing a
new mechanism instead of simply *documenting the existing analogy*.
*Fix:* Reword #17's Problem to "no *stated* Claude fallback — the reload-doc
mechanism exists but is only inferable by analogy to the Codex branch." The
proposed fix (#17 already says "embed the quality bar at the top of the reload
doc, exactly as the Codex mechanism does") is correct — only the problem
statement over-claims.

**MF4 — setup-F3 and pm-session-F8 are absorbed with no explicit mention;
confirm deliberate.** Two source findings have no named home:
- **setup-F3** (plugin-check ordering: step 1 plugins before step 2 repo check)
  — not mentioned in any ticket. #19 covers the plugin checklist but only its
  content (pass/fail, Codex-absent), not the §1-vs-§2 ordering. F3 is explicitly
  rated "minor ordering oddity, not wrong" by the source, so dropping it is
  *defensible* — but the triage should say so rather than leave it silently
  unaddressed.
- **pm-session-F8** (`start-here.md` "not set up" branch routes you out to the
  agentsmith repo) — this is the *same defect* as AX B2 part 2 and is fully
  carried by #12 item 2 ("Unreachable re-setup dead-end"). #12 cites
  "PM-session E2E F8" in its source line, so it *is* captured — good — but #12's
  body never restates F8's specific angle (that FRICTION 1's path defect makes a
  *correct* setup look incomplete and thus triggers the bad route). Actually
  #12 *does* capture that: item 3 says "the path defect (#11) can make a
  *correct* setup look incomplete, actively triggering the bad re-setup route."
  So pm-session-F8 is fully covered. **Only setup-F3 needs an explicit
  disposition.**
*Fix:* Add one line to #19 (or the epic) explicitly dispatching setup-F3:
either "WONTFIX — defensible ordering, machine-before-repo" or fold a sentence
into #19. Silent omission of even a minor is the one coverage gap.

### SUGGESTED

**S1 — #12 is labelled "BLOCKER + MAJOR"; consider splitting or clarifying.**
#12 bundles AX B2 (blocker), AX M2 (major), setup-F8 (minor), pm-session-F8
(minor) into three sub-defects. The bundling is *coherent* (all three are the
setup→runtime routing seam) and the epic build-order places it second, which is
right. But a single ticket with a compound severity is harder to triage and
close — item 1 (bootstrap exclusion, ~5-minute doc edit) and item 3
(completeness-check atomic marker, a small design decision) are very different
sizes. Not a must-fix; the ticket is internally clear. *Suggestion:* keep as
one ticket but note in the epic that #12-item-1 can ship in the same wave as
#13/#18 (cheap doc edits) while #12-item-2/3 belong with #11 (the routing
core).

**S2 — #11's acceptance criterion #1 is slightly soft.** "A `grep` … finds no
bare `tmp/`, `templates/`, `methodology/` path *that should be*
`.agentsmith/`-prefixed." The "that should be" clause reintroduces judgment into
what is meant to be a mechanical check — a `grep` cannot evaluate "should." In
practice some bare mentions are legitimate (e.g. prose naming the agentsmith
repo's own `methodology/` dir in `setup.md`, which runs *from* the agentsmith
repo, not `.agentsmith/`). *Suggestion:* reword to scope the check to the
**embedded runtime files only** (`pm-setup.md`, `methodology/*.md`, templates)
and state that within those files *every* such path must be prefixed — making
the grep truly binary. This also surfaces a real subtlety: `setup.md` itself is
NOT an embedded runtime file and its bare paths are correct; #11 should say so
to avoid an agent "fixing" `setup.md` wrongly.

**S3 — #15 and #19 both touch the Codex reviewer wiring; cross-links exist but
verify scope.** #15 (autonomous fresh-context review) and #19 (`codex review`
vs `codex exec` reconciliation) both edit `how-to-use-a-team.md`'s Codex
reviewer path. Both cross-reference each other ("coordinate with #15" in #19;
#15's fix specifies the `codex exec` path). This is handled — but #15 frames the
fix as "Codex → a fresh `codex exec` run" while #19's whole point is that
`codex review` (code) vs `codex exec` (architecture/QA/design) must be
*distinguished*. If #15 lands first and writes "use `codex exec`" flatly, #19
then has to re-nuance it. *Suggestion:* state in the epic that #15 and #19 ship
in the same wave and #15's `how-to-use-a-team.md` edit must already carry the
`codex review`-vs-`codex exec` distinction so #19 only validates it.

**S4 — #20's "PM-name sourcing" overlaps #15/#16 territory lightly.** #20
includes "PM name unsourced" (pm-session F5) — the `<your-name>` placeholder.
This is purely a `pm-setup.md` + `collaboration.md` wording add. Since #11, #14,
#16, #17 already form a `pm-setup.md` wave (MF1), #20's pm-setup.md touch should
join that wave or be explicitly noted as a non-conflicting addition (it adds a
sentence, does not move steps). *Suggestion:* note in #20 that its `pm-setup.md`
edit is a localized addition that should land in or after the #11/#16
`pm-setup.md` wave to avoid a stale-line conflict.

**S5 — epic build order is sound but #13 could move earlier.** Priority order
#11→#20 is broadly correct (blockers first). #13 (commit step + verification)
is independent of the path/routing core, is the single biggest *setup* gap per
the source ("major … the single biggest setup gap"), and is a cheap, isolated
edit to `setup.md`. It currently sits third. *Suggestion:* #13 could ship in
wave 1 alongside #11/#12 — it has zero dependency on them and unblocks a clean
setup E2E re-run. Minor; current order is not wrong.

---

## 4. Coverage map — every source finding → ticket

### AX review findings

| Source | Severity (source) | Carried by | Faithful? |
|---|---|---|---|
| B1 — systematic path ambiguity | BLOCKER | #11 | Yes — #11 is a near-verbatim, well-expanded carry; also folds in m1. |
| B2 — `bootstrap/` dangling + unreachable re-setup | BLOCKER | #12 (items 1 & 2) | Yes — split into bootstrap-exclusion + re-setup dead-end; both parts present. |
| M1 — umbrella doc read last | MAJOR | #16 | Yes — accurate, with the re-sequence spelled out. |
| M2 — completeness-check mismatch | MAJOR | #12 (item 3) | Yes — accurate, incl. the atomic-marker fix and the #11 interaction. |
| M3 — `/loop` unexplained, Codex asymmetry | MAJOR | #17 | Mostly — see MF3 (over-claims "no Claude fallback"). |
| M4 — `README.md`/`VERSION` fate + "stamp" ambiguity | MAJOR | #18 | Yes — "stamp", embed verbs, README non-embed all carried. |
| m1 — intra-file bare-vs-prefixed path inconsistency | MINOR | #11 (named explicitly in "Known offenders") | Yes. |
| m2 — board-creation wording, newcomer gap | MINOR | #20 | Yes — "lazy-board wording" bullet. |
| m3 — `notifications.md` macOS/Tanay hardcode | MINOR | #20 | Yes — "notifications hardcoded" bullet. |
| m4 — no canonical `.agentsmith/` tree | MINOR | #20 | Yes — flagged as #20's priority item. |
| m5 — Codex review-role wiring not threaded | MINOR | #19 | Yes — "codex review vs codex exec" bullet. |

All 11 AX findings carried. No drop.

### Setup E2E friction log (F1–F10)

| Source | Severity (source) | Carried by | Faithful? |
|---|---|---|---|
| F1 — plugin checklist not true pass/fail | minor | #19 | Yes. |
| F2 — Codex required-or-optional unclear | minor (major Claude-only) | #19 | Yes — #19's severity line "MINOR (major on a Claude-only machine)" matches the source exactly. |
| F3 — plugin-check ordering (§1 before §2) | minor | **none explicit** | **GAP — see MF4.** Defensible to drop, but not dispatched. |
| F4 — `reflections.template.md` multi-placeholder ambiguity | minor | #18 | Yes — "reflections example block" bullet, source cited as "F9, F4". |
| F5 — `templates/` embedded verbatim, exists twice | minor | — | Source itself concludes "this is correct" and self-resolves ("Guessed/did … this is correct"); no fix needed. Acceptable non-carry; not a defect to fix. |
| F6 — empty `tmp/pm-docs/` won't survive clone | minor | #20 | Yes — "empty `tmp/pm-docs/`" bullet. |
| F7 — no commit step | major | #13 (item 1) | Yes — called out as "the single biggest setup gap". |
| F8 — two routers, circular routing, no front door | minor (major if unaided) | #12 (item 2) | Partially — #12 carries the circular re-setup route. The "no single front door / cross-link README" half of F8 is **not** explicitly carried by any ticket. See note below. |
| F9 — embed/instantiate/stamp terminology | minor | #18 | Yes. |
| F10 — no post-setup verification checklist | (the friction log #2 ranks it top-3) | #13 (item 2) | Yes. |

Note on F8: the AX review treats the circular-route as B2; the setup log's F8
additionally raises "no single front door — README not cross-linked from
setup.md/start-here.md." That second half (the README cross-link / front-door
ambiguity) is **not assigned to any ticket.** It is minor and the source notes
the human's routing resolved it, but like F3 it is silently absent. Recommend
dispatching it (a one-line "cross-link the README as the front door" could fold
into #18 or #20, or be explicitly WONTFIX'd).

### PM-session E2E friction log (F1–F8)

| Source | Severity (source) | Carried by | Faithful? |
|---|---|---|---|
| F1 — bare paths don't resolve from repo root | major | #11 | Yes — cited as "confirmed live". |
| F2 — issue-driven work mandatory, no remote | major | #14 | Yes — full carry incl. the on-disk stand-in fix. |
| F3 — fresh-context reviewer unspawnable, autonomy breaks | major | #15 | Yes — strong, faithful carry. |
| F4 — `/loop` not runnable in bounded session | minor | #17 | Mostly — see MF3. |
| F5 — PM name unsourced | minor | #20 | Yes. |
| F6 — `compaction.md` assumes task-list primitive | minor | #15 | Yes — #15's fix names the disk-based fallback. |
| F7 — operating mode genuinely ambiguous, "ask once" unreachable | minor | #15 | Yes — #15's fix gives `operating-modes.md` a documented default. |
| F8 — `start-here.md` "not set up" routes you out | minor | #12 (item 2) | Yes. |

All 8 pm-session findings carried.

### Coverage verdict

- **Blockers:** 2/2 carried (AX B1→#11, AX B2→#12).
- **Majors:** every major carried (AX M1–M4, setup-F7, pm-session-F1/F2/F3).
- **Minors:** 1 true gap (setup-F3, no disposition) + 1 partial gap (setup-F8's
  "front door" half). Both minor; both should be explicitly dispatched.
- **Non-carries that are correct:** setup-F5 (self-resolved in source as
  "correct, intentional").

No blocker or major was dropped, mis-stated downward, or silently merged. The
only coverage defects are two minors lacking an explicit dispatch.

---

## 5. Verdict

**This is a faithful, near-complete, build-ready triage — score 8.5/10.** The
hard part (severity-weighted completeness) is done well: both blockers and every
major are carried with high fidelity, often stated more sharply than the source.
The acceptance criteria are concrete enough to gate a fix wave. The epic's
"Done when" is measurable.

Before the fix wave starts, do four things:

1. **MF1** — declare a single `pm-setup.md` wave (#11 + #14 + #16 + #17 on one
   agent, #16's re-sequence authoritative, #14's and #17's steps slotted into
   it). Add the cross-links to #14 and #17, not just #16. This is the one real
   build hazard.
2. **MF2** — give the `bootstrap/`/`README.md` non-embed wording a single owner
   (#18) and a reciprocal cross-link in #12.
3. **MF3** — reword #17's problem statement so it does not claim "no Claude
   fallback exists" (the reload-doc analogy exists; it is just unstated).
4. **MF4** — explicitly dispatch setup-F3 (and setup-F8's front-door half) —
   WONTFIX with a reason, or fold a line into #19/#18. No minor should be
   silently absent.

With those done, this issue set is ready to build, and the epic's path to a ≥ 8
re-review is realistic — the triage correctly identifies that the fixes are
"editing, no rethinking."
