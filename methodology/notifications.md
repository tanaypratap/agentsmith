# Notifications

How a PM keeps the human updated without the human watching the screen — especially during long autonomous runs.

## The pattern

Replace `<YOUR_NAME>` with the human's name — the person the PM reports to. It comes from how the human addressed you in their own instructions at session start, **not** from `.agentsmith/pm-setup.md` (which sets the *PM's* handle, `<your-name>` — a different placeholder). If the human's name was never given, drop it and use a neutral greeting.

**macOS:**

```
osascript -e 'display notification "MSG" with title "Agent update"' \
  && say -v Samantha -r 165 "Hey <YOUR_NAME> — MSG"
```

- `osascript` — a silent visual notification in Notification Center.
- `say` — the spoken, audible part (macOS only).

**Linux / Windows (or any non-macOS machine):**

```
echo "[Agent update] MSG"
```

On Linux you can also use `notify-send "Agent update" "MSG"` if `libnotify` is available. On Windows, `msg * "MSG"` works in a terminal. The `say` voice command is macOS-only — on other platforms, omit it entirely; the text echo is sufficient.

> If you are unsure which OS you are on, run `uname -s`. A result of `Darwin` means macOS; `Linux` means Linux; anything else, fall back to the plain `echo`.

## When to notify

- A milestone: ticket created, review posted, commit pushed, local verification complete, a wave done.
- A blocker that changes direction.
- A decision is needed from the human.
- Every 5–10 minutes during a long autonomous run, as a heartbeat.

Do **not** notify for every small command. Reserve it for status and attention moments.

## Message style

- Start with "Hey `<YOUR_NAME>`" (the human's name, per the placeholder above; omit it if unknown).
- Short and concrete: what is happening now, what changed, or what decision is needed.

## The day / overnight rule

The silent visual notification stays on always. The spoken (`say` / audio) part is governed by time of day:

- **Day** (09:00–22:00 local) — audio on.
- **Overnight, a human in the loop** (e.g. a late-night Co-PM session) — audio on; the human is there to hear it.
- **Overnight, an autonomous run active, no human present** — audio on, so blockers and milestones still surface.
- **Overnight, idle** (no autonomous run, no human) — audio off. Do not talk into an empty, sleeping room.

Rule of thumb: audio is on whenever a human can hear it or an autonomous run needs to surface something; off only overnight when nothing is running and nobody is there.
