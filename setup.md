# Setup — first-time

You are the **setup PM**. A human has pointed you at the agentsmith repo to set up a target repo. Follow these steps. This is idempotent — safe to re-run.

## 1. Machine check — plugins

Run the checklist in `bootstrap/plugin-checklist.md`. Verify the Claude Code and Codex plugins/MCP are present; install only what is missing. This is a once-per-machine concern — if the machine is already equipped, this step is near-instant.

## 2. Repo check

Look for `.agentsmith/` in the target repo.

- **It already exists** → this repo is already set up. Report "already set up" and stop. (v0.1 does not re-sync; that is deferred to v2.)
- **It does not exist** → continue.

## 3. Create `.agentsmith/`

In the target repo, create `.agentsmith/` and populate it:

- **Embed (copy verbatim)** from this agentsmith repo: `methodology/`, `templates/`, `start-here.md`, `pm-setup.md`.
- **Instantiate** `reflections.md` at `.agentsmith/reflections.md` from `.agentsmith/templates/reflections.template.md` — fill in the repo name.
- **Create** the scratch area `.agentsmith/tmp/`, with an empty `pm-docs/` inside it.
- **Stamp** `.agentsmith/VERSION` with this agentsmith repo's `VERSION`.

## 4. Gitignore the scratch

Add `.agentsmith/tmp/` to the target repo's `.gitignore` (create `.gitignore` if absent). Everything else under `.agentsmith/` is committed; only `tmp/` is ephemeral.

## 5. Report

Tell the human what was done: `.agentsmith/` created; `methodology/` + `templates/` + entry files embedded; `reflections.md` instantiated; `tmp/` created and gitignored; `VERSION` stamped. The repo is now agentsmith-enabled — future agents start with the PM-session instruction, not setup.
