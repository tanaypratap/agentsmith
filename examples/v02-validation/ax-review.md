# agentsmith — end-to-end Agent-Experience (AX) review

Independent, adversarial review. Fresh context. Reviewed the whole repo at
`<HOME>/github/agentsmith/` (21 markdown files + VERSION) as ONE
agent-experience arc: cold arrival → human-routed → first-time setup → PM
session → working within the methodology. Read-only review.

---

## 1. Summary

agentsmith is a genuinely well-conceived idea and the *prose* is unusually
disciplined: every methodology file knows its boundary, the embed-vs-instantiate
distinction is articulated, the rubrics are consistent, and the Claude/Codex
fork is handled almost everywhere. But it was built in waves and the central
seam between the setup wave (`setup.md`, which creates `.agentsmith/`) and the
runtime wave (`pm-setup.md` + `methodology/`, which run *inside* `.agentsmith/`)
does not connect cleanly. The runtime files address paths relative to a working
directory that is never pinned down — they say `tmp/...` and `templates/...` and
`methodology/...` while actually living under `.agentsmith/`. A literal-minded
agent (especially Codex, with no plugin scaffolding to paper over ambiguity)
will create folders in the wrong place, fail to find templates, or guess. There
is also one true dead-end (`bootstrap/` is created in setup's mental model but
never embedded, yet `start-here.md`'s "incomplete" branch can route an agent
back to a setup it cannot reach) and a real ordering hazard in `pm-setup.md`.
None of these are fatal to a careful Claude agent, but the brief asked: can an
agent follow this *cleanly, unaided, end to end*? Not yet — it will stumble at
the setup/runtime seam.

---

## 2. Score

**5.5 / 10.**

Justification: The conceptual model is sound and the writing quality is high, so
this is not a 3. But AX is judged on whether an agent executes *without
guessing*, and there are two blocker-class defects (systematic path ambiguity
between `.agentsmith/`-relative and repo-root-relative; `bootstrap/` embed gap)
plus a genuine ordering hazard and several majors that each force a guess. A
repo whose entire purpose is to be agent-bootstrapped must be airtight on
exactly the mechanics it is loosest on. The score is dragged below 6 by the fact
that the *most-walked path* — every PM session, every repo, forever — is the one
with the path ambiguity. Fix the ~4 blockers/majors and this is an 8.

---

## 3. Findings by severity

### BLOCKERS

---

**B1. Systematic path ambiguity: runtime files use bare `tmp/`, `templates/`,
`methodology/` but the files live under `.agentsmith/`.**

Files/lines:
- `pm-setup.md:15` — "If `tmp/pm-board.md` exists…"
- `pm-setup.md:17` — "creates `tmp/pm-board.md` from `templates/pm-board.template.md`"
- `pm-setup.md:23` — "Make `tmp/pm-docs/<your-name>/` and create your reload doc there from `templates/context-reload.template.md`"
- `pm-setup.md:7,11,19,27,32` — "`methodology/operating-modes.md`", "`methodology/how-to-use-a-team.md`", etc.
- `methodology/compaction.md:7,13` — "your `tmp/pm-docs/<you>/` folder"
- `methodology/collaboration.md:7,14` — "`tmp/pm-board.md`"
- `methodology/how-pms-work.md:22,27` — "`tmp/pm-docs/<you>/`"

