# agentsmith E2E — PM-Session Onboarding Friction Log

**Date:** 2026-05-22
**Tester:** fresh coding agent, role = PM, no prior agentsmith exposure
**Target repo:** `<HOME>/agentsmith-e2e-target/` (agentsmith pre-installed under `.agentsmith/`)
**Entry point:** `.agentsmith/start-here.md`
**agentsmith version under test:** `.agentsmith/VERSION` = `0.1.0`
**Scope:** exercise the entire PM-session onboarding flow (start-here -> pm-setup -> mode -> team -> board -> reload doc -> reminder loop -> start working). Anchor task invented to drive the flow: "add a CONTRIBUTING.md to this repo."

---

## 1. ACTION LOG — every step, in order

1. Read `.agentsmith/start-here.md`. It self-describes as a router. Checked: `.agentsmith/` exists and contains `pm-setup.md` and `methodology/` -> step 2 applies -> routed to `pm-setup.md`.
2. Listed the target repo and `.agentsmith/` tree. Confirmed contents: `pm-setup.md`, `start-here.md`, `reflections.md`, `VERSION`, `methodology/` (13 files), `templates/` (3 files), `tmp/pm-docs/` (empty).
3. Read `.agentsmith/pm-setup.md` — the 6-step onboarding checklist.
4. Tested whether `pm-setup.md`'s bare paths resolve from the repo root: `tmp/`, `templates/`, `methodology/` — none exist at repo root (FRICTION 1). They exist only under `.agentsmith/`.
5. Read the methodology files pm-setup routes to: `operating-modes.md`, `how-to-use-a-team.md`, `collaboration.md`, `compaction.md`, `how-pms-work.md`, `quality-bar.md`, `issue-driven-work.md`, `notifications.md`, `architecture.md`. Also read all 3 templates, `VERSION`, `reflections.md`, the reflections template, `.gitignore`, `README.md`.
6. **Step 1 — operating mode.** Human said "start a PM session" with a self-contained goal and no turn-by-turn presence. Per `operating-modes.md` ("set this goal and pursue it" = autonomous), chose **autonomous mode**.
7. **Step 2 — team.** Anchor task = a CONTRIBUTING.md = pure-documentation. Per `how-to-use-a-team.md` ("a pure-documentation task may need only the PM and an adversarial reviewer"), staffed **PM + adversarial architecture reviewer**. No designer, QA, or code reviewer (no UI, no behaviour, no code).
8. **Step 3 — PM board.** Solo PM. pm-setup step 3: "you do not need a board yet." Skipped board creation (correctly, per spec).
9. **Step 4 — context-reload doc.** Created `.agentsmith/tmp/pm-docs/Claude-PM/` and wrote `context-reload.md` from `templates/context-reload.template.md`, filled with real session state. (Chose PM name "Claude-PM" — pm-setup never assigns one; FRICTION 5.)
10. Checked GitHub remote + `gh` auth for the target repo: `gh` is authed, but the repo has **no git remote** (FRICTION 2).
11. **Step 5 — reminder loop.** pm-setup says "Claude: start a `/loop` re-surfacing the quality bar." A true open-ended loop never terminates inside a bounded session (FRICTION 4). Applied the documented fallback: embedded the quality bar verbatim at the top of the context-reload doc (the Codex-style mechanism), so every reload re-grounds it.
12. Checked for a harness task-list primitive (`TaskCreate`/`TaskList`) — none available (FRICTION 6). compaction.md step 3 ("load granular tasks in the harness task list") fell back to recording the task shape in the reload doc.
13. **Step 6 — start working.** Ran the anchor task through the issue-driven flow:
    a. Read `architecture.md` for the review rubric.
    b. Because the repo has no remote, created an on-disk stand-in for the GitHub issue: `.agentsmith/tmp/pm-docs/Claude-PM/issue-001-contributing-md.md` with a well-formed spec (scope / deliverables / context / acceptance criteria) per `issue-driven-work.md`.
    c. Produced the work: drafted `<HOME>/agentsmith-e2e-target/CONTRIBUTING.md` v1.
    d. Attempted to spawn a fresh-context adversarial architecture reviewer — no Agent/Task primitive available (FRICTION 3). Per `how-to-use-a-team.md`, the documented fallback is a human in a fresh session. Recorded the review as a PENDING hand-off package in issue-001 rather than fabricating a score (fabricating would break no-self-scoring).
