# Plugin Checklist

A once-per-machine health-check. Re-running is safe — it skips anything already present.

**Pass/fail rule:** A plugin or MCP is **passing** when it appears in the list output with an `enabled` status. Extra plugins beyond the expected set are fine — do not remove them. A plugin that is listed but not enabled, or is missing entirely, is a **fail** for that item; install or re-enable it. Once all expected items are listed and enabled, the checklist passes.

## Claude Code

Run `claude plugin list`. The expected set (all from the `claude-plugins-official` marketplace):

`superpowers`, `notion`, `context7`, `frontend-design`, `pr-review-toolkit`, `code-review`, `commit-commands`, `feature-dev`, `claude-md-management`, `playground`, `skill-creator`, `typescript-lsp`, `session-report`.

Install a missing one with:

```
claude plugin install <name>@claude-plugins-official
```

## Codex

> **Codex-absent machine:** If `codex` is not installed (`codex plugin list` returns `command not found`), skip this section entirely — note it to the human as "Codex not present; Claude Code plugins verified." This is not a blocker for Claude Code-only workflows.

Run `codex plugin list` and `codex mcp list`. The expected set:

- `superpowers` — `codex plugin add superpowers@openai-curated`
- `notion` — `codex plugin add notion@openai-curated`
- `context7` (MCP) — `codex mcp add context7 --url https://mcp.context7.com/mcp`

## Verify, don't reinstall

For each tool: list first, install only the gaps. Plugins install at user scope and persist across repos and sessions — a machine set up once stays set up. A plugin or MCP that is listed but failing should be reinstalled; one that is listed and enabled is left alone.

## After install

Plugins load at session start — a freshly installed plugin applies on the next start of that tool, not the current session.
