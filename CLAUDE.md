# vibe-learn

This is a Claude Code skill — a prompt-engineering project, not a traditional codebase.

## Project Structure

- `SKILL.md` — the skill entry point. This is the core deliverable. Claude Code loads this when `/learn` is invoked.
- `references/` — supporting docs loaded by SKILL.md for detailed templates and schemas.
- `SPEC.md` — the full product specification. Source of truth for behavior and design decisions.

## Key Conventions

- No runtime dependencies. The skill uses Claude's built-in tools (Read, Write, Glob, Grep) for all file I/O.
- Cross-platform. No shell scripts — everything works on Windows, Mac, and Linux.
- SKILL.md should stay under 500 lines. Move detailed reference material to `references/`.
- All JSON schemas are documented in `references/profile-schema.md`.
- Lesson format templates with examples are in `references/lesson-formats.md`.

## Runtime Data

When users invoke `/learn`, the skill creates a `.vibe-learn/` directory in their project root containing:
- `profile.json` — knowledge profile (concepts seen, exercise level, session log)
- `decisions.json` — architectural decision log

These files are gitignored and never committed to this repo.