Failure mode: An agent's working directory in a PM session is the **target
repo root** (the human said "you're a PM, agentsmith is set up here" and the
agent is working that repo). `pm-setup.md` itself lives at
`.agentsmith/pm-setup.md`. When that file says "Make `tmp/pm-docs/<your-name>/`",
a literal agent resolves that against cwd and creates
`<repo-root>/tmp/pm-docs/...` — NOT `<repo-root>/.agentsmith/tmp/pm-docs/...`,
which is what `setup.md:24` actually created (`.agentsmith/tmp/` with
`pm-docs/` inside) and what `pm-board.template.md:45` explicitly states
("This board lives at `.agentsmith/tmp/pm-board.md`"). So the PM writes its
reload doc and board into a directory that (a) is the wrong location, (b) is
**not gitignored** — `setup.md:29` only gitignores `.agentsmith/tmp/`, so a
stray repo-root `tmp/` would get committed, and (c) won't be found by the next
agent that correctly looks under `.agentsmith/`. The contradiction is internal
and provable: `collaboration.md:7` says the board is at `tmp/pm-board.md` while
`pm-board.template.md:45` says `.agentsmith/tmp/pm-board.md`. The same applies
to `templates/` and `methodology/`: `pm-setup.md:32` says "read
`methodology/how-pms-work.md`" — from cwd that path does not exist; it is at
`.agentsmith/methodology/how-pms-work.md`.

Why it's a blocker: this is on the single most-walked path (every PM session in
every set-up repo). A careful Claude agent might infer "oh, everything is under
`.agentsmith/`" because `pm-setup.md` line 3 says "Everything it refers to is
local under `.agentsmith/`". But that is one sentence of disambiguation against
~12 bare relative paths, and a Codex agent or a fast Claude agent will take the
literal path. "Might infer" is not "cleanly, unaided."

Suggested fix: Pick ONE convention and apply it with zero exceptions. Cleanest:
make every path in every embedded file explicitly `.agentsmith/`-rooted
(`.agentsmith/tmp/pm-board.md`, `.agentsmith/methodology/how-pms-work.md`,
`.agentsmith/templates/...`). That matches `pm-board.template.md` and
`context-reload.template.md` (`context-reload.template.md:7` already writes
`.agentsmith/methodology/quality-bar.md`), so the templates become the correct
model and the runtime files are the outliers to fix. Alternatively, state once,
loudly, at the top of `pm-setup.md` and `start-here.md`: "All bare paths in
agentsmith files are relative to `.agentsmith/`; resolve them there, not against
your working directory" — but the explicit-root option is safer because it
removes the need for the agent to remember a rule across a dozen files and a
compaction.

---

**B2. `bootstrap/` is a dangling component — never embedded, and the only path
that would need it is unreachable.**

Files/lines:
- `setup.md:22` — embed list: "`methodology/`, `templates/`, `start-here.md`, `pm-setup.md`" — **`bootstrap/` is not in it.**
- `README.md:36-41` — the Layout block lists `bootstrap/` as a top-level dir of the agentsmith repo, and `README.md:40` comment for `methodology/` says "embedded into every repo" while `bootstrap/` has no such note.
- `setup.md:7` — "this agentsmith repo = where you run from".
- `start-here.md:7` — the "not set up / incomplete" branch: "Go to the agentsmith repo and follow its `setup.md`."

Failure mode: This is partly correct-by-design (the plugin check is
once-per-machine, not per-repo, so `bootstrap/` need not be copied into
`.agentsmith/`) — but it is never *stated* as a deliberate exclusion. `setup.md`
section 3 lists exactly what to embed and silently omits `bootstrap/`; an agent
cannot tell "omitted on purpose" from "omitted by mistake." Worse, it interacts
with a real dead-end: `start-here.md:7` tells an agent in an *incomplete*
`.agentsmith/` to "Go to the agentsmith repo and follow its `setup.md`." But a
PM-session agent reached `start-here.md` precisely because the human said
"agentsmith is set up here" — that agent has **no path, URL, or clone location
for the agentsmith repo**. Nothing in `.agentsmith/` records where the
agentsmith repo is. So if `.agentsmith/` is incomplete (e.g. a partial setup, or
a future-version mismatch), the agent is told to go somewhere it cannot locate
and cannot ask for cleanly. It will either hallucinate a path or stall.

Why it's a blocker: it is an unrecoverable dead-end on an error path that is
*more* likely than the happy path to be hit in real use (partial setups,
interrupted setup runs, the v0.1 "no re-sync" gap). An agent that hits it is
stuck.

