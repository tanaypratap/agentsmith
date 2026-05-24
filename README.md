# agentsmith

A portable, agent-bootstrappable repo. Point an agent at it and it sets up — into any repo, on any machine — the plugins you rely on and the PM operating model your agents work by.

## What it does

agentsmith carries two things:

- **Bootstrap** — a plugin/MCP checklist so a machine is equipped (Claude Code + Codex).
- **The methodology** — "how PMs work": operating modes, the team model, issue-driven work, worktrees, review & QA, collaboration, the quality bar, compaction readiness, notifications.

When set up in a repo, it creates a local `.agentsmith/` folder holding the methodology, the templates, and a scratch area — so any agent working that repo can read how to operate without fetching anything.

## Cloning / setting up agentsmith locally

agentsmith is its own repo — keep it that way; do not copy its source into other repos. To use it from any workspace, clone it once per machine:

```
git clone https://github.com/tanaypratap/agentsmith.git ~/github/agentsmith
```

That is the only setup agentsmith itself needs. From there, point an agent at the clone — `setup.md` bootstraps a target repo, `.agentsmith/start-here.md` runs every PM session after. See "The two ways you use it" below.

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
├── start-here.md          the local router (copied into .agentsmith/)
├── pm-setup.md            the PM session flow (copied into .agentsmith/)
├── bootstrap/
│   └── plugin-checklist.md  the Claude + Codex plugin/MCP health-check (not copied into repos)
├── methodology/           how PMs work — copied verbatim into every repo
├── templates/             board, context-reload, reflections — instantiated per repo
└── examples/              worked examples of the methodology in practice (this repo only; not copied)
```

## Where to go next

| You want to… | Start here |
|---|---|
| Set up a new repo | `setup.md` |
| Begin a PM session in an already-set-up repo | `.agentsmith/start-here.md` (in the target repo) |
| Understand the methodology | `methodology/` |
| See the methodology lived — a worked example | `examples/v02-validation/` |
| Check or install plugins | `bootstrap/plugin-checklist.md` |

## Built with its own methodology

agentsmith was built the way it tells you to work — epic + sub-issues, adversarial review before and during the build, iterative. See this repo's issues for the trail.
