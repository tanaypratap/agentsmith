# Cross-machine collaboration

When two PM agents collaborate from **different machines** on the same problem, the PM board (`.agentsmith/tmp/pm-board.md`) is not enough. The board is local to a checkout; an off-board agent never sees it. This file is the protocol for collaborating across that boundary.

For *same-machine* PM-to-PM coordination, see `collaboration.md` — that doc covers the board, layer-1/layer-2 reflections, and same-checkout name handling. This file extends that doc for the cross-machine case.

---

## When this applies

You need this protocol when **at least one of these** is true:

- A second PM agent is running on a different machine (the off-board agent does not write to your `.tmp/`).
- A human steers each PM separately on their own machine (so each PM hears direction the other does not).
- The work touches one shared external surface (a GitHub issue, a PR thread) but the PMs cannot see each other's local state.

If you are the only active PM, ignore this file. If both PMs share a working tree, `collaboration.md` is sufficient. This file kicks in only when the boundary itself is the problem.

---

## The two coordination surfaces

Cross-machine PM collaboration has **two surfaces**, not one:

| Surface | Direction | Visibility |
|---|---|---|
| **In-band** | PM ↔ PM via a shared GitHub issue thread | Both PMs see it |
| **Out-of-band** | Human ↔ each PM on their own machine | Only that PM sees it |

The GitHub issue is the *convergence layer*, not the *complete picture*. The human is the real out-of-band sync between machines.

> **Anti-pattern: treating the issue as the complete coordination surface.** If you assume everything the other PM knows is on the thread, you will silently drift. The other PM is hearing things from the human you are not.

---

## The relay-and-reconcile protocol

Two rules. Both load-bearing.

### Rule 1 — Always relay human direction as an explicitly-labeled section

Whenever the human gives you direction on your machine that affects the shared work, your **next post** on the coordination thread must include a clearly-labeled section announcing it.

The header is the signal — the other PM scans for that exact phrasing:

```
### 🤖 <your name> — incorporating latest direction from <human>
```

or

```
### 🤖 <your name> — relaying latest direction from <human>
```

Inside the section, enumerate the direction points. Be explicit, not synthesized — the other PM needs to see what is *new direction* separate from your own analysis. If you fold relayed direction silently into a synthesis paragraph, the other PM cannot tell what is human-load-bearing and what is your judgment.

**Example (from `realtyledger#172`):**
> ### 🤖 Codex PM — incorporating latest direction from Tanay
>
> 1. **One fork is better.** … (relayed)
> 2. **Reviewer kit should have two layers — kit (framework) vs recipe (consumer-customizable).** … (relayed)
> 3. **Downstream consumers include detached consumers too.** … (relayed)
> 4. **FRS is a Mocktail feature, for now.** … (relayed)

Four direction points, each tagged. The other PM can scan, see what is new, and reconcile.

### Rule 2 — When the other PM relays direction with unfamiliar points, stop and reconcile

When the other PM posts a relayed-direction section containing points you have **not** heard from the human:

1. **Stop.** Do not move forward assuming you have the full picture.
2. **Ask.** Either ask the human directly (on your own machine) what else they have told the other PM, or ask the other PM on the thread:

   > *"Worth explicitly asking: since your last post, has \<human\> told you anything I should know about that isn't in this thread yet?"*

3. **Wait for the answer** before you commit to a position that depends on full-direction context.

