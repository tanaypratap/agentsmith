# Plugin Checklist

A once-per-machine health-check. Verify the plugins and MCP servers are present and working; install only what is missing. Re-running is safe — it skips anything already there.

## Claude Code

Check `claude plugin list`. The expected set (all from the `claude-plugins-official` marketplace):

`superpowers`, `notion`, `context7`, `frontend-design`, `pr-review-toolkit`, `code-review`, `commit-commands`, `feature-dev`, `claude-md-management`, `playground`, `skill-creator`, `typescript-lsp`, `session-report`.

Install a missing one with:

```
claude plugin install <name>@claude-plugins-official
```

## Codex

Check `codex plugin list` and `codex mcp list`. The expected set:

- `superpowers` — `codex plugin add superpowers@openai-curated`
- `notion` — `codex plugin add notion@openai-curated`
- `context7` (MCP) — `codex mcp add context7 --url https://mcp.context7.com/mcp`

Codex's review role is covered by its built-in `codex review`; the Claude-Code-internal plugins are deliberately not ported.

## Verify, don't reinstall

For each tool: list first, install only the gaps. Plugins install at user scope and persist across repos and sessions — a machine set up once stays set up. A plugin or MCP that is listed but failing should be reinstalled; one that is present and healthy is left alone.

## After install

Plugins load at session start — a freshly installed plugin applies on the next start of that tool, not the current session.