Suggested fix: Two parts. (1) In `setup.md:22`, add an explicit line: "Note:
`bootstrap/` is intentionally NOT embedded — the plugin check is a
once-per-machine concern, not per-repo." (2) In `start-here.md`, change the
fallback so it does not assume the agent can find the agentsmith repo: "tell the
human the `.agentsmith/` here is incomplete and ask them to point you at the
agentsmith repo for a re-setup." Optionally, have `setup.md` write the
agentsmith repo's origin URL into `.agentsmith/VERSION` or a tiny
`.agentsmith/source` file so re-setup is self-locating.

---

### MAJOR

---

**M1. Ordering hazard in `pm-setup.md`: the umbrella doc is read LAST, after
five steps that depend on understanding it.**

Files/lines:
- `pm-setup.md:32` (step 6) — "Read `methodology/how-pms-work.md` first — it is the umbrella"
- `how-pms-work.md:1-3` — "The operating model agentsmith encodes. **Read this first.**"
- `pm-setup.md:5-19` — steps 1–3 already ask the agent to pick an operating mode, decide a team, and join the board *before* step 6.

Failure mode: `pm-setup.md` numbers its steps 1→6 and an agent executes them in
order. Steps 1–3 require judgment that `how-pms-work.md` exists to provide — the
autonomous-decision bar, the three buckets, the roles model. But the
instruction to read `how-pms-work.md` is step 6, and the word "first" inside
step 6 directly contradicts its position as the sixth step. An agent literally
cannot obey "read this first" when told to do so in step six. It will either
(a) follow the numbering and staff a team / pick a mode without the umbrella, or
(b) notice the contradiction and stall on which instruction wins. `how-pms-work.md`
itself reinforces the conflict by opening with "Read this first."

Suggested fix: Move "Read `methodology/how-pms-work.md`" to be step 1 of
`pm-setup.md` (or step 0, before mode selection). Re-sequence: read the umbrella
→ pick mode → decide team → board → reload doc → loop → work. The current step 6
prose can then shrink to "now read your role/mode files and start working."

---

**M2. `start-here.md` set-up check and `setup.md` completion definition disagree
on what "complete" means.**

Files/lines:
- `start-here.md:5` — "Check that `.agentsmith/` exists and contains `pm-setup.md` and `methodology/`."
- `setup.md:20-26` — a complete setup produces: embedded `methodology/`, `templates/`, `start-here.md`, `pm-setup.md`; instantiated `reflections.md`; `tmp/pm-docs/`; stamped `VERSION`.

Failure mode: `start-here.md` declares a repo "set up" if just two of seven
artifacts are present. An agent whose `.agentsmith/` has `pm-setup.md` and
`methodology/` but is missing `templates/`, `reflections.md`, `tmp/`, or
`VERSION` (a half-finished or interrupted setup — plausible given v0.1 has no
re-sync) will be routed by `start-here.md:6` straight into a PM session. It then
hits `pm-setup.md:17` / `:23` and tries to instantiate from
`templates/pm-board.template.md` or `templates/context-reload.template.md` —
which do not exist. Mid-session failure with no clean recovery, because the
agent already committed to "set up."

Suggested fix: Make `start-here.md`'s completeness check match `setup.md`'s
actual deliverable set — at minimum require `templates/`, `reflections.md`, and
`VERSION` in addition to `pm-setup.md` and `methodology/`. Better: have
`setup.md` write a single `.agentsmith/SETUP_COMPLETE` marker (or rely on
`VERSION` as the marker) as its *last* step, and have `start-here.md` check only
that one marker — an atomic, unambiguous signal.

---

**M3. The `/loop` reminder mechanism is unexplained and unavailable to a
Codex agent — and the asymmetry is under-specified.**

Files/lines:
- `pm-setup.md:26-28` (step 5) — "Claude: start a `/loop` that periodically re-surfaces the quality bar. Codex: no live loop."
- `quality-bar.md:16-19` — same Claude/Codex split.
- `bootstrap/plugin-checklist.md:18-26` — Codex's plugin set has no loop primitive.

