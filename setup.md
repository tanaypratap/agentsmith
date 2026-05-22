# Setup — first-time

You are the **setup PM**. Follow these steps to set up a target repo. This is idempotent — safe to re-run.

**The repo model.** You run this from inside a clone of the **agentsmith repo**. The human tells you the **target repo** — the repo to be set up — by its path. You create `.agentsmith/` *in the target repo*, never inside the agentsmith clone itself. Throughout: "this agentsmith repo" = where you run from; "the target repo" = the path the human gave you.

**Terminology used in §3:**

- **Copy verbatim** — take the file or folder exactly as-is into the target location; do not alter content.
- **Instantiate** — copy a template and then fill in the repo-specific placeholders; the result is a live file, not a template.

> **Note on step order:** The plugin check (step 1) runs before the repo check (step 2) because plugins are machine-level concerns that must be in place before any agent work begins. If the machine is already equipped, step 1 is near-instant.

## 1. Machine check — plugins

Run the checklist in `bootstrap/plugin-checklist.md`. Verify the Claude Code and Codex plugins/MCP are present; install only what is missing. This is a once-per-machine concern — if the machine is already equipped, this step is near-instant. If you install anything, tell the human: a freshly installed plugin applies on the *next* start of that tool, not the current session.

## 2. Repo check

Look for `.agentsmith/VERSION` in the target repo.

- **It exists** → this repo is already set up (a completed setup always writes `VERSION` last). Report "already set up" and stop. (v0.1 does not re-sync; that is deferred to v2.)
- **It does not exist** → continue.

## 3. Create `.agentsmith/`

In the target repo, create `.agentsmith/` and populate it **in this order**:

1. **Copy verbatim** from this agentsmith repo into `.agentsmith/`: `methodology/`, `templates/`, `start-here.md`, `pm-setup.md`.

   > `README.md` and `bootstrap/` are intentionally **not** copied — `README.md` describes the agentsmith repo itself, and `bootstrap/` is a once-per-machine concern already handled in step 1. Neither belongs in the per-repo `.agentsmith/`.

2. **Instantiate** `reflections.md` at `.agentsmith/reflections.md` from the now-local `.agentsmith/templates/reflections.template.md`:
   - Replace `<REPO>` with the target repo's name.
   - Leave the example reflection block (`<DATE>`, `<reflection title>`, `<the situation>`, `<the durable lesson>`, `<the change in practice>`) intact as an in-file example for future PMs — do not fill those in or delete them.

3. **Create** the scratch area `.agentsmith/tmp/`. (The `pm-docs/` subfolder is re-created lazily by `pm-setup.md` when needed — no need to pre-create it.)

4. **Copy** this agentsmith repo's `VERSION` file to `.agentsmith/VERSION` verbatim. This records which agentsmith version set this repo up. **This is the last write — it is the setup completeness marker.**

## 4. Gitignore the scratch

Add `.agentsmith/tmp/` to the target repo's `.gitignore` (create `.gitignore` if absent). Everything else under `.agentsmith/` is committed; only `tmp/` is ephemeral.

## 5. Commit `.agentsmith/`

Stage and commit the new `.agentsmith/` folder and the updated `.gitignore` to the target repo:

```
git add .agentsmith .gitignore
git commit -m "Set up agentsmith"
```

## 6. Verify, then report

Before reporting to the human, run this checklist. All items must pass:

- [ ] `.agentsmith/methodology/` exists and is non-empty (spot-check at least one file).
- [ ] `.agentsmith/templates/` exists and is non-empty.
- [ ] `.agentsmith/reflections.md` exists and contains the target repo's name (not the literal string `<REPO>`).
- [ ] `.agentsmith/VERSION` exists and its content matches this agentsmith repo's `VERSION`.
- [ ] `git check-ignore .agentsmith/tmp/` exits 0 (confirms `tmp/` is gitignored).
- [ ] The commit created in step 5 is present in `git log --oneline -1`.

Once all items pass, tell the human: `.agentsmith/` created and committed; `methodology/` + `templates/` + entry files copied; `reflections.md` instantiated with the repo name; `tmp/` created and gitignored; `VERSION` recorded. The repo is now agentsmith-enabled — future agents start with `.agentsmith/start-here.md`, not this setup.

---

**What a populated `.agentsmith/` looks like** (after a successful setup):

```
.agentsmith/
├── VERSION                    ← agentsmith version that set this repo up
├── start-here.md              ← entry point for every future PM session
├── pm-setup.md                ← PM session flow
├── reflections.md             ← repo-specific retrospective (instantiated from template)
├── methodology/               ← how PMs work — all files copied verbatim
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
├── templates/                 ← board, context-reload, reflections templates
│   ├── context-reload.template.md
│   ├── pm-board.template.md
│   └── reflections.template.md
└── tmp/                       ← gitignored scratch area (re-created lazily)
    └── (pm-docs/ and other scratch files appear here at runtime)
```
