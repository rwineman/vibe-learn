# vibe-learn

> A Claude Code skill that teaches vibe coders about the code their AI just wrote.

## Overview

`/learn` is an open-source Claude Code skill. After a coding session, the user invokes `/learn` and gets a brief, contextualized lesson on a concept, language feature, or pattern that Claude used — grounded in the actual code that was just written. Includes a quick-fire micro-exercise (10-15 seconds) to reinforce the concept.

100% local. No cloud, no accounts, no data leaving the machine.

## How It Works

1. User works with Claude Code on their project as normal
2. User invokes `/learn`
3. The skill analyzes the conversation context — what code was written, what decisions were made
4. Delivers a short lesson structured as:
   - **Concept** — what it is, in plain language
   - **In your code** — where and how it was just applied
   - **The decision** — why this approach was chosen over alternatives, what tradeoffs were made
   - **Quick challenge** — a 10-15 second micro-exercise to test understanding

## Lesson Format

### For new concepts (explain first, exercise after):

```
CONCEPT: [Name]

What it is:
[1-2 sentence plain-language explanation]

In your code:
[Reference to the specific code that was just written, with a simplified snippet]

The decision:
[Why this approach was chosen. What the conventional wisdom is, and whether/why
it was followed or broken. What was traded off.]

The trap:
[What could go wrong with this approach. When you'd outgrow it.
What to watch for.]

Quick challenge:
[A micro-exercise that takes 10-15 seconds max]

Answer: [hidden/revealed on request]
```

### For concepts you've seen before (exercise first, then deepen):

```
QUICK CHECK: [Concept Name]

You learned about [concept] on [date]. Let's see if it stuck:

[Exercise: predict output, spot the bug, etc.]

Answer: [revealed on request]

Going deeper:
[New angle on the concept — a tradeoff, a connection, a trap you haven't
seen yet. Builds on what you already know.]
```

The knowledge profile determines which format to use. New concept = explain first. Previously seen concept = challenge first, then deepen. This follows the research: retrieval practice (the "testing effect") significantly improves retention, but only works when there's something to retrieve.

## Lesson Types

Not every lesson is "here's a language feature." The skill should rotate across these types to teach *judgment*, not just syntax:

### Type 1: Concept Introduction
"Here's what [concept] is and how I just used it."
Best for: Language features, patterns, or tools the user likely hasn't seen before.

### Type 2: Decision Explanation
"The conventional wisdom says X, but I chose Y because of your specific situation."
Best for: Architectural choices, library selection, tradeoffs between approaches.

### Type 3: Hidden Trap
"This works now, but here's the trap. Here's when you'd need to change it."
Best for: Expedient choices (global variables, hardcoded values, localStorage vs. DB) where the user should understand the limits.

### Type 4: Road Not Taken
"Here's what I almost did, and why I didn't."
Best for: Teaching the user to see alternative approaches and understand why one was chosen over another.

### Type 5: Connection
"Remember [earlier concept]? This builds on it. Here's how they connect."
Best for: Building systemic understanding of how parts of the codebase relate. Requires knowledge profile history.

## Design Principles

- **Teach judgment, not just syntax.** The goal is to help the user think like a developer, not memorize keywords. Tradeoffs, decisions, and reasoning matter more than definitions.
- **Contextual, not generic.** Every lesson is grounded in code that was just written. Never feels like a textbook.
- **One concept at a time.** Don't overwhelm. Pick the single most interesting or important thing.
- **Respect the user's level.** Don't explain what a variable is to someone who's been coding for months. Calibrate based on conversation context and knowledge profile.
- **Brief.** The whole lesson should be readable in 30-60 seconds. The exercise should take 10-15 seconds.
- **No jargon without explanation.** If a technical term is used, define it inline.
- **Honest about tradeoffs.** Don't present choices as "the right way." Explain what was gained and what was given up.
- **Never frustrate.** Don't test people on things they haven't learned. Explain first, challenge later.
- **Require engagement, not passivity.** Every lesson has an exercise. The exercise is how learning actually sticks. Without it, this is just another explanation people skim past.

## Concept Selection Logic

When the user invokes `/learn`, the skill should pick a concept based on:

1. **Recency** — prefer concepts from the most recent code changes
2. **Novelty** — prefer concepts the user likely hasn't seen before (based on conversation context and knowledge profile)
3. **Impact** — prefer concepts that are foundational or widely applicable
4. **Decision richness** — prefer moments where a meaningful choice was made (tradeoff, alternative rejected, convention broken)
5. **Teachability** — prefer concepts that can be clearly explained in 2-3 sentences

Priority order: Interesting decisions with tradeoffs > novel high-impact concepts > familiar concepts applied in a new way > general best practices

## Micro-Exercise Types

Exercises unlock progressively as the user's knowledge profile grows. This keeps early sessions approachable and later sessions challenging.

### Available from day one (beginner-friendly):

- **Predict the output:** "What does this print?" (2-3 lines of code)
- **Fill in the blank:** "Complete this line to make it work: `___`"
- **True/false:** "This code will throw an error. True or false?"

### Unlocked after foundational concepts are learned:

- **Spot the difference:** "Which version is better and why?" (two small snippets)
- **What would you change?** "If this app needed to support 1000 users instead of 10, what's the first thing you'd change about this code?"

### Unlocked after significant profile history:

