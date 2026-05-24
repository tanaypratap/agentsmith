# agentsmith E2E First-Time Setup — Friction Log

**Test:** A fresh agent ("setup PM") performs agentsmith first-time setup, following `setup.md`
literally, into a brand-new target repo. The friction log is the deliverable.

- **Date:** 2026-05-22
- **agentsmith repo (read-only reference):** `<HOME>/github/agentsmith/` — VERSION `0.1.0`
- **Target repo (created for this test):** `<HOME>/agentsmith-e2e-target/`
- **Entry point:** `setup.md` (per the human's instruction)

---

## 1. ACTION LOG

In order, exactly what the setup PM did:

1. Read `<HOME>/github/agentsmith/setup.md` — the entry point named by the human.
2. Listed the agentsmith repo root to see what `setup.md` references actually exist
   (`bootstrap/`, `methodology/`, `templates/`, `start-here.md`, `pm-setup.md`, `VERSION`, `README.md`).
3. Read `bootstrap/plugin-checklist.md` (referenced by setup.md step 1).
4. Read `VERSION` (`0.1.0`), and listed `methodology/`, `templates/`, `bootstrap/`.
5. Read the files setup.md step 3.1 says to embed: `start-here.md`, `pm-setup.md`, and
   `templates/reflections.template.md` (needed for step 3.2). Also read `README.md` for context.
6. **setup.md Step 1 — Machine check (plugins).** Ran `claude plugin list` — all 13 expected
   Claude Code plugins present and enabled. Ran `codex plugin list` and `codex mcp list` —
   `superpowers` + `notion` Codex plugins installed/enabled, `context7` MCP present/enabled.
   Per the test rules, plugins are pre-installed: verification passes, nothing installed or modified.
7. Created the target repo: `mkdir -p <HOME>/agentsmith-e2e-target`, `git init`,
   wrote a trivial `README.md`, committed it (`Initial commit`, `1cf08ce`).
8. **setup.md Step 2 — Repo check.** Looked for `.agentsmith/` in the target repo — absent → continue.
9. **setup.md Step 3.1 — Embed.** Created `.agentsmith/`; copied verbatim from the agentsmith repo:
   `methodology/` (dir), `templates/` (dir), `start-here.md`, `pm-setup.md`.
10. **setup.md Step 3.2 — Instantiate `reflections.md`.** Created `.agentsmith/reflections.md`
    from the now-local `.agentsmith/templates/reflections.template.md`, replacing the `<REPO>`
    placeholder with `agentsmith-e2e-target`.
11. **setup.md Step 3.3 — Scratch area.** Created `.agentsmith/tmp/` with an empty
    `.agentsmith/tmp/pm-docs/` inside.
12. **setup.md Step 3.4 — Stamp VERSION.** Copied the agentsmith repo's `VERSION` to
    `.agentsmith/VERSION` (`0.1.0`).
13. **setup.md Step 4 — Gitignore.** No `.gitignore` existed in the target repo; created one
    containing `.agentsmith/tmp/`.
14. Verified: `git check-ignore` confirms `.agentsmith/tmp/` is ignored; `git add --dry-run`
    confirms every other `.agentsmith/` file is trackable; `diff -rq` confirms all embedded
    files/dirs are byte-identical to source (verbatim).
15. **setup.md Step 5 — Report.** Wrote this friction log; returned a summary verdict.

---

## 2. FRICTION LOG

### FRICTION 1 — Plugin checklist says "install only what is missing" but the test machine has more than the expected set
- **Where:** `bootstrap/plugin-checklist.md` § Claude Code / § Codex.
- **Expected:** A checklist I can mechanically pass/fail.
- **Found:** It frames the check as "verify present, install the gaps." It does not say what to
  do when *more* than the expected set is installed, nor how a fresh agent confirms a plugin is
  "healthy" vs merely "listed." `claude plugin list` shows `Version: unknown` for most plugins
  and `Status: ✔ enabled` — there is no objective health signal. The Codex marketplace listing
  is ~150 plugins long; the agent must eyeball three names out of it.
- **Guessed/did:** Treated "listed + enabled" as passing. Did not install anything (also per
  test rules). Extra plugins = harmless, ignored.
- **Severity:** minor.

### FRICTION 2 — setup.md step 1 references "Codex plugins/MCP" but never says Codex is required
- **Where:** `setup.md` §1; `bootstrap/plugin-checklist.md` § Codex.
- **Expected:** Clarity on whether a machine without Codex installed at all is a failure, or
  whether Codex is optional (Claude-only setups are plausible).
- **Found:** The checklist assumes both `codex` and `claude` CLIs exist. A fresh agent on a
  Claude-only machine would hit `command not found` on `codex plugin list` with no guidance on
  whether that is a blocker or a skip. (On this machine `codex` exists, so no actual block.)
- **Guessed/did:** Both CLIs were present, so I ran both. A fresh agent elsewhere would have to
  guess whether a missing `codex` aborts setup.
- **Severity:** minor (would be major on a Claude-only machine).

### FRICTION 3 — Order of operations: setup.md says "check plugins" (step 1) before "check/create the target repo" (steps 2-3)
- **Where:** `setup.md` step ordering (§1 vs §2).
- **Expected:** As a setup PM handed a target-repo path, the natural first move is to confirm
  the target repo, then do machine-level setup.
- **Found:** setup.md puts the once-per-machine plugin check first and the repo work second.
  This is defensible (machine before repo), but it means if the repo is already set up (§2 says
  "report 'already set up' and stop"), the agent has already spent effort on the plugin check.
  Minor ordering oddity, not wrong.
- **Guessed/did:** Followed the written order: plugins first, then repo.
- **Severity:** minor.

### FRICTION 4 — `reflections.template.md` has multiple placeholder types; setup.md says only to fill the repo name
- **Where:** `setup.md` §3.2; `templates/reflections.template.md`.
- **Expected:** Clear instruction on every placeholder in the template.
- **Found:** setup.md §3.2 says "fill in the target repo's name." The template contains `<REPO>`
  (the heading) **and** `<DATE>`, `<reflection title>`, `<the situation>`, `<the durable lesson>`,
  `<the change in practice>` in an example reflection block. setup.md is silent on whether that
  example block should be kept as a template stub, removed, or filled.
- **Guessed/did:** Replaced only `<REPO>` (the one placeholder setup.md names) and left the
  example reflection block verbatim, reading it as an in-file template/example for future PMs.
  A different agent could reasonably delete that block — inconsistent outcomes across agents.
- **Severity:** minor.

### FRICTION 5 — `templates/` is embedded verbatim into `.agentsmith/`, so `reflections.template.md` exists twice
- **Where:** `setup.md` §3.1 (embed `templates/`) + §3.2 (instantiate `reflections.md` from it).
- **Expected:** Either the templates are carried for future use, or they are consumed by setup.
- **Found:** After setup, `.agentsmith/` contains both `templates/reflections.template.md` AND
  the instantiated `.agentsmith/reflections.md`. That is intentional — `pm-setup.md` instantiates
  `context-reload` and `pm-board` from `templates/` at PM-session time — but setup.md never says
  so. A literal fresh agent may wonder if leaving the consumed `reflections.template.md` behind
  is a mistake.
- **Guessed/did:** Kept `templates/` intact (verbatim embed per §3.1). Confirmed via `pm-setup.md`
  that the other two templates are needed later, so this is correct.
- **Severity:** minor.

### FRICTION 6 — `.agentsmith/tmp/pm-docs/` is created empty, but git does not track empty directories
- **Where:** `setup.md` §3.3 + §4.
- **Expected:** "Create `.agentsmith/tmp/`, with an empty `pm-docs/` inside it" implies the
  folder persists.
- **Found:** §4 then gitignores `.agentsmith/tmp/` entirely. Combined with git not tracking
  empty dirs, `pm-docs/` exists on disk now but will NOT survive a clone — the next agent on a
  fresh checkout gets no `tmp/pm-docs/`. `pm-setup.md` §4 tells the PM to `Make tmp/pm-docs/<name>/`,
  so it is re-created lazily and nothing breaks — but setup.md presents creating it now as
  meaningful when it is effectively a no-op for any cloned copy.
- **Guessed/did:** Created the empty `pm-docs/` as written. No `.gitkeep` added (setup.md doesn't
  mention one, and the dir is gitignored anyway).
- **Severity:** minor.

### FRICTION 7 — setup.md never says whether to `git add`/commit the new `.agentsmith/`
- **Where:** `setup.md` §3, §4, §5.
- **Expected:** Setup that says "everything else under `.agentsmith/` is committed" (§4) to also
  tell the agent to actually stage/commit it.
- **Found:** §4 states `.agentsmith/` "is committed" as a fact, but no step instructs the agent
  to run `git add`/`git commit`. §5 ("Report") only says to tell the human. The README's framing
  ("you have my GitHub access") implies committing is expected, yet setup.md stops short.
- **Guessed/did:** Did NOT commit — followed setup.md literally (it never instructs a commit),
  and the test rules also scope writes tightly. Left the files staged-able. A fresh agent is left
  guessing whether to commit; outcomes will differ between agents.
- **Severity:** major. This is the single biggest gap — "is committed" is asserted but never
  actioned, so a literal fresh agent leaves the repo with `.agentsmith/` uncommitted.

### FRICTION 8 — Two routers (`setup.md` and `start-here.md`) with overlapping/circular routing and no single front door
- **Where:** `start-here.md` vs `setup.md` vs `README.md`.
- **Expected:** One obvious entry point for a fresh agent.
- **Found:** The human pointed me at `setup.md` directly — fine. But `start-here.md` step 3 says
  "if not set up → go to the agentsmith repo and follow its `setup.md`," while `start-here.md`
  itself gets *embedded into* `.agentsmith/` by setup.md §3.1. So a post-setup repo contains a
  `start-here.md` whose step 3 tells you to leave and re-fetch agentsmith — only reachable if
  `.agentsmith/` is incomplete. The README documents both "first-time setup" and "PM session"
  entry phrasings, but neither `setup.md` nor `start-here.md` cross-links to the README. A truly
  fresh agent with no human routing would not know which file to open first.
- **Guessed/did:** Followed the human's explicit instruction (`setup.md`). Noted that without
  that instruction the entry point is ambiguous.
- **Severity:** minor (the human's instruction resolved it for this run; would be major for a
  fully unaided agent).

### FRICTION 9 — "Embed" vs "Instantiate" vs "Stamp" terminology defined inline, no glossary
- **Where:** `setup.md` §3.
- **Expected:** Defined verbs.
- **Found:** §3 uses three custom verbs — "Embed (copy verbatim)", "Instantiate ... from ...
  template", "Stamp ... with". They are parenthetically explained inline, so a careful reader
  copes, but a skimming agent could conflate "embed" and "instantiate" (e.g. instantiate the
  methodology, or embed the reflections template as-is without filling `<REPO>`).
- **Guessed/did:** Used the inline parentheticals: verbatim copy for §3.1, placeholder-fill for
  §3.2, file copy for §3.4. No glossary needed in the end, but the terms carry the whole
  procedure's correctness.
- **Severity:** minor.

### FRICTION 10 — No post-setup verification / acceptance checklist
- **Where:** `setup.md` §5.
- **Expected:** A "setup is correct if..." checklist so the agent can self-verify before reporting.
- **Found:** §5 ("Report") is a list of claims to *tell the human*, not a list of things to
  *verify*. There is no instruction to confirm the files actually copied, that `tmp/` is ignored,
  or that `VERSION` matches. A fresh agent that mis-copied a folder would still "report success."
- **Guessed/did:** Self-verified anyway (`diff -rq` verbatim check, `git check-ignore`,
  `git add --dry-run`) — but that diligence is not prompted by setup.md.
- **Severity:** minor.

---

## 3. END STATE

Final tree of the target repo `<HOME>/agentsmith-e2e-target/`:

```
agentsmith-e2e-target/
├── .gitignore                       (new — contains ".agentsmith/tmp/")
├── README.md                        (pre-existing scratch readme, committed)
└── .agentsmith/
    ├── VERSION                       0.1.0   (stamped from agentsmith repo)
    ├── start-here.md                 (embedded verbatim)
    ├── pm-setup.md                   (embedded verbatim)
    ├── reflections.md                (instantiated — heading: "Reflections — `agentsmith-e2e-target`")
    ├── methodology/                  (embedded verbatim — 13 files)
    │   ├── architecture.md
    │   ├── code-review.md
    │   ├── collaboration.md
    │   ├── compaction.md
    │   ├── design-critique.md
    │   ├── how-pms-work.md
    │   ├── how-to-use-a-team.md
    │   ├── issue-driven-work.md
    │   ├── notifications.md
    │   ├── operating-modes.md
    │   ├── qa.md
    │   ├── quality-bar.md
    │   └── worktrees.md
    ├── templates/                    (embedded verbatim — 3 files)
    │   ├── context-reload.template.md
    │   ├── pm-board.template.md
    │   └── reflections.template.md
    └── tmp/                          (gitignored scratch)
        └── pm-docs/                  (empty — present on disk, not git-tracked)
```

**Completeness check:** Everything `setup.md` §3-§4 directs is present.
- §3.1 embed — `methodology/`, `templates/`, `start-here.md`, `pm-setup.md` all present and
  byte-identical to source (`diff -rq` clean).
- §3.2 instantiate — `reflections.md` present, `<REPO>` filled with `agentsmith-e2e-target`.
- §3.3 scratch — `.agentsmith/tmp/pm-docs/` present (empty).
- §3.4 stamp — `.agentsmith/VERSION` = `0.1.0`, matches agentsmith repo.
- §4 gitignore — `.gitignore` created, `git check-ignore` confirms `.agentsmith/tmp/` ignored;
  `git add --dry-run` confirms all 20 other `.agentsmith/` files are trackable.

**It looks complete and correct** against a literal reading of setup.md, with two caveats:
(a) `.agentsmith/` is NOT committed — setup.md never instructs a commit (Friction 7);
(b) `tmp/pm-docs/` will not survive a clone, being an empty gitignored dir (Friction 6) — but
`pm-setup.md` re-creates it lazily, so this is cosmetic.

---

## 4. VERDICT

**Could a fresh agent complete first-time setup unaided? — Mostly yes.**
`setup.md` is short, ordered, and explicit enough that a careful fresh agent reaches a correct
`.agentsmith/` end state. The numbered steps and the inline definitions of "embed/instantiate/
stamp" carried the procedure. No blockers were hit. The frictions are gaps in precision and
completeness, not contradictions — every one was resolvable by picking the most literal reading.
The one substantive ambiguity is whether to commit (Friction 7): a literal agent leaves
`.agentsmith/` uncommitted, which contradicts setup.md's own assertion that it "is committed."

**Top 3 things to fix:**

1. **Add an explicit commit step (Friction 7).** setup.md §4 asserts `.agentsmith/` "is
   committed" but no step performs it. Add a step 4.5 / step 5 action: "Stage and commit
   `.agentsmith/` and `.gitignore` to the target repo (e.g. `git add .agentsmith .gitignore &&
   git commit -m 'Set up agentsmith'`)." Otherwise the assertion is false for a literal agent.

2. **Add a post-setup verification checklist (Friction 10).** §5 is "what to tell the human,"
   not "how to confirm it worked." Add a short acceptance checklist: methodology/ + templates/
   copied and non-empty, `reflections.md` has the repo name, `VERSION` matches, `git check-ignore
   .agentsmith/tmp/` passes. This converts "report success" into "verify, then report."

3. **Tighten the plugin checklist into a true pass/fail and handle the Codex-absent case
   (Frictions 1 & 2).** State explicitly: extra plugins are fine; "listed + enabled" = pass;
   and what to do if the `codex` CLI is absent entirely (skip with a note vs. block). Right now
   a fresh agent on a Claude-only machine has no guidance when `codex plugin list` errors.

**Honorable mention:** clarify in §3.2 what to do with the example reflection block's
non-`<REPO>` placeholders (Friction 4), and have setup.md / start-here.md cross-link the README
so a fully unaided agent has one front door (Friction 8).