14. Updated `context-reload.md` and `issue-001-contributing-md.md` to the true end state (anchor task drafted, blocked on fresh-context review).
15. Wrote this friction log.

---

## 2. FRICTION LOG — numbered

### FRICTION 1 — pm-setup.md uses bare paths that do not resolve from the repo root
- **Where:** `.agentsmith/pm-setup.md`, steps 3, 4, 5, 6 (and the line "Everything it refers to is local under `.agentsmith/`").
- **Expected:** A PM session's working directory is the target repo root. Paths in pm-setup should resolve from there.
- **Found:** pm-setup writes `tmp/pm-board.md`, `tmp/pm-docs/<your-name>/`, `templates/context-reload.template.md`, `templates/pm-board.template.md`, `methodology/...` — all **without** a `.agentsmith/` prefix. From the repo root, `tmp/`, `templates/`, `methodology/` do not exist; the real paths are `.agentsmith/tmp/`, `.agentsmith/templates/`, `.agentsmith/methodology/`. Worse, pm-setup *contradicts itself*: its own intro says "everything it refers to is local under `.agentsmith/`," yet its steps drop the prefix. `collaboration.md` and `pm-board.template.md` use the correct `.agentsmith/`-prefixed paths — so the methodology disagrees with itself file-to-file.
- **What I did:** Cross-checked against `.gitignore` (`.agentsmith/tmp/`) and `pm-board.template.md` (`.agentsmith/tmp/pm-board.md`), which confirm the real root is `.agentsmith/`. Used `.agentsmith/`-prefixed paths everywhere. Logged and continued.
- **Severity:** **major** — every step of pm-setup that creates a file points at a non-existent directory. A literal fresh agent either errors or silently creates a parallel `tmp/`/`templates/` tree at the repo root, fragmenting state.

### FRICTION 2 — issue-driven work is mandatory, but the target repo has no GitHub remote
- **Where:** `.agentsmith/methodology/issue-driven-work.md` ("All real work is tracked on GitHub issues"); `pm-setup.md` step 6 ("Then work: issue-driven ...").
- **Expected:** Either the repo has a remote so issues can be created, or the methodology states what to do when there is no remote.
- **Found:** `git remote -v` returns nothing for the target repo. `gh` is authed but `gh repo view` fails ("no git remotes found"). The entire issue-driven model — spec on issue, evidence as comments, review as comments, epic/sub-issue linkage — has no surface to land on. No methodology file addresses a remote-less repo, even though first-time setup clearly ran here.
- **What I did:** Created an on-disk stand-in (`issue-001-contributing-md.md` in my PM folder) carrying spec + evidence + review trail, and documented the substitution explicitly. Logged.
- **Severity:** **major** — issue-driven work is presented as non-negotiable ("no work that exists only in a chat log"), yet the flow has no fallback for the common case of a fresh/local repo with no remote.

### FRICTION 3 — the fresh-context reviewer cannot be spawned; fallback needs a human and breaks autonomy
- **Where:** `.agentsmith/methodology/how-to-use-a-team.md` ("The fresh-context mechanism"); `architecture.md`, `quality-bar.md` (no-self-scoring depends on it).
- **Expected:** In an autonomous PM session the PM can spawn an independent fresh-context reviewer without human involvement.
- **Found:** This harness exposes no Agent/Task sub-agent primitive. how-to-use-a-team.md's "Claude: use the Agent / Task tool" assumes one exists. Its fallback — "the human opens a fresh session and hands it the work" — requires a human, which directly contradicts autonomous mode (`operating-modes.md`: "the human is not in the loop turn-by-turn"). So in an autonomous session with no Task tool, the *mandatory* adversarial review has no executable path, and no work can ever reach "done."
- **What I did:** Identified the role + rubric + fresh-context requirement, drafted the work, and parked the review as a PENDING hand-off package in issue-001 rather than fabricating a score (which would violate no-self-scoring). Logged.
- **Severity:** **major** — adversarial review gates "done" for everything meaningful; if it cannot run autonomously, the autonomous mode cannot actually close any issue on its own.