- **Prompt problem:** "Write a natural language prompt that would generate this code." Tests comprehension AND improves prompting ability — the core skill for vibe coders. Dual benefit: learn coding and learn to communicate better with AI agents.
- **Reorder (Parsons problem):** Scrambled code blocks — put them in the right order. Research shows this is as effective as writing code but takes half the time.

### Why progressive unlocking matters:

The knowledge profile isn't just about avoiding re-teaching — it's the mechanism that unlocks harder exercise types. The more you use `/learn`, the more interesting and challenging the exercises become. This creates a natural engagement loop.

## Local Knowledge Profile

All data stays on the user's machine. Stored as a simple local JSON file in the project directory (`.vibe-learn/profile.json`).

Tracks:
- **Concepts seen** — what has been taught, when, in what context, which exercise type was used
- **Blind spots** — concepts the agent uses frequently that the user has never asked about or been taught
- **Decision history** — a log of architectural decisions made in this codebase and the reasoning behind them (doubles as project documentation)
- **Exercise progression level** — determines which exercise types are available based on accumulated learning

This enables:
- Don't re-teach concepts the user already knows
- Thread concepts across sessions ("this builds on what you learned last week")
- Proactively surface blind spots ("you've never been taught about error handling, but your app uses it heavily")
- Difficulty and exercise type progression without explicit configuration
- Flip to exercise-first format for previously learned concepts (spaced retrieval)

The decision history is valuable beyond learning — it becomes a **living architectural decision record (ADR)** for the project. Even if someone stops using `/learn`, the decision log remains useful.

## Research Basis

Key findings from coding pedagogy research that informed this design:

- **Contextual/situated learning** produces deeper understanding than abstract exercises (Mills et al., 2025; Li et al., 2025)
- **Micro-learning** (single-concept, under 2 min) improves retention 25-80% vs. traditional instruction
- **The testing effect** (retrieval practice) produces significantly better retention (g = 0.50 across 159 studies) — but only when the learner has prior exposure to the concept
- **Scaffolded explanation with progressive disclosure** outperforms raw explanation (CodeAid, CHI 2024 — ~90% correct and helpful in 700-student deployment)
- **Parsons problems** are as effective as code writing but take half the time (ACM ITiCSE 2024)
- **Prompt problems** — a new AI-era exercise type where users write prompts to generate code — tested positively for comprehension and computational thinking (Denny et al., SIGCSE 2024)
- **Expert judgment develops through dual-process cognition** — pattern recognition (System 1) plus deliberate analysis (System 2). Teaching tradeoffs and decision reasoning accelerates this process (Baltes & Diehl, 2018)
- **Cognitive offloading is the central risk** of AI-assisted coding. Unstructured GenAI use hurts learning; structured scaffolding with required engagement improves it (Vibe-Check Protocol, 2026; Prather et al., 2025)

## Technical Implementation

### As a Claude Code Skill

- Skill name: `learn`
- Trigger: `/learn`
- Input: Current conversation context (Claude Code provides this automatically)
- Output: Formatted lesson text rendered in the terminal
- Local storage: `.vibe-learn/profile.json` in the project root
- No external dependencies, no API calls, no cloud services

### File Structure

```
.vibe-learn/
  profile.json        # knowledge profile — concepts seen, blind spots, level, exercise progression
  decisions.json      # architectural decision log for this project
```

## v1 Scope

**In scope:**
- `/learn` slash command as a Claude Code skill
- Analyze current conversation context to identify a teachable concept
- All five lesson types (concept, decision, trap, road not taken, connection)
- Beginner exercise types (predict output, fill-in-blank, true/false)
- Two lesson formats: explain-first (new concepts) and exercise-first (previously seen)
- Works with any programming language present in the conversation
- Local knowledge profile (`.vibe-learn/profile.json`) for cross-session continuity
- Local decision log (`.vibe-learn/decisions.json`)

**Out of scope for v1:**
- Advanced exercise types (prompt problems, Parsons problems) — these unlock in a future version once progression logic is proven
- Spaced repetition scheduling
- Any external service, cloud sync, or API integration
- Interactive exercise execution (exercises are text-based, answer revealed on request)
- Analytics or dashboards

## Success Criteria

- User learns something they didn't know in under 60 seconds
- Exercise feels doable, not intimidating — especially in early sessions
- Lesson feels relevant to what they just built, not generic
- "The decision" and "the trap" sections teach judgment, not just facts
- Users invoke it repeatedly because it's genuinely useful
- Exercise-first format for returning concepts feels like a satisfying "quiz" moment, not a frustrating test
- Decision log becomes something users actually reference later

## Open Questions

- [ ] How to handle sessions where Claude wrote very little code (e.g., mostly debugging or discussion)?
- [ ] Should `/learn` accept arguments? e.g., `/learn python` or `/learn advanced`
- [ ] How to avoid repeating the same concept type if invoked multiple times in one session?
- [ ] Best format for the "answer reveal" in the terminal — spoiler tags, separate command, etc.?
- [ ] Should the decision log be human-readable markdown instead of JSON?
- [ ] How to detect blind spots effectively — frequency analysis of concepts in code vs. concepts taught?
- [ ] What's the right threshold of "concepts seen" before unlocking intermediate exercise types?
- [ ] How to handle the knowledge profile when a user works across multiple projects?
