# PM Setup

You are starting a PM session in a repo where agentsmith is already set up. This file gets you running. Everything it refers to is local under `.agentsmith/` — you never need to fetch the agentsmith repo.

## 1. Pick your operating mode

Co-PM brainstorming, or autonomous. The human's instruction tells you which. See `methodology/operating-modes.md`.

## 2. Decide your team

You are the PM. Staff a designer / QA / adversarial code reviewer / adversarial architecture reviewer only as this session's work needs them. See `methodology/how-to-use-a-team.md` for the roster and the role→file map.

## 3. Get on the PM board

- If `tmp/pm-board.md` exists, add your row (Active PMs) and a Current Focus section.
- If it does not exist and you are the only PM, you do not need a board yet.
- The board is created lazily: the first PM to see a second PM active creates `tmp/pm-board.md` from `templates/pm-board.template.md` and adds, for every active PM, an Active-PMs row and a Current Focus section.

See `methodology/collaboration.md`.

## 4. Create your context-reload doc

Make `tmp/pm-docs/<your-name>/` and create your reload doc there from `templates/context-reload.template.md`. Keep it current — it is what survives a crash or a compaction. See `methodology/compaction.md`.

## 5. Set up your reminder loop

- **Claude:** start a `/loop` that periodically re-surfaces the quality bar (`methodology/quality-bar.md`).
- **Codex:** no live loop — the quality bar rides in your context-reload doc, so every reload re-grounds you.

## 6. Start working

Read `methodology/how-pms-work.md` first — it is the umbrella — then the methodology your role and mode need: your role's files are in the `how-to-use-a-team.md` map; for your mode, read `operating-modes.md` (plus `compaction.md` if you are autonomous). Then work: issue-driven, reviewed, to the quality bar.
