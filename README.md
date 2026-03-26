# vibe-learn

A Claude Code skill that teaches vibe coders about the code their AI just wrote.

After a coding session, type `/learn` and get a brief, contextual lesson on a concept, pattern, or decision that Claude used — grounded in the actual code that was just written. Includes a micro-exercise to reinforce it.

100% local. No cloud, no accounts, no data leaving your machine.

## Install

**macOS / Linux:**
```bash
git clone https://github.com/rwineman/vibe-learn.git
mkdir -p ~/.claude/skills
cp -r vibe-learn/skill ~/.claude/skills/learn
```

**Windows (PowerShell):**
```powershell
git clone https://github.com/rwineman/vibe-learn.git
mkdir -Force $env:USERPROFILE\.claude\skills | Out-Null
Copy-Item -Recurse vibe-learn\skill $env:USERPROFILE\.claude\skills\learn
```

You can delete the cloned repo after copying — the skill is standalone.

### Verify

Start a new Claude Code session and type `/learn` — it should appear in autocomplete.

## Usage

1. Work with Claude Code on your project as normal
2. Type `/learn`
3. Read the lesson (30-60 seconds)
4. Try the micro-exercise (10-15 seconds)
5. Ask Claude to reveal the answer

That's it. Each invocation teaches you one concept from the code you just built.

## What You Get

Every lesson includes:

- **What it is** — plain-language explanation of the concept
- **In your code** — where and how it was applied in your actual code
- **The decision** — why this approach was chosen, what was traded off
- **The trap** — what could go wrong, when you'd outgrow it
- **Quick challenge** — a micro-exercise to test understanding

## Lesson Types

The skill rotates across five types to teach judgment, not just syntax:

| Type | What it teaches |
|------|----------------|
| Concept Introduction | A language feature or pattern you haven't seen before |
| Decision Explanation | Why one approach was chosen over alternatives |
| Hidden Trap | A pragmatic choice that works now but has known limits |
| Road Not Taken | An alternative that was considered and rejected |
| Connection | How a current concept builds on something you learned before |

## Knowledge Profile

The skill tracks what you've learned in `.vibe-learn/profile.json` (in each project directory). This enables:

- No re-teaching concepts you already know
- Exercise-first format for previously seen concepts (retrieval practice)
- Progressive exercise difficulty as you learn more
- Cross-session continuity ("this builds on what you learned last week")

An architectural decision log (`.vibe-learn/decisions.json`) is also maintained — a living record of the decisions made in your codebase and why.

Both files are local to your project and gitignored by default.

## Design

See [SPEC.md](SPEC.md) for the full specification, research basis, and design principles.

## License

MIT
