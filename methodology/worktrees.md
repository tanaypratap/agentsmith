# Worktrees

For parallel work, isolate each lane in its own git worktree so agents never collide on files.

## When you need one

- **Parallel lanes** — two or more agents building at the same time → one worktree each. Required.
- **Sequential, single-PM work** — no parallelism, nothing to collide with → no worktree needed. Do not add ceremony.

The rule is about *collision avoidance*, not ritual.

## How

```
git worktree add -b <lane-branch> .worktrees/<lane-name> main
```

- The `-b <lane-branch>` flag creates a **fresh branch for the lane**, off `main` (or the stable base). Never branch a lane off another lane's in-progress branch — that imports its mess and worsens the eventual merge.
- Use a clear, unique lane name per worktree — collisions on a branch name fail hard.
- Keep all worktrees under `.worktrees/`.

## Per-worktree setup

A fresh worktree does not contain gitignored files — `node_modules/`, `.env`, build output. If the target repo needs them, run its install/setup inside the new worktree before working.

## Gotchas

- **One branch, one worktree.** A branch can be checked out in only one worktree at a time.
- **Dependency / lockfile conflicts.** If two parallel lanes both change dependencies, the lockfiles conflict at merge. Avoid parallel dependency changes.
- **Drift.** A worktree that never pulls from `main` drifts; the longer it runs, the worse the merge. Pull regularly.
- **Clean up.** `git worktree remove` a lane once its work is merged.
