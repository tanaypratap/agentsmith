# Collaboration

How multiple PMs coordinate. The third bucket (workflow, individual, **collaboration**).

## Two collaboration surfaces — keep them distinct

- **The PM board** (`tmp/pm-board.md`) — PM-to-PM coordination: who is doing what, lanes, status, hand-offs. *Not* where work happens.
- **The GitHub issue** — where the work itself lives: the spec, the implementation evidence, the review trail. See `issue-driven-work.md`.

If it is "what is everyone up to / what do I need from you," it goes on the board. If it is the work or its review, it goes on the issue.

## The PM board

Created lazily — a solo PM does not need one. The first PM to notice a second PM is active creates the board (from `templates/pm-board.template.md`, at `tmp/pm-board.md`) and adds a row for every active PM.

Sections:

- **Active PMs** — one row per PM: name, lane, status. You own your row.
- **Current Focus** — one short section per PM: what you are on right now. You own your section.
- **Messages** — append-only, dated, attributed PM-to-PM notes.
- **Wins** — shared; notable shipped work.
- **Learnings** — shared; tactical, in-the-moment lessons (Reflections layer 1 — see below).
- **Open Asks** — shared; cross-PM requests.
- **Conventions** — shared; the board's own ground rules.

## Messages protocol

Messages are **append-only, dated, attributed**. Never edit another PM's message — reply with a new one. You own your Active-PMs row and your Current-Focus section; the shared sections (Wins, Learnings, Open Asks) anyone may add to.

## Open Asks

An Open Ask is a cross-PM request: who → whom, what, status. Mark it **closed** when resolved — never delete it. The trail is the point.

## Two-layer reflections

- **Layer 1 — board `## Learnings`.** Tactical, in-the-moment: "this bit me, here is the lesson." Ephemeral with the board.
- **Layer 2 — `reflections.md`** (committed, at `.agentsmith/reflections.md`; created by setup from a template — see the epic's embed-vs-instantiate rule). The deep periodic retrospective, owned by all PMs on the repo. Durable.

**Promotion:** when a layer-1 learning recurs — you see it twice, or it is clearly not a one-off — promote it into `reflections.md` as a considered reflection. Layer 1 is the scratchpad; layer 2 is what the repo durably knows.

## Multi-PM coordination

- Each PM owns a lane; lanes should not overlap. If they do, sort it on the board — do not collide.
- Sync on the board at the start of each work block, and at least every ~15 minutes during active multi-PM work — read others' updates before continuing.
- The board is gitignored scratch; it survives crashes on disk but is not committed.
