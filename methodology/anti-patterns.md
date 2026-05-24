# Anti-Patterns

What NOT to do — distilled from the actual defects the v0.1 and v0.2 fix waves caught. Every entry is evidence-backed; none are invented. The authority of this file comes entirely from being grounded in real failures.

Each entry: **Pattern** (what people do) → **Why it's wrong** (what breaks) → **Evidence** (the specific v0.x finding) → **The fix** (the right move) → **See also** (the methodology file that handles it correctly).

---

## 1. Bare paths in runtime files

**Pattern.** Writing `tmp/foo.md` or `methodology/qa.md` in a file that lives under `.agentsmith/` and is read with the target repo as cwd.

**Why it's wrong.** A literal agent resolves bare paths against cwd (the target repo root), not against `.agentsmith/`. It creates `<repo-root>/tmp/pm-docs/...` instead of `<repo-root>/.agentsmith/tmp/pm-docs/...`, which is the wrong location, is not gitignored, and is not where the next agent looks. The same bare reference to `methodology/` fails at runtime because the runtime file lives at `.agentsmith/methodology/`, not at `<repo-root>/methodology/`.

**Evidence.** v0.1 AX blocker B1 (systematic path ambiguity across `pm-setup.md`, `compaction.md`, `collaboration.md`, `how-pms-work.md`); live-confirmed by v0.1 PM-session E2E friction F1 (from the PM-session friction log, distinct from the setup-E2E log's F1 on plugin checklist) — "bare paths don't resolve from repo root," rated major.

**The fix.** Apply the sibling-path-style rule. See principle #9 in `principles.md` for the full convention (one rule, three branches); do not restate it inline.

**See also.** `principles.md` principle #9 (sibling-path style); `validation-loops.md`.

---

## 2. Asserting an action without performing it

**Pattern.** Documentation states "X is done" or "Y is committed" as a fact, with no step that actually does the thing.

**Why it's wrong.** A reader following the documentation cannot produce the stated outcome — the step is missing. The doc and the reality diverge silently. An agent told "`.agentsmith/` is committed" will assume setup is complete and proceed into a PM session against a repo with no committed setup.

**Evidence.** v0.1 setup E2E friction F7 — "no commit step" — rated major, called "the single biggest setup gap." The setup docs stated the `.agentsmith/` folder existed without providing the `git add / git commit` step to persist it.

**The fix.** Every assertion the docs make must have a corresponding step that does the thing. For setup: `git add .agentsmith/` and `git commit` are explicit steps. For any doc: if you say "X is written," there must be a step that writes X.

**See also.** `.agentsmith/setup.md` (the fixed commit step and verification checklist).

---

## 3. Step-number references between files

**Pattern.** Referencing another file's numbered step — "see `pm-setup.md`, Step 6" — rather than referencing by role or section.

**Why it's wrong.** Step numbers are internal numbering that can change whenever the referenced file is re-sequenced (and v0.2 did re-sequence `pm-setup.md` as fix M1). A step-number reference rots silently: nothing in the repo signals that `collaboration.md:43`'s "Step 6" is now step 1.

**Evidence.** v0.2 wave-review finding S2 — "`collaboration.md`'s `Step` reference to `pm-setup.md`" identified as a real silent-rot hazard; the v0.2 wave remediation fixed the known instance.

**The fix.** Reference by role or section heading, never by number. "See the context-reload-doc step in `pm-setup.md`" survives re-sequencing. "See `pm-setup.md`, Step 6" does not.

**See also.** `issue-driven-work.md` (the principle that the issue body is the canonical spec, not positional references within it).

---

## 4. Cross-references that need a human to resolve

**Pattern.** Telling an agent to "go to the agentsmith repo" as a fallback without providing a path, URL, or clone location the agent can use.

**Why it's wrong.** An autonomous agent in a non-interactive run has no way to resolve "the agentsmith repo" — it cannot ask the human, it has no stored location for the source repo, and it will either hallucinate a path or halt the session. The fallback that requires human resolution is not a fallback at all for an autonomous agent.

**Evidence.** v0.1 AX blocker B2 — "`start-here.md`'s 'incomplete' branch tells an agent to 'go to the agentsmith repo' but the agent has no path, URL, or clone location." Confirmed by v0.1 PM-session E2E friction F8 — same dead-end reached in the wild.

**The fix.** Every fallback must be one an autonomous agent can take alone. If a human is genuinely required, the methodology says to surface the block explicitly ("Please point me at your agentsmith repo") — not to skip the gate or assume the agent can locate what it was never given.

**See also.** `operating-modes.md` (autonomous mode stop conditions); `how-to-use-a-team.md` (no-fresh-context fallback that blocks loudly rather than self-scoring).

---

## 5. Skipping a gate when the gate's mechanism is unavailable

**Pattern.** When no fresh-context reviewer is available, a PM self-scores "just this once" and declares work done.

**Why it's wrong.** Producer bias is exactly the failure mode the gate exists to catch. A PM that self-scores is giving itself the benefit of the doubt on the blind spots that the reviewer was meant to surface. "The reviewer is unavailable" is never a reason to skip the review — it is a reason to block.

**Evidence.** v0.1 PM-session E2E friction F3 — "fresh-context reviewer unspawnable, autonomy breaks" — rated major. The tester could not spawn a fresh-context reviewer and documented the friction; the correct behavior was to block on the issue, not to proceed.

**The fix.** Surface the block explicitly on the issue or on-disk stand-in. Send a human notification. Work stays open until a review runs. No self-scoring, ever. (See principle #2 in `principles.md`.)

**See also.** `how-to-use-a-team.md` (no-fresh-context fallback); `principles.md` principle #2.

---

## 6. Hardcoded environment assumptions

**Pattern.** Using OS-specific commands (macOS `osascript`, `say`), assuming a git identity exists, or mandating a GitHub remote without providing fallbacks.

**Why it's wrong.** A repo that claims to work "into any repo, on any machine" (agentsmith's own claim) that hard-codes macOS notification paths silently no-ops on Linux and Windows — an autonomous agent believes it is sending heartbeats that never arrive. An unchecked `git commit` on a machine with no identity fails mid-setup with a cryptic error.

**Evidence.** Three findings, distinct waves: (1) v0.1 AX minor m3 — `notifications.md` hard-coded to macOS and the literal name "Tanay"; (2) v0.1 PM-session E2E friction F2 — "no git remote, `gh issue` fails," rated major; (3) v0.2 setup-E2E re-verify minor — `git commit` step assumed a configured git identity (a v0.3 polish item added the pre-check).

**The fix.** Detect at boundaries. For notifications: include a `uname -s` detection block and provide Linux/Windows fallbacks. For git identity: pre-check `git config user.email`; if empty, surface a clear message and stop. For remote-less repos: provide the on-disk stand-in (see `issue-driven-work.md`).

**See also.** `notifications.md`; `issue-driven-work.md` (remote-less fallback).

---

## 7. Conflating similar-looking placeholders

**Pattern.** Using `<YOUR_NAME>` (the human's name) and `<your-name>` (the PM's handle) interchangeably, or using one where the other is meant.

**Why it's wrong.** These placeholders name different actors. The human's name appears in notification salutations ("Hey Tanay"). The PM's handle is a filesystem path segment under `tmp/pm-docs/<your-name>/`. Conflating them produces either awkward notifications or broken paths, and a reviewer that does not know the codebase cannot tell which is wrong.

**Evidence.** v0.2 wave-review must-fix MF1 — `notifications.md` used `<YOUR_NAME>` (the human's name) while `pm-setup.md` used `<your-name>` (the PM's handle); the conflation was invisible until the wave review ran the full set against acceptance criteria.

**The fix.** Name placeholders unambiguously. When both appear in the same file or same reader journey, disambiguate inline: "`<YOUR_NAME>` = the human's name; `<your-name>` = your PM handle (the filesystem slug you chose during PM setup)."

**See also.** `notifications.md` (the fixed version, which calls out the distinction explicitly).

---

## 8. New documentation that overlaps existing canonical content

**Pattern.** Restating a rule in a new file "for completeness" instead of cross-linking to where it already lives.

**Why it's wrong.** Every duplicated statement is a future drift hazard. When the canonical file updates, the copy does not. A reader who follows the cross-reference and finds a different statement now has two authorities in conflict and cannot tell which is right.

**Evidence.** This is a *preventive* anti-pattern — the discipline against it shows up in agentsmith's design choices, not in a single failed finding. The closest concrete analog in the v0.2 trail is the ticket-review's MF1: when five different tickets (#11, #12, #14, #16, #17) all needed to edit `pm-setup.md`, the review required all five edits to land in **one wave on one agent** so re-sequencing and step rewrites by different agents could not silently conflict. That is the same discipline (one canonical home, everything else coordinates with it) applied to *file editing* rather than to *content authoring*. The general rule is enforced inside the docs themselves by `docs-review.md`'s **scope coherence** dimension, which scores duplicate-canonical-content as a defect.

**The fix.** Every concept has ONE canonical home. Everything else points at it. A new methodology file's job is a job no existing file already does; where it overlaps with an existing file, it cross-links. `docs-review.md`'s accuracy/no-rot dimension makes this a scored rubric criterion.

**See also.** `docs-review.md` (accuracy / no-rot dimension); `principles.md` principle #8.

---

## 9. Reading docs vs. running them as the same signal

**Pattern.** Treating a holistic review as sufficient validation — concluding that because a document *reads* clear and correct, the flow it describes *runs* clean.

**Why it's wrong.** A document that looks clear and a flow that runs cleanly are two different properties. Docs fail at reading; flows fail at running. The v0.1 AX reviewer found B1 and B2 in reading — but did not catch the `git commit` gap (F7) that the E2E setup run found immediately. The two modes surface different failures.

**Evidence.** Every v0.1 E2E friction that the AX review did not catch on its own — including F7 (no commit step, rated major), which was invisible in a read-through and immediately apparent when actually running setup. Rated by the v0.2 triage as a distinct validation category.

**The fix.** Validation loops always include BOTH a holistic review (reads the experience end-to-end as one arc) AND an empirical E2E run (a fresh agent actually executes the flow and logs every friction). One does not substitute for the other. See `validation-loops.md`.

**See also.** `validation-loops.md` (the full loop that mandates both); `principles.md` principle #8.
