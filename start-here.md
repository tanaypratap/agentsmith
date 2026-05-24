# Start Here

You were pointed at a repo's `.agentsmith/`. Route yourself — this file is a router, its only job is to read the situation and hand off; the imperatives below clarify what "hand off" does and does not mean.

> **First time here?** If you are reading this from the agentsmith repo itself (not from inside a target repo's `.agentsmith/`), you want `setup.md` — it is the first-time setup procedure. See also `README.md` for an overview.

1. **Is this repo set up?** Check that `.agentsmith/VERSION` exists. That file is written last by setup; its presence means setup completed cleanly.
2. **Yes — it is set up** → read `pm-setup.md` and follow it. You are starting a PM session.
3. **No — not set up or incomplete** → `.agentsmith/` here is missing or was not fully set up. **Do not attempt to continue as a PM session.** Tell the human: "`.agentsmith/` in this repo is absent or incomplete (no `VERSION` file). Please point me at your agentsmith repo so I can run setup from there."

That is all. Do not do setup work from here. If step 2 applies, do **not** re-fetch the agentsmith repo — everything you need is already local in `.agentsmith/`.
