# PM Setup

You are starting a PM session in a repo where agentsmith is already set up. This file gets you running. Everything it refers to is local under `.agentsmith/` — you never need to fetch the agentsmith repo.

## Step 1 — Read the umbrella doc

Read `.agentsmith/methodology/how-pms-work.md` now, before anything else. It explains the operating model, the autonomous-decision bar, the three buckets, and the roles model. Everything below depends on that context.

## Step 2 — Pick your operating mode

Co-PM brainstorming, or autonomous. The human's instruction tells you which. See `.agentsmith/methodology/operating-modes.md`.

## Step 3 — Check for a git remote

Run `git remote -v`.

- **Remote present:** GitHub issues are your source of truth for all work tracking. Proceed normally.
- **No remote:** Use the on-disk issue stand-in. See `.agentsmith/methodology/issue-driven-work.md` — the "Remote-less fallback" section. All work gets tracked in your `.agentsmith/tmp/pm-docs/<your-name>/` folder until a remote exists.

## Step 4 — Decide your team

You are the PM. Staff a designer / QA / adversarial code reviewer / adversarial architecture reviewer / documentation reviewer only as this session's work needs them. See `.agentsmith/methodology/how-to-use-a-team.md` for the roster and the role→file map.

## Step 5 — Get on the PM board

The board is created lazily — a solo PM does not need one. Walk this decision in order:

- **`.agentsmith/tmp/pm-board.md` exists** → add your Active-PMs row and a Current Focus section.
- **It does not exist, and you are the only active PM** → no board yet; skip this step. (Create it later if a second PM appears.)
- **It does not exist, but a second PM is also active** → you (even as a newcomer) create the board now from `.agentsmith/templates/pm-board.template.md`, then add an Active-PMs row and a Current Focus section for every active PM.

See `.agentsmith/methodology/collaboration.md` for the board's structure and the messages/learnings protocols.

## Step 6 — Create your context-reload doc

Your name (`<your-name>`) is the unique handle you use across all agentsmith artifacts in this repo. Choose it once — it identifies your folder, your board row, and your reload doc. Pick something distinguishable from other active PMs.

Make `.agentsmith/tmp/pm-docs/<your-name>/` and create your reload doc there from `.agentsmith/templates/context-reload.template.md`. Keep it current — it is what survives a crash or a compaction. See `.agentsmith/methodology/compaction.md`.

## Step 7 — Set up your quality-bar reminder

**`/loop`** is a Claude Code harness command that re-runs a prompt on a cadence in the background. It keeps the quality bar in view so it cannot drift unnoticed over a long session.

- **Claude (interactive / long-running session):** Start the loop now:
  ```
  /loop 15m read .agentsmith/methodology/quality-bar.md
  ```
  This re-injects the quality bar every 15 minutes.

- **Claude (bounded / non-interactive session, or `/loop` unavailable):** Embed the quality-bar section at the top of your context-reload doc. Every reload and every compaction re-grounds you on it — the same effect, no live loop needed.

- **Codex:** No live loop — confirm your context-reload doc carries the quality-bar section (the template already includes it). That IS your reminder mechanism. There is nothing to start; the bar re-enters your context on every reload.

See `.agentsmith/methodology/quality-bar.md` and `.agentsmith/methodology/compaction.md`.

## Step 8 — Start working

Read the methodology files your mode and role need: for your mode, see `.agentsmith/methodology/operating-modes.md` (plus `.agentsmith/methodology/compaction.md` if you are autonomous); your role's files are listed in the `.agentsmith/methodology/how-to-use-a-team.md` roster. Then work: issue-driven, reviewed, to the quality bar.