### FRICTION 4 — the quality-bar `/loop` cannot run in a bounded session and is under-specified
- **Where:** `.agentsmith/pm-setup.md` step 5; `quality-bar.md` ("set up a reminder `/loop` at session start").
- **Expected:** A concrete, runnable instruction: which loop, what cadence, what command, and what to do if `/loop` is unavailable or the session is not long-lived.
- **Found:** pm-setup says only "start a `/loop` that periodically re-surfaces the quality bar" — no cadence, no exact command, no guidance for a bounded/non-interactive session where an open-ended loop would never terminate (and would block the deliverable). `/loop` is a harness skill that self-paces or takes an interval; pm-setup does not say which to use. There is no stated fallback for Claude (only Codex gets the "rides in your reload doc" mechanism).
- **What I did:** Applied the Codex-style mechanism by analogy — embedded the quality bar verbatim at the top of the context-reload doc so every reload re-grounds it. Logged.
- **Severity:** **minor** — the *intent* (keep the bar in view) is achievable via the reload doc, but the instruction is not literally executable as written.

### FRICTION 5 — pm-setup never tells the PM how to get its name
- **Where:** `.agentsmith/pm-setup.md` step 4 ("Make `tmp/pm-docs/<your-name>/`"); `collaboration.md` (Active-PMs rows keyed by name).
- **Expected:** Guidance on choosing/deriving the PM name (the human assigns it / derive from the tool / pick any unique handle).
- **Found:** `<your-name>` and `<PM name>` are used throughout pm-setup, collaboration, and the templates, but nothing ever says where the name comes from. For a solo PM it is cosmetic; for multi-PM coordination (board rows, message attribution, lane ownership) an unstated naming convention risks collisions.
- **What I did:** Chose "Claude-PM" myself and noted the assumption in the reload doc. Logged.
- **Severity:** **minor** — works for a solo PM; latent collision risk in multi-PM mode.

### FRICTION 6 — compaction.md assumes a harness task-list primitive that may not exist
- **Where:** `.agentsmith/methodology/compaction.md` pre-compaction checklist step 3 ("Ensure granular tasks are loaded in the harness task list"); post-compaction resume step 1 ("the task list points you here").
- **Expected:** Either the harness reliably provides a task list, or compaction.md names a fallback.
- **Found:** This harness exposes no task-list/TaskCreate primitive. compaction.md treats "the harness task list" as a given survivable surface and even routes post-compaction resume through it ("Load one explicit task: read the context-reload doc"). With no task list, that resume hook does not exist.
- **What I did:** Recorded the task shape inside the context-reload doc instead (compaction.md does say disk-written state survives), and made the reload doc self-bootstrapping (its TL;DR tells a fresh agent what to do first). Logged.
- **Severity:** **minor** — the reload doc covers the same need, but the methodology over-assumes harness features.

### FRICTION 7 — "operating mode is set by the human's instruction," but the instruction was genuinely ambiguous
- **Where:** `.agentsmith/methodology/operating-modes.md` ("Telling which mode you are in"); `pm-setup.md` step 1.
- **Expected:** The human's phrasing maps cleanly onto Co-PM vs autonomous.
- **Found:** The instruction was just "start a PM session" — it contains neither the Co-PM signal ("let's think through X") nor the autonomous signal ("go, don't wait for me"). operating-modes.md says "if it is ambiguous, ask once" — but in this harness there is no human to ask mid-task, so "ask once" is not actually available, mirroring FRICTION 3.
- **What I did:** Used the surrounding context (self-contained goal, no turn-by-turn human) to pick autonomous, and committed. Logged.
- **Severity:** **minor** — defensible default exists, but the "ask once" escape hatch is not reachable in a non-interactive run.

### FRICTION 8 — start-here.md's "not set up" branch tells you to "go to the agentsmith repo"
- **Where:** `.agentsmith/start-here.md` step 3.
- **Expected:** Consistent with the PM-session rule that sessions are 100% local and never re-fetch agentsmith.
- **Found:** start-here step 3 (the "not set up" branch) says "Go to the agentsmith repo and follow its `setup.md`." For *this* run step 2 applied so it did not bite, but the line is a latent trap: if a PM ever misjudges step 1 (e.g. FRICTION 1 makes `.agentsmith/` look "incomplete" because expected paths seem missing), start-here actively routes them out to the agentsmith source repo — exactly what the PM-session rules forbid.
- **What I did:** Confirmed step 2 applied and stayed local. Logged the latent contradiction.
- **Severity:** **minor** — did not trigger here, but it is a real "incomplete-looking setup -> bad route" hazard, and FRICTION 1 makes the setup genuinely look incomplete.

