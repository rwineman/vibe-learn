# Knowledge Profile & Decision Log Schemas

## Storage Location

All data lives in `.vibe-learn/` at the project root:

```
.vibe-learn/
  profile.json       # Knowledge profile
  decisions.json     # Architectural decision log
```

Create the directory and files on first `/learn` invocation if they don't exist.

---

## profile.json

Tracks what the user has learned, their exercise progression level, and session history.

### Schema

```json
{
  "version": 1,
  "created": "2025-01-15T10:30:00Z",
  "lastSession": "2025-01-16T14:20:00Z",
  "exerciseLevel": 1,
  "totalConceptsLearned": 3,
  "conceptsSeen": [
    {
      "name": "array-destructuring",
      "language": "javascript",
      "type": "concept_introduction",
      "firstSeen": "2025-01-15T10:30:00Z",
      "lastSeen": "2025-01-16T14:20:00Z",
      "timesEncountered": 2,
      "exerciseTypesUsed": ["predict_output", "true_false"]
    }
  ],
  "sessionLog": [
    {
      "date": "2025-01-15T10:30:00Z",
      "conceptTaught": "array-destructuring",
      "lessonType": "concept_introduction",
      "exerciseType": "predict_output"
    }
  ]
}
```

### Field Reference

| Field | Type | Description |
|-------|------|-------------|
| `version` | number | Schema version. Always `1` for v1. |
| `created` | string | ISO-8601 timestamp of first `/learn` invocation in this project. |
| `lastSession` | string | ISO-8601 timestamp of most recent `/learn` invocation. |
| `exerciseLevel` | number | Current exercise progression level (1-3). |
| `totalConceptsLearned` | number | Count of unique concepts in `conceptsSeen`. |
| `conceptsSeen` | array | All concepts the user has been taught. |
| `sessionLog` | array | Chronological log of every lesson delivered. |

### conceptsSeen Entry

| Field | Type | Description |
|-------|------|-------------|
| `name` | string | Kebab-case concept identifier (e.g. `"array-destructuring"`). |
| `language` | string | Programming language (e.g. `"javascript"`, `"python"`, `"rust"`). |
| `type` | string | Lesson type used most recently. One of: `concept_introduction`, `decision_explanation`, `hidden_trap`, `road_not_taken`, `connection`. |
| `firstSeen` | string | ISO-8601 timestamp of first encounter. |
| `lastSeen` | string | ISO-8601 timestamp of most recent encounter. |
| `timesEncountered` | number | How many times this concept has appeared in lessons. |
| `exerciseTypesUsed` | array | Which exercise types have been used for this concept. |

### sessionLog Entry

| Field | Type | Description |
|-------|------|-------------|
| `date` | string | ISO-8601 timestamp. |
| `conceptTaught` | string | Matches a `name` in `conceptsSeen`. |
| `lessonType` | string | One of the 5 lesson types. |
| `exerciseType` | string | One of the exercise types used. |

### Exercise Levels

| Level | Concepts Required | Exercise Types Available |
|-------|-------------------|------------------------|
| 1 | 0-9 | `predict_output`, `fill_in_blank`, `true_false` |
| 2 | 10-24 | Level 1 types + `spot_the_difference`, `what_would_you_change` |
| 3 | 25+ | Level 2 types + `prompt_problem`, `reorder` (future) |

### Initialization

When no `profile.json` exists, create it with:

```json
{
  "version": 1,
  "created": "<now>",
  "lastSession": "<now>",
  "exerciseLevel": 1,
  "totalConceptsLearned": 0,
  "conceptsSeen": [],
  "sessionLog": []
}
```

---

## decisions.json

A living architectural decision record (ADR) for the project. Written whenever a lesson covers a decision, trap, or road-not-taken.

### Schema

```json
{
  "version": 1,
  "decisions": [
    {
      "date": "2025-01-15T10:30:00Z",
      "concept": "State management approach",
      "choice": "React Context API",
      "alternatives": ["Redux", "Zustand", "Jotai"],
      "reasoning": "App has simple state with few consumers. Context avoids adding a dependency for a small amount of shared state.",
      "tradeoffs": "Simpler setup, but re-renders all consumers on any state change. Would need to switch if state grows complex or performance becomes an issue.",
      "codeLocation": "src/providers/AppContext.tsx:12"
    }
  ]
}
```

### Field Reference

| Field | Type | Description |
|-------|------|-------------|
| `version` | number | Schema version. Always `1`. |
| `decisions` | array | All architectural decisions logged. |

### decisions Entry

| Field | Type | Description |
|-------|------|-------------|
| `date` | string | ISO-8601 timestamp. |
| `concept` | string | Human-readable name for the decision. |
| `choice` | string | What was chosen. |
| `alternatives` | array | What was considered but not chosen. |
| `reasoning` | string | Why this choice was made. |
| `tradeoffs` | string | What was gained and what was given up. |
| `codeLocation` | string | File and line reference where this decision is most visible. |

### Initialization

When no `decisions.json` exists, create it with:

```json
{
  "version": 1,
  "decisions": []
}
```
