# agentsmith

A portable, agent-bootstrappable repo. Point an agent at it and it sets up — into any repo, on any machine — the plugins you rely on and the PM operating model your agents work by.

## What it does

agentsmith carries two things:

- **Bootstrap** — a plugin/MCP checklist so a machine is equipped (Claude Code + Codex).
- **The methodology** — "how PMs work": operating modes, the team model, issue-driven work, worktrees, review & QA, collaboration, the quality bar, compaction readiness, notifications.

When set up in a repo, it creates a local `.agentsmith/` folder holding the methodology, the templates, and a scratch area — so any agent working that repo can read how to operate without fetching anything.

## The two ways you use it

A fresh agent cannot detect agentsmith on its own — your instruction routes it.

**First-time setup** (once per repo) — tell an agent:

> "You're the setup PM. Go to the agentsmith repo, you have my GitHub access, set this repo up."

It reads `setup.md`, checks plugins, and creates `.agentsmith/` in the target repo.

**A PM session** (every time after) — tell an agent:

> "You're a PM this session. agentsmith is set up here."

It reads `.agentsmith/start-here.md` → `pm-setup.md`, assembles its team, and works. It never re-fetches this repo.

## Layout

```
agentsmith/
├── setup.md               first-time setup procedure
├── VERSION                the agentsmith version stamp
├── start-here.md          the local router (embedded into .agentsmith/)
├── pm-setup.md            the PM session flow (embedded into .agentsmith/)
├── bootstrap/
│   └── plugin-checklist.md  the Claude + Codex plugin/MCP health-check
├── methodology/           how PMs work — embedded into every repo
└── templates/             board, context-reload, reflections — instantiated per repo
```

## Built with its own methodology

agentsmith was built the way it tells you to work — epic + sub-issues, adversarial review before and during the build, iterative. See this repo's issues for the trail.
