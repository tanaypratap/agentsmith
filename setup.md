# Setup — first-time

You are the **setup PM**. Follow these steps to set up a target repo. This is idempotent — safe to re-run.

**The repo model.** You run this from inside a clone of the **agentsmith repo**. The human tells you the **target repo** — the repo to be set up — by its path. You create `.agentsmith/` *in the target repo*, never inside the agentsmith clone itself. Throughout: "this agentsmith repo" = where you run from; "the target repo" = the path the human gave you.

## 1. Machine check — plugins

Run the checklist in `bootstrap/plugin-checklist.md`. Verify the Claude Code and Codex plugins/MCP are present; install only what is missing. This is a once-per-machine concern — if the machine is already equipped, this step is near-instant. If you install anything, tell the human: a freshly installed plugin applies on the *next* start of that tool, not the current session.

## 2. Repo check

Look for `.agentsmith/` in the target repo.

- **It already exists** → this repo is already set up. Report "already set up" and stop. (v0.1 does not re-sync; that is deferred to v2.)
- **It does not exist** → continue.

## 3. Create `.agentsmith/`

In the target repo, create `.agentsmith/` and populate it **in this order**:

1. **Embed (copy verbatim)** from this agentsmith repo into `.agentsmith/`: `methodology/`, `templates/`, `start-here.md`, `pm-setup.md`.
2. **Instantiate** `reflections.md` at `.agentsmith/reflections.md` from the now-local `.agentsmith/templates/reflections.template.md` (this works only after step 1 has copied `templates/` in) — fill in the target repo's name.
3. **Create** the scratch area `.agentsmith/tmp/`, with an empty `pm-docs/` inside it.
4. **Stamp** `.agentsmith/VERSION` with this agentsmith repo's `VERSION`.

## 4. Gitignore the scratch

Add `.agentsmith/tmp/` to the target repo's `.gitignore` (create `.gitignore` if absent). Everything else under `.agentsmith/` is committed; only `tmp/` is ephemeral.

## 5. Report

Tell the human what was done: `.agentsmith/` created; `methodology/` + `templates/` + entry files embedded; `reflections.md` instantiated; `tmp/` created and gitignored; `VERSION` stamped. The repo is now agentsmith-enabled — future agents start with the PM-session instruction, not setup.
