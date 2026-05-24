# Documentation Review

Adversarial review of a documentation deliverable. Staffed when the work is new prose — a new methodology file, a readme, a guide, a worked example.

## How it works

An **independent fresh-context sub-agent** (see `how-to-use-a-team.md`) reviews the document against this rubric and posts a scored review on the issue. Give the reviewer the document plus this rubric; it returns findings anchored in specific passages, not generalities.

Be genuinely adversarial. A soft documentation review is worse than none — prose that looks complete and correct can still fail to transfer understanding cleanly to a fresh agent.

## The documentation rubric

Score /10 across these dimensions; anchor each in a specific passage or structural choice:

- **Clarity** — can a reader new to the topic follow the writing without having to re-read or infer? Dings: ambiguous pronoun scope, sentences that require external context to parse, definitions deferred past their first use.
- **Accuracy / no-rot** — is every claim true against the canonical source files? Does the document agree with the files it cross-references? Dings: stale version labels, numbers that contradict the source, restatements that drift from the file they summarize. (Scope: *semantic correctness against source*; the existence and target-correctness of links is scored separately under Cross-link integrity.)
- **Completeness** — does the document cover its stated scope without omissions that would leave a reader stuck? Dings: a section promised in the intro but missing, acceptance criteria not testable from the text, a flow with an undocumented branch.
- **Navigability** — can a reader find the part they need? Do cross-links lead to the right file? Dings: no headers for a document longer than a screen, cross-links that reference a file title but not a section, dead links.
- **Voice** — does the document match the register of the repo? agentsmith uses direct, declarative prose: every sentence does work; no filler; no hedging where certainty is warranted. Dings: passive voice where active is available, filler phrases ("it is important to note that"), summaries that re-state rather than synthesize.
- **Cross-link integrity** — do all outbound references exist, point at the right place, and serve the reader? Dings: references to files not yet created (unless explicitly deferred), references that say "see X" without saying *what* in X to look for when the target is long. (Scope: *link mechanics* — existence and routing; semantic agreement of the linked content is scored separately under Accuracy / no-rot.)
- **Scope coherence** — does this document's job differ from every other file's job, with no duplication of canonical content that lives elsewhere? Dings: a passage that restates a rule from another file rather than cross-linking, two files independently authoritative on the same topic, a file whose contents would be at home in a sibling without the sibling losing anything. This is the rubric criterion that catches `anti-patterns.md` #8 (new docs overlapping existing canonical content).

**Scoring.** Rate each dimension **Strong / Adequate / Weak**, then compute `score = 10 − (1.5 × Adequate) − (3 × Weak)`, rounded, floored at 1.

**The gate is blockers, not the number.** A finding is a **blocker** if it would leave a reader stuck, cause them to act incorrectly, or if any dimension is rated Weak; the document is done when it has zero unresolved blockers. The /10 is a quality signal — a low score with no blockers means "publishable but weak," not "rejected." Separate blockers from **polish** (worth doing, not gating); surface at most the ~10 most important findings, blockers first, prioritized — do not dump.

## No self-scoring

The author never scores their own documentation. The score comes from the fresh-context reviewer. This holds for the same reason it holds everywhere else: the author's familiarity with the subject is exactly the source of the blind spot the reviewer exists to find.
