# agentsmith v0.2 — Holistic AX Re-Review

**Reviewer:** Independent adversarial AX reviewer, fresh context
**Subject:** `agentsmith` repo at `<HOME>/github/agentsmith/`, v0.2.0, commit `2d1a63a`
**Date:** 2026-05-22
**Prior review:** v0.1 scored 5.5/10 (1 review cycle ago)
**Bar to clear:** ≥ 8/10 with no blocker-class defect

---

## 1. Summary

v0.2 is a genuine, substantial repair of v0.1. I read every file end-to-end as one agent
experience — cold arrival at `README.md`, human-routed into either first-time setup
(`setup.md` → `bootstrap/plugin-checklist.md`) or a PM session
(`.agentsmith/start-here.md` → `pm-setup.md` → `methodology/`).

The two v0.1 blockers are both fixed and verified, not papered over:

- **B1** — I grep'd every runtime file for bare `tmp/`, `templates/`, `methodology/`
  paths. Zero hits. Every cross-cutting runtime path is `.agentsmith/`-rooted.
- **B2** — `bootstrap/` is now explicitly addressed in three places, and the
  `start-here.md` re-setup dead-end is replaced with an actionable instruction
  ("point me at your agentsmith repo so I can run setup from there").

All four majors and all five minors are addressed with real edits, not hand-waves.
The fix wave (#11–#20) did not introduce a blocker. The repo now reads as one
coherent experience with a closed setup→runtime seam.

The remaining findings are minor: a relative-vs-rooted path-style inconsistency
*inside* `methodology/` (cosmetic, resolves correctly), and a small number of
polish-level wording seams. None gate the release.

**v0.2 clears the bar.**

---

## 2. Score: 8.5 / 10

### Justification

| Dimension | Assessment |
|---|---|
| Cold-arrival clarity | Strong. `README.md` states what agentsmith is, the two intents, the layout, and a "where to go next" table. A fresh agent is correctly routed. |
| Routing correctness | Strong. The human-routes-the-agent constraint is stated up front in `README.md:16` and reinforced in `start-here.md`. Both intents have a clean, dead-end-free path. |
| Setup→runtime seam | Strong. The seam that sank v0.1 is closed: runtime files are `.agentsmith/`-rooted, `bootstrap/`/`README.md` exclusion is explicit and justified, and setup ends with a self-marker (`VERSION` written last) that `start-here.md` checks for. |
| Setup procedure | Strong. `setup.md` is idempotent, ordered, has a populate-order, an explicit commit step, a 6-item verification checklist, and a canonical post-setup tree. |
| Methodology coherence | Strong. The three buckets, roles model, review pipeline, no-self-scoring, compaction, collaboration all cross-reference cleanly and consistently. |
| Resilience / autonomous-mode realism | Strong. Remote-less fallback, no-fresh-context fallback, and stop conditions are all specified — autonomous mode is executable without a human, or blocks loudly. |
| Residual polish | Minor deductions: relative-path style inside `methodology/` is inconsistent with the rooted style everywhere else; a few wording seams (see §4). |

Not a 9–10 because of the path-style inconsistency inside `methodology/` (a real,
if cosmetic, inconsistency a strict reviewer must flag) and a handful of polish
seams. But there is **no blocker and no major** — the experience is sound
end-to-end. 8.5/10.

---

## 3. v0.1 Defect Status Table

| ID | v0.1 Defect | v0.2 Status | Evidence |
|---|---|---|---|
| **B1** | Runtime files used bare `tmp/`/`templates/`/`methodology/` paths | **FIXED** | Grep of `start-here.md`, `pm-setup.md`, `templates/`, `methodology/` for bare `tmp/`\|`templates/`\|`methodology/` (not preceded by `.agentsmith/`) returns **zero hits**. Every cross-cutting runtime path is rooted: `pm-setup.md:7,13,14,18,22,30,36,52,56`; `start-here.md:7`; `compaction.md:13,14,23`; `collaboration.md:8,14,37`; `quality-bar.md:17`. The context-reload template `.agentsmith/methodology/quality-bar.md` at `context-reload.template.md:7`. |
| **B2** | `bootstrap/` dangling + unreachable re-setup dead-end in `start-here.md` | **FIXED** | `bootstrap/` is now explicitly handled in three places: `README.md:39` (layout: "not copied into repos"), `README.md:51` (where-to-go table), and `setup.md:31` (explicit "intentionally not copied" note with rationale). The re-setup dead-end is gone: `start-here.md:9` now gives an actionable instruction — "Please point me at your agentsmith repo so I can run setup from there" — instead of looping. `start-here.md:5` adds a "First time here?" note routing a misrouted agent to `setup.md`. |
| **M1** | `pm-setup.md` read the umbrella doc (`how-pms-work.md`) sixth | **FIXED** | `pm-setup.md:5` — "Step 1 — Read the umbrella doc" — `how-pms-work.md` is now read **first**, before anything else, with the explicit line "Everything below depends on that context" (`pm-setup.md:7`). |
| **M2** | `start-here.md`'s completeness check weaker than `setup.md`'s output | **FIXED (adequate)** | `start-here.md:7` checks `.agentsmith/VERSION` exists and explains *why* that is sufficient — "That file is written last by setup; its presence means setup completed cleanly." `setup.md:39` confirms `VERSION` is "the last write — it is the setup completeness marker." The two are now consistent: setup writes `VERSION` last as a deliberate completion marker, and `start-here.md` keys off exactly that. The checks are intentionally asymmetric (`setup.md` runs a full 6-item verification, `start-here.md` does a fast presence check) but the asymmetry is sound because of the write-last contract. Not a defect. |
| **M3** | `/loop` unexplained; Codex branch an absence | **FIXED** | `/loop` is now defined inline: `pm-setup.md:40` — "a Claude Code harness command that re-runs a prompt on a cadence in the background." Step 7 gives three concrete branches: Claude interactive (start `/loop`), Claude bounded / `/loop` unavailable (embed quality-bar in reload doc), and **Codex** (`pm-setup.md:50` — "No live loop … your context-reload doc carries the quality-bar section … That IS your reminder mechanism"). The Codex absence is now a stated, designed path, not a gap. `quality-bar.md:17-19` mirrors all three. |
| **M4** | `VERSION` "stamp" verb ambiguous; non-embedded files unstated | **FIXED** | `VERSION` semantics are concrete: `setup.md:39` — "Copy this agentsmith repo's `VERSION` file to `.agentsmith/VERSION` verbatim. This records which agentsmith version set this repo up." Non-embedded files are explicitly stated: `setup.md:31` — "`README.md` and `bootstrap/` are intentionally **not** copied" with rationale for each; `README.md:39` flags `bootstrap/` as "not copied into repos". The `setup.md` terminology block (`setup.md:7-10`) defines "Copy verbatim" and "Instantiate". |
| **m1** | Inconsistent path refs | **PARTIAL** | All *cross-cutting* runtime paths (board, reload doc, templates, methodology files referenced from `pm-setup.md`/`start-here.md`/templates) are now `.agentsmith/`-rooted and consistent. However, **inside `methodology/`**, sibling-file references are bare (`code-review.md:27` → `how-to-use-a-team.md`; `how-pms-work.md:21` → `issue-driven-work.md`, `worktrees.md`; `how-to-use-a-team.md:39` → `issue-driven-work.md`; `collaboration.md:39` → `reflections.md`). This is *internally* consistent (methodology files always reference siblings bare) and resolves correctly since they co-locate in `methodology/`, but it is a different style from the rooted refs used everywhere else, including rooted refs to methodology files *within `pm-setup.md`*. A strict reviewer flags this as a residual inconsistency. Minor — cosmetic, does not break navigation. |
| **m2** | Lazy-board ambiguity | **FIXED** | The lazy-board trigger is now precisely specified and consistent across both files: `pm-setup.md:28` and `collaboration.md:14` both say — any PM that "becomes aware of a second active PM — including a newcomer just joining — creates the board … and adds a row for every active PM." `pm-setup.md:27` covers the solo case ("you do not need a board yet"). No ambiguity remains. |
| **m3** | macOS-only notifications | **FIXED** | `notifications.md:20-28` now has an explicit "Linux / Windows (or any non-macOS machine)" block with `echo`, `notify-send`, and `msg *` alternatives, plus a `uname -s` detection tip (`notifications.md:27`) and an explicit "the `say` voice command is macOS-only — on other platforms, omit it" (`notifications.md:25`). |
| **m4** | No canonical tree | **FIXED** | `setup.md:69-97` adds a "What a populated `.agentsmith/` looks like" canonical tree listing every file and folder including the gitignored `tmp/`. `README.md:32-42` carries a complementary layout of the agentsmith repo itself. |
| **m5** | Codex reviewer wiring | **FIXED** | `how-to-use-a-team.md:22-26` now specifies Codex reviewer paths concretely: `codex review` for code-review specifically, `codex exec` (fresh non-interactive run) for architecture/QA/design reviewers, and a human-fresh-session fallback. The fresh-context requirement is stated as the invariant ("a process that never saw the producer's reasoning"). |

**Tally:** B1 fixed, B2 fixed, M1–M4 fixed, m2–m5 fixed, m1 partial (cosmetic residual). No defect is still open.

---

## 4. New Findings (introduced or surfaced by the fix wave)

No blocker or major was introduced. The following are minor / polish.

### Minor

**N1 — Path-style inconsistency inside `methodology/` (= residual m1).**
Methodology files reference sibling methodology files by bare filename, while
`pm-setup.md`/`start-here.md`/templates/`compaction.md` reference methodology
files with the full `.agentsmith/methodology/` prefix. Within a single experience
an agent sees both styles. It resolves correctly (siblings co-locate) and is
*internally* consistent, so it is not a navigation bug — but it is a real
inconsistency a strict review must record. Recommendation for a future polish
pass: either root all methodology cross-refs, or add one line to `how-pms-work.md`
stating "methodology files reference each other by bare name; they all live in
this folder." Severity: **minor**.

**N2 — `start-here.md` step numbering vs. the "router does nothing else" framing.**
`start-here.md:3` says "this file does nothing else," then `start-here.md:11`
adds two more imperatives ("Do not do setup work from here" / "do not re-fetch").
The instructions are correct and useful; the "does nothing else" line slightly
undersells them. Cosmetic. Severity: **minor / polish**.

**N3 — `setup.md` step 2 references "v0.1 does not re-sync … deferred to v2."**
`setup.md:22` carries a v0.1-era parenthetical inside a v0.2 repo. The behaviour
(no re-sync) is correct and intentionally documented, but the version label "v0.1"
is now stale against `VERSION` 0.2.0. A reader could briefly wonder whether the
file belongs to this release. Recommendation: say "agentsmith does not yet
re-sync an already-set-up repo" without the version number, or update to "v0.2."
Severity: **minor**.

**N4 — `pm-setup.md` Step 4 and `how-to-use-a-team.md` both name the role→file map.**
`pm-setup.md:22` says "the role→file map"; `how-to-use-a-team.md:15` says "the
role→methodology-file map." Same concept, two names one word apart. Trivial, but
a strict reviewer notes it. Severity: **minor / polish**.

### Observations (not defects)

- The `setup.md:22` already-set-up branch reports and stops with no re-sync — this
  is a deliberate v0.1/v0.2 scope boundary, correctly disclosed. Not a defect.
- `setup.md` step 3 says `pm-docs/` is "re-created lazily by `pm-setup.md`" — and
  `pm-setup.md:36` does create `.agentsmith/tmp/pm-docs/<your-name>/`. Cross-ref
  holds. Good.
- The no-fresh-context fallback (`how-to-use-a-team.md:32`) and the autonomous
  stop condition (`operating-modes.md:24-30`) together make autonomous mode
  genuinely safe to run without a human — it blocks loudly rather than
  self-scoring. This is a real strength of v0.2.
- `notifications.md` placeholder discipline (`<YOUR_NAME>` = human vs `<your-name>`
  = PM handle, called out explicitly at `notifications.md:7`) is precise and
  removes a real ambiguity. Good.

---

## 5. Remaining Setup→Runtime Seam Check

The seam that defined the v0.1 failure is closed:

1. **Path rooting** — every runtime file an agent reads *after* setup
   (`start-here.md`, `pm-setup.md`, `methodology/*` cross-cutting refs, both
   templates) refers to its world as `.agentsmith/...`. Verified by grep: no bare
   `tmp/`, `templates/`, or `methodology/` cross-cutting path survives.
2. **What setup produces == what runtime expects** — `setup.md`'s canonical tree
   (`setup.md:69-97`) lists exactly the files `pm-setup.md` and the methodology
   later read: `methodology/` (13 files), `templates/` (3), `start-here.md`,
   `pm-setup.md`, `reflections.md`, `VERSION`, `tmp/`. No file is read at runtime
   that setup does not create.
3. **The completion handshake** — setup writes `VERSION` last as the completion
   marker (`setup.md:39`); `start-here.md:7` checks exactly `VERSION`'s presence.
   The producer and the consumer agree on the same artifact.
4. **The exclusions are stated on both sides** — `README.md`/`bootstrap/` are
   declared not-copied in `setup.md:31` and `README.md:39`, so a runtime agent is
   never surprised by their absence under `.agentsmith/`.

The only residual seam is cosmetic (N1: bare vs rooted path style inside
`methodology/`). It does not break the experience — an agent reading
`methodology/code-review.md` finds `how-to-use-a-team.md` correctly because they
are siblings.

---

## 6. Verdict

**v0.2 clears the bar.**

- Score: **8.5 / 10** (bar: ≥ 8).
- **No blocker.** Both v0.1 blockers (B1, B2) are genuinely fixed and verified.
- **No major.** All four majors (M1–M4) are fixed.
- Minors: four of five v0.1 minors fully fixed; m1 is partially fixed (the
  cross-cutting paths are rooted, the residual is cosmetic path-style inside
  `methodology/`). Four small new polish findings (N1–N4), all minor.

The fix wave (#11–#20) did its job: the setup→runtime seam is closed, the
experience reads as one coherent flow from cold arrival to PM session, and
autonomous mode is executable-or-blocks rather than silently degrading. The
remaining findings are worth a future polish pass but none gates the release.

**Recommendation: v0.2 ships. Carry N1–N4 into a v0.3 polish backlog.**