This is the *reconcile* half of the protocol. The polling/notification half (rule 1's header) is necessary but not sufficient — it surfaces *that* direction landed, not whether you have all of it.

> **The failure mode this prevents:** silent drift. Each PM assumes the other has the same direction; neither asks; positions diverge slowly, then converge by luck (or do not converge at all). The human ends up acting as the convergence layer instead of the coordination one.

---

## In-band notification — polling is the transition mechanism

Use `gh api` to poll the coordination issue's comments at a steady cadence. The script is portable across shells (Claude Code, Codex CLI, etc.):

```bash
LAST=""
while true; do
  CUR=$(gh api repos/<owner>/<repo>/issues/<n>/comments \
          --jq '.[].id' | tr '\n' ' ')
  if [ -z "$LAST" ]; then
    LAST="$CUR"
    echo "[issue-<n>] baseline: $(echo $CUR | wc -w) comments"
  elif [ "$CUR" != "$LAST" ]; then
    NEW=$(comm -13 <(echo "$LAST" | tr ' ' '\n' | sort) <(echo "$CUR" | tr ' ' '\n' | sort))
    echo "[issue-<n>] NEW comment(s): $NEW"
    LAST="$CUR"
  fi
  sleep 90
done
```

**Cadence defaults:** 90s while actively debating, 300s when waiting in standby.

**Platform nuance** — same script, different wake-up models:

- **Claude Code:** launch via `Bash` with `run_in_background: true`. The poller's stdout pipes back into the running session as task-notifications — true async wake-up.
- **Codex CLI:** if Codex does not yet surface background stdout as live session events, run the script via `nohup ./poll.sh > /tmp/issue-<n>.log 2>&1 &` and `tail` that log at the top of each turn. Strictly better than fully manual checks; weaker than Claude Code's wake-up.

Polling is the **transition mechanism**. The real fix is event-driven issue collaboration tooling — see the agentsmith feature request for an `agentsmith watch-issue` capability with structured events, reconcile prompts, and per-thread cursors.

---

## Prefix conventions on the coordination thread

Each PM uses a consistent header prefix on every comment, including an emoji and the PM name. Examples:

- `### 🤖 Codex PM — <topic>`
- `### 🧭 Mocktail PM — <topic>`
- `### 🎨 Design PM — <topic>`

The convention is the same as the PM board's per-PM section headers. Pick a distinguishable emoji + name once and use it consistently for the session.

**Why this matters:** scan-readability. With dozens of comments on a thread, the other PM (and the human) needs to spot *who said what* at a glance. Plain comments without a prefix mix with human-authored comments and get lost.

---

## When convergence happens

The pattern observed on `realtyledger#172`:

1. **Both PMs post initial positions** with explicit prefix headers.
2. **Debate** — each PM iterates against the other's position, posting reasoned pushback. Polling catches each reply.
3. **Human (out-of-band) calls the load-bearing fork** — usually a question one or both PMs surface as "this is your call." The human's direction reaches each PM on their own machine.
4. **Both PMs post relayed-direction sections** (Rule 1) on their next turn. Both surfaces converge.
5. **PMs converge** in the next round — debate is on tactics now, not direction.
6. **PMs file follow-up implementation tickets** and split execution by machine/context.

Two PMs collaborating well on this protocol can converge from initial position to filed-tickets-of-record in 4–6 message exchanges (≈30–60 minutes at 90s polling).

---

## Connection to other agentsmith methodology

- `collaboration.md` — covers same-machine PM board mechanics. Use it for any active PM collaboration. This file extends it for the cross-machine case.
- `issue-driven-work.md` — the GitHub issue as the source of truth for work (not just coordination). Cross-machine collaboration leans on this twice: once for the *work* (per-ticket execution), once for the *coordination thread*.
- `principles.md` — particularly the principle around explicit human-direction handling. The relay-and-reconcile rule is a concrete application of that principle.

---

## Anti-patterns to avoid

- **Treating the GitHub issue as the complete coordination surface.** It is the *convergence* surface. The human is the *sync*.
- **Folding relayed direction into your own synthesis.** If you do not label what came from the human, the other PM cannot reconcile.
- **Posting your reply before reconciling unfamiliar direction.** If the other PM's last comment surfaced new points you have not heard, ask before you commit to a counter-position.
- **Polling without reconciling.** The notification ≠ the check. The check is on the PM.
- **Inconsistent prefixes.** A thread where one PM uses `### Codex PM` and another `Codex` and another no prefix is unscannable.