---

## 3. END STATE

**Files created in the target repo (`<HOME>/agentsmith-e2e-target/`):**
- `CONTRIBUTING.md` — anchor-task draft v1, at repo root. Untracked.
- `.agentsmith/tmp/pm-docs/Claude-PM/context-reload.md` — PM context-reload doc, instantiated from the template, filled with real session state, quality bar embedded at top. (Gitignored scratch.)
- `.agentsmith/tmp/pm-docs/Claude-PM/issue-001-contributing-md.md` — on-disk stand-in for the GitHub issue: well-formed spec + implementation evidence + review trail (review = PENDING hand-off). (Gitignored scratch.)

**Not created (correctly):** the PM board — solo PM, created lazily on a second PM.

**PM-session onboarding flow — coverage:**
- start-here routing: done.
- pm-setup step 1 (mode): done — autonomous.
- step 2 (team): done — PM + adversarial architecture reviewer; roster reasoning exercised.
- step 3 (board): done — correctly skipped for a solo PM.
- step 4 (reload doc): done — instantiated and kept current.
- step 5 (reminder loop): substituted — quality bar embedded in the reload doc (FRICTION 4).
- step 6 (start working): exercised — issue-driven spec written, work produced, reviewer role + rubric + fresh-context requirement identified.

**Was I actually ready to do PM work?** Mostly. The onboarding flow ran end to end and a fresh agent reloading my `context-reload.md` could continue cleanly. But the session is **not fully operational**: with no GitHub remote (FRICTION 2) and no spawnable fresh-context reviewer (FRICTION 3), the two load-bearing mechanisms — issue-driven tracking and mandatory adversarial review — both run on fallbacks, and the anchor task is correctly **blocked at "drafted, awaiting review"**, never reaching "done." An autonomous PM in this exact environment cannot close an issue without a human.

---

## 4. VERDICT

**Can a fresh agent start a PM session unaided?** Partially. The routing is clean (`start-here.md` -> `pm-setup.md`), the methodology is well-written and internally coherent in *content*, and the 6-step checklist is followable. A fresh agent reaches "onboarded and ready to take work." But it cannot run the loop to completion unaided: every "create a file" step in pm-setup points at a wrong path, and the two non-negotiable mechanisms (GitHub issues, fresh-context review) have no working path in a local, non-interactive, no-Task-tool environment. A capable agent recovers by inference; a literal one stalls or fragments state.

**Top 3 things to fix:**

1. **Fix the paths in `pm-setup.md` (FRICTION 1).** Every path in pm-setup steps 3-6 must carry the `.agentsmith/` prefix (`.agentsmith/tmp/...`, `.agentsmith/templates/...`, `.agentsmith/methodology/...`), matching `collaboration.md`, `pm-board.template.md`, and `.gitignore`. This is the single highest-impact fix — it currently makes every file-creating step of onboarding point at a non-existent directory, and contradicts pm-setup's own intro line.

2. **Give issue-driven work a remote-less fallback (FRICTION 2).** `issue-driven-work.md` presents GitHub issues as mandatory, but a freshly-set-up local repo routinely has no remote. Add an explicit fallback: an on-disk issue stand-in under the PM folder (spec + evidence + review trail), with a migrate-to-real-issue note — and have pm-setup check for a remote during onboarding.

3. **Make adversarial review runnable autonomously, or scope autonomous mode to it (FRICTION 3 + 4 + 6 + 7).** Several steps assume harness features that may be absent — a Task/Agent sub-agent tool, a `/loop`, a task list, a human to "ask once." `how-to-use-a-team.md`'s only no-sub-agent fallback needs a human, which breaks autonomous mode outright. Either specify a tool-agnostic fresh-context review path that an autonomous agent can execute alone, or state plainly that autonomous mode is unavailable without those primitives — so the no-self-scoring gate is never silently skipped.
