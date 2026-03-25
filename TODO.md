# vibe-learn improvements

Identified 2026-03-03. Updated 2026-03-23. Ordered by impact.

## 0. Learning feedback loop (the big one)

The skill tracks what concepts you've *seen* but not what you *struggle with*. There's no feedback from exercise performance back into concept selection. This is the single biggest gap — it's what would make vibe-learn actually learn about the learner.

Missing pieces:
- **Answer tracking with correctness.** After the exercise, evaluate whether the user's attempt was right before revealing. Track pass/fail per concept.
- **Struggle profile.** Add `weakAreas` to `profile.json` — topics with wrong answers, follow-up questions, or repeated confusion. Different from `conceptsSeen`.
- **Weakness-weighted concept selection.** Step 3 should prioritize concepts connected to struggle areas, not just novelty.
- **Spaced repetition.** Wrong answers resurface later as harder exercises — this is where Level 3 exercises (item #5) could live.

This connects to items #3 (decisions.json), #5 (Level 3), and #6 (answer tracking) below — they're all parts of the same missing system.

## 1. Remove duplicate format templates from SKILL.md

SKILL.md lines 87-131 duplicate the format templates already in `references/lesson-formats.md`. Claude loads both, wasting tokens and creating a maintenance fork. Replace the inline templates with a one-liner: "Follow the exact templates in `references/lesson-formats.md`." Keep just the format names (A = Explain-First, B = Exercise-First) and selection rule (new concept → A, seen concept → B) inline.

## 2. Add sessionLog pruning

`sessionLog` in `profile.json` grows unboundedly — every `/learn` invocation appends an entry. After 100+ sessions the JSON gets large and Claude reads the whole thing on every invocation. Add a pruning step to Step 6: keep only the most recent 30 entries. `conceptsSeen` is fine (bounded by unique concepts), but the log needs a cap.

## 3. Make decisions.json useful or remove it

Currently write-only — the skill appends decisions but never reads them back. Two options:

- **Option A (use it):** In Step 3 (concept selection), read `decisions.json` and look for connections between past decisions and the current session's code. This makes the "Connection" lesson type richer — it can reference not just concepts but actual architectural decisions the user made previously. Adds ~10 lines to Step 3.
- **Option B (remove it):** Delete the decisions.json logic from Step 6 and the reference schema. YAGNI — if nobody reads it, don't write it.

Recommendation: Option A is more interesting and aligns with the skill's purpose. But if it's not pulling its weight after a few sessions, cut it.

## 4. Add Python/Svelte/Rust examples to lesson-formats.md

All five lesson type examples in `references/lesson-formats.md` are JavaScript. This subtly biases Claude toward JS-flavored lessons even in Python or Rust sessions. Either:

- Add one example per lesson type in Python (the most-used language across projects), or
- Add a note at the top of lesson-formats.md: "These examples use JavaScript for illustration. Always adapt the lesson content and code snippets to match the session's primary language."

The note is faster; real examples are better. Start with the note, add examples over time.

## 5. Define Level 3 exercises or collapse to 2 levels

Level 3 is currently "Level 2 + (future types)" — a placeholder. Either:

- **Define it:** Level 3 (25+ concepts) adds `refactor_this` (show working code, ask how to improve it) and `design_tradeoff` (present a scenario, ask which approach and why). These are more open-ended and test judgment, not just recall.
- **Collapse:** Remove Level 3, make Level 2 the ceiling. Simpler, and 25+ concepts is a lot of `/learn` invocations.

## 6. Track answer reveals

Step 7 (answer reveal) depends on the user asking in the same session, but there's no tracking of whether they do. Add an `answersRevealed` count to `profile.json` (increment in Step 7). Over time, a low reveal rate signals the exercises aren't engaging — useful signal for adjusting exercise types or difficulty.

## 7. Verify `allowed-tools` frontmatter

The Anthropic skill docs say frontmatter should only have `name` and `description`. The current `allowed-tools: Read, Write, Glob, Grep` field may be silently ignored. Test whether it actually restricts tool access. If not, remove it to avoid false confidence about what the skill can/can't do.
