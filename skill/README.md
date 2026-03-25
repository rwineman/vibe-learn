# /learn — Contextual Code Lesson

A Claude Code skill that teaches you about the code you just wrote.

After a coding session, type `/learn` to get a brief lesson on a concept, pattern, or decision from your actual code — with a micro-exercise to reinforce it.

## Files

| File | Purpose |
|------|---------|
| `SKILL.md` | The skill itself — Claude Code loads this when you invoke `/learn` |
| `references/lesson-formats.md` | Detailed templates and worked examples for all 5 lesson types |
| `references/profile-schema.md` | JSON schemas for the knowledge profile and decision log |

## Usage

1. Work with Claude Code on your project as normal
2. Type `/learn`
3. Read the lesson (30-60 seconds)
4. Try the micro-exercise (10-15 seconds)
5. Ask Claude to reveal the answer

## What it tracks

The skill creates a `.vibe-learn/` directory in your project root (gitignored) with:
- `profile.json` — concepts you've learned, exercise level, session history
- `decisions.json` — architectural decisions from your sessions

This enables spaced repetition, progressive difficulty, and no re-teaching concepts you already know.

## Full documentation

See the [main README](https://github.com/rwineman/vibe-learn) for the full specification, design principles, and lesson type details.
