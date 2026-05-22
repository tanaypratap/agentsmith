# Notifications

How a PM keeps the human updated without the human watching the screen — especially during long autonomous runs.

## The pattern (macOS)

```
osascript -e 'display notification "MSG" with title "Agent update"' \
  && say -v Samantha -r 165 "MSG"
```

- `osascript` — a silent visual notification.
- `say` — the spoken, audible part.

## When to notify

- A milestone: ticket created, review posted, commit pushed, local verification complete, a wave done.
- A blocker that changes direction.
- A decision is needed from the human.
- Every 5–10 minutes during a long autonomous run, as a heartbeat.

Do **not** notify for every small command. Reserve it for status and attention moments.

## Message style

- Start with "Hey Tanay" (or the human's name).
- Short and concrete: what is happening now, what changed, or what decision is needed.

## The day / overnight rule

The spoken `say` is governed by time of day; the silent `osascript` notification stays on always.

- **Day** (09:00–22:00 local) — `say` on.
- **Overnight, a human in the loop** (e.g. a late-night Co-PM session) — `say` on; the human is there to hear it.
- **Overnight, an autonomous run active, no human present** — `say` on, so blockers and milestones still surface.
- **Overnight, idle** (no autonomous run, no human) — `say` off. Do not talk into an empty, sleeping room.

Rule of thumb: `say` is on whenever a human can hear it or an autonomous run needs to surface something; off only overnight when nothing is running and nobody is there.