Failure mode: `/loop` is a Claude Code feature; nowhere in the embedded files is
it explained what `/loop` is, how to start one, what cadence, or what the loop
should actually re-inject. A Claude agent reading `pm-setup.md:27` is told to
"start a `/loop`" with no argument, interval, or content spec — it must guess.
The Codex side is handled in *prose* ("the quality bar rides in your
context-reload doc") and `context-reload.template.md:5-7` does carry the bar, so
that path mostly works — but `pm-setup.md` step 5 for a Codex agent reduces to
"do nothing," which an agent may read as "step 5 is N/A, skip" without
registering that the *compensating mechanism* (reload doc must carry the bar) is
its responsibility. The dependency — Codex correctness here depends on the
reload doc actually being created in step 4 and kept current — is implicit.

Suggested fix: In `pm-setup.md` step 5, give the Claude branch a concrete
invocation and cadence (e.g. "`/loop` every ~30 min re-reading
`.agentsmith/methodology/quality-bar.md`") and give the Codex branch an explicit
positive instruction, not an absence ("Codex: confirm your context-reload doc
includes the quality-bar section — that IS your reminder mechanism; there is
nothing to start"). Cross-link to `compaction.md` so the dependency is visible.

---

**M4. `setup.md` does not say what happens to `README.md` and `VERSION` from
the agentsmith repo, and the `VERSION` "stamp" step is ambiguous.**

Files/lines:
- `setup.md:25` (step 3.4) — "**Stamp** `.agentsmith/VERSION` with this agentsmith repo's `VERSION`."
- `setup.md:22` — embed list omits `README.md` and `VERSION`.

Failure mode: "Stamp `.agentsmith/VERSION` with this agentsmith repo's
`VERSION`" — does "stamp" mean copy the file, or write the version *string*
(`0.1.0`) into a file? An agent will probably copy, which is fine, but "stamp"
is a non-standard verb here and `VERSION` was deliberately left off the embed
list in step 1, making step 3.4 read like a special case rather than a normal
copy. Minor on its own, but it compounds B2's "what's embedded and what isn't"
fog. Separately, `README.md` is correctly not embedded (it's about the
agentsmith repo itself), but that is never stated, so an over-thorough agent
might copy it into `.agentsmith/` and create a confusing artifact.

Suggested fix: In `setup.md` step 3.4, replace "Stamp" with explicit
instruction: "Copy this agentsmith repo's `VERSION` file to `.agentsmith/VERSION`
verbatim — it records which agentsmith version set this repo up." Add a one-line
note that `README.md` and `bootstrap/` are intentionally not embedded.

---

### MINOR

---

**m1. `pm-setup.md:32` references methodology files by bare name inconsistently
("`how-to-use-a-team.md` map", "`operating-modes.md`", "`compaction.md`") while
the same file elsewhere uses the `methodology/` prefix.**

`pm-setup.md:32` writes "`operating-modes.md`" and "`compaction.md`" with no
directory, but `pm-setup.md:7` writes "`methodology/operating-modes.md`" with
one. An agent sees the same file referred to two ways in one document and may
conclude they are different files, or waste a step resolving the discrepancy.
Fix: use one form consistently (and per B1, ideally the fully-`.agentsmith/`-
rooted form).

---

**m2. The board-creation rule is stated three times with slightly different
wording, and the "solo PM" branch has a latent gap.**

`pm-setup.md:15-17`, `collaboration.md:14`, and `pm-board.template.md` all
describe lazy board creation. They agree in substance, but `pm-setup.md:16`
("If it does not exist and you are the only PM, you do not need a board yet")
and `collaboration.md:14` leave one case unhandled: a *second* PM arriving when
the *first* PM never created a board (because PM-1 correctly skipped it as solo)
and PM-1 is now mid-session. `pm-setup.md:17` says "the first PM to see a second
PM active creates the board" — but if PM-1 is heads-down and never re-checks for
PM-2, PM-2 arrives, sees no board, and the rule says PM-2 should create it
(PM-2 *is* the one who "sees a second PM active" — itself). This works only if
PM-2 reads the rule as "whoever notices, including the newcomer, creates it."
The wording "first PM to see a second PM" is ambiguous about whether the
newcomer counts. Fix: state explicitly "any PM that becomes aware of a second
active PM — including a newly-arriving PM — creates the board if absent."

---

**m3. `notifications.md` is hardcoded to macOS and to the name "Tanay", with no
fallback.**

`notifications.md:7-9` gives an `osascript`/`say` command that only works on
macOS; `notifications.md:27` says "Start with 'Hey Tanay' (or the human's
name)." A portable repo ("into any repo, on any machine" — `README.md:3`) that
hard-codes a macOS-only notification path will silently no-op on Linux/Windows —
an autonomous agent will believe it is sending heartbeats that never arrive.
Fix: add a one-line "if not macOS, fall back to a printed status line / the
harness's own notification channel" and make the name a clear placeholder.

---

**m4. No glossary / no statement of what `.agentsmith/` looks like once
populated.**

There is no single place that shows the final shape of a set-up `.agentsmith/`
directory tree. `setup.md` describes the creation steps procedurally and
`README.md:32-42` shows the *agentsmith repo* layout, but an agent that wants to
verify "did setup produce the right thing?" or "where exactly does X live?" has
to reconstruct the tree from prose scattered across `setup.md`,
`pm-board.template.md`, and `compaction.md`. Given B1 (path ambiguity), a
canonical tree diagram of the populated `.agentsmith/` would be the single
highest-value addition. Fix: add a fenced tree diagram to `setup.md` (or
`start-here.md`) showing `.agentsmith/` fully populated, with the gitignored
`tmp/` marked.

---

**m5. Codex review-role wiring is asserted but not threaded into `how-to-use-a-team.md`.**

`plugin-checklist.md:24` says "Codex's review role is covered by its built-in
`codex review`." `how-to-use-a-team.md:22-25` (the fresh-context mechanism) tells
a Codex agent to "start a separate `codex exec` run … or a new Codex session" for
a reviewer — it does *not* mention `codex review`. So a Codex PM has two
different stories for "how do I run a reviewer": the checklist says use
`codex review`, the team file says spawn `codex exec`. They may both be valid
(`codex review` for code, `codex exec` for architecture/QA/design) but the repo
never reconciles them. Fix: in `how-to-use-a-team.md`, note when to use
`codex review` (code review specifically) vs. a fresh `codex exec` (the other
three reviewer roles), so the Codex path is unambiguous.

---

## 4. Verdict

agentsmith is a strong *draft* of a genuinely useful thing. The methodology
content is the best part — disciplined, boundaried, internally consistent on
concepts. The problem is mechanical, and it is concentrated exactly where an
agent-bootstrapped repo cannot afford it: the seam between "set up the
`.agentsmith/` folder" and "operate from inside it." The runtime files address
paths against an unstated working directory; the setup file's embed list has a
silent omission; the router's completeness check is weaker than the setup it
guards; and the umbrella doc is read sixth. A careful Claude agent with generous
inference will probably muddle through. A literal Codex agent, or a Claude agent
post-compaction working from a terse reload doc, will create folders in the
wrong place or hit a template that isn't there.

This is **not ship-ready as a "point an agent at it, unaided" tool** — it needs
one focused fix wave. Priority order: B1 (pick a path convention, apply it
everywhere — this alone lifts the score most), B2 (`bootstrap/` exclusion +
self-locating re-setup), M1 (re-order `pm-setup.md`), M2 (align the
completeness check). Those four are a few hours of editing, no rethinking. After
them, m4's canonical tree diagram is the cheapest remaining AX win. With that
wave done, this is an 8/10 repo — the bones are good.

**Score: 5.5 / 10.**
