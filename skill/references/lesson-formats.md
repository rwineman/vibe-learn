# Lesson Formats & Types

This reference defines the exact output format for every lesson type. Follow these templates precisely.

---

## Two Lesson Formats

### Format A: Explain-First (for new concepts)

Use when the concept does NOT appear in the user's `conceptsSeen` profile.

```
CONCEPT: [Name]

What it is:
[1-2 sentence plain-language explanation. No jargon without inline definition.]

In your code:
[Reference the specific code just written. Include a simplified snippet (3-8 lines).]

The decision:
[Why this approach was chosen over alternatives. What was traded off.]

The trap:
[What could go wrong. When you'd outgrow this. What to watch for.]

Quick challenge:
[A micro-exercise that takes 10-15 seconds max.]

(Ask me to reveal the answer when you're ready.)
```

### Format B: Exercise-First (for previously seen concepts)

Use when the concept DOES appear in the user's `conceptsSeen` profile.

```
QUICK CHECK: [Concept Name]

You've seen [concept] before. Let's see if it stuck:

[Exercise: predict output, spot the bug, fill in the blank, etc.]

(Ask me to reveal the answer when you're ready.)

Going deeper:
[A new angle — a tradeoff, a connection, a trap they haven't seen yet.
Builds on what they already know. 2-3 sentences max.]
```

---

## Five Lesson Types

Each lesson type has a specific purpose and selection criteria. The lesson type determines the *content focus*, while the format (A or B) determines the *structure*.

### Type 1: Concept Introduction

**Purpose:** Teach a language feature, pattern, or tool the user likely hasn't seen before.

**When to use:** The conversation contains a concept that is foundational, widely applicable, and new to the user.

**Example (Format A):**

```
CONCEPT: Array Destructuring

What it is:
Destructuring lets you unpack values from an array into separate variables in a single line, instead of accessing each element by index.

In your code:
When processing the API response, I used destructuring to pull out the values:

  const [status, data, error] = await fetchResult();

Instead of writing status = fetchResult[0], data = fetchResult[1], etc.

The decision:
Destructuring is the idiomatic JavaScript approach here. It's more readable when the array positions have known meanings. The alternative — accessing by index — is safer when array length is uncertain, but here the API always returns exactly three values.

The trap:
This breaks silently if the array shape changes. If fetchResult() starts returning four values or reorders them, this code won't throw an error — it'll just assign the wrong values. When APIs evolve, named properties (returning an object instead) are more resilient.

Quick challenge:
What does `y` equal after this runs?

  const [x, , y] = [10, 20, 30];

(Ask me to reveal the answer when you're ready.)
```

### Type 2: Decision Explanation

**Purpose:** Explain why a specific approach was chosen when multiple valid options existed.

**When to use:** The conversation contains a moment where a meaningful architectural or design choice was made — library selection, pattern choice, data structure selection.

**Example (Format A):**

```
CONCEPT: Choosing Map Over Object for Dynamic Keys

What it is:
A Map is a key-value data structure like an object, but designed for cases where keys are added and removed dynamically. Unlike objects, Maps preserve insertion order and allow any type as a key (not just strings).

In your code:
For the user session cache, I used a Map instead of a plain object:

  const sessionCache = new Map();
  sessionCache.set(userId, sessionData);

The decision:
The conventional approach is a plain object ({}), and it would work here. I chose Map because your cache will have frequent additions and deletions of dynamic keys. Maps are optimized for this — they don't have prototype chain issues, .size gives you the count for free, and iteration is predictable. For a config object with known keys, a plain object is better.

The trap:
Maps aren't JSON-serializable. If you ever need to store this cache (localStorage, send over network), you'll need to convert it: [...map.entries()]. That friction is the cost of the performance benefit.

Quick challenge:
True or false: You can use a number as a key in a plain JavaScript object.

(Ask me to reveal the answer when you're ready.)
```

### Type 3: Hidden Trap

**Purpose:** Warn about a pragmatic choice that works now but has known limits.

**When to use:** The conversation contains code that uses a shortcut, simplification, or expedient approach (global variables, hardcoded values, localStorage, string concatenation for SQL, etc.).

**Example (Format A):**

```
CONCEPT: localStorage for User Preferences

What it is:
localStorage is a browser API that stores key-value pairs (strings only) that persist even after the browser tab closes. It's the simplest way to save data locally without a server.

In your code:
I stored the user's theme preference in localStorage:

  localStorage.setItem('theme', 'dark');
  const theme = localStorage.getItem('theme');

The decision:
For a single preference like theme, localStorage is the pragmatic choice. No server needed, no database, instant read/write. A database or cookie would be overengineered here.

The trap:
localStorage has a ~5MB limit per origin, is synchronous (blocks the main thread), and stores only strings — so objects need JSON.stringify/parse. The bigger trap: it's per-browser, not per-user. If someone logs in on a different browser, their preferences don't follow. Once you have user accounts and server sync, preferences should move to the database.

Quick challenge:
What does this return?

  localStorage.setItem('count', 42);
  typeof localStorage.getItem('count');

(Ask me to reveal the answer when you're ready.)
```

### Type 4: Road Not Taken

**Purpose:** Teach by showing an alternative that was considered but rejected, and why.

**When to use:** The conversation contains a decision point where the rejected alternative is itself educational — understanding *why not* deepens judgment.

**Example (Format A):**

```
CONCEPT: Why Not a Switch Statement Here

What it is:
A switch statement checks a value against multiple cases and runs the matching block. It's the classic alternative to a chain of if/else-if statements.

In your code:
For the command handler, I used an object lookup instead of a switch:

  const handlers = { save: handleSave, delete: handleDelete, edit: handleEdit };
  const action = handlers[command];
  if (action) action();

The decision:
I almost used a switch here — it's the textbook approach for dispatching on a string value. But object lookup is more concise, easier to extend (just add a key), and avoids the fall-through footgun that causes bugs in switch statements. The tradeoff: switch can handle ranges and complex conditions; object lookup only works for exact matches.

The trap:
Object lookup fails silently if the key doesn't exist — you get undefined. The if (action) guard handles this, but if you forget it, you'll get "action is not a function" at runtime. Switch with a default case makes the fallback explicit.

Quick challenge:
Fill in the blank to make this object lookup handle an unknown command by logging "unknown":

  const action = handlers[command] ?? ____;

(Ask me to reveal the answer when you're ready.)
```

### Type 5: Connection

**Purpose:** Link a current concept to something the user learned previously. Build systemic understanding.

**When to use:** The conversation contains a concept that directly relates to something in the user's `conceptsSeen` profile. Only available when the profile has history.

**Example (Format B — exercise-first, since they've seen the base concept):**

```
QUICK CHECK: Promises + Async/Await

You've seen Promises before. Let's see if it stuck:

What gets logged first?

  async function load() {
    console.log("A");
    const data = await fetch("/api");
    console.log("B");
  }
  load();
  console.log("C");

(Ask me to reveal the answer when you're ready.)

Going deeper:
Last time you saw Promises handling a single async operation. This time I used async/await — which is syntax sugar on top of Promises. The connection: every await is a hidden .then(). The code after await doesn't run until the Promise resolves, but the code *outside* the async function continues immediately. Understanding this is the difference between code that works and code that races.
```

---

## Micro-Exercise Types

### Level 1 (available from day one)

**predict_output** — "What does this print/return?"
- Show 2-4 lines of code
- Ask what a specific variable equals or what gets logged
- Must have a single, unambiguous answer

**fill_in_blank** — "Complete this line to make it work"
- Show code with one `____` blank
- The blank should test understanding of the concept just taught
- Must have a clear correct answer (minor syntax variations OK)

**true_false** — "True or false: [statement about the code]"
- Make a claim about code behavior
- Should require understanding, not just pattern matching
- Include a subtle misconception that the lesson content addresses

### Level 2 (unlocked at 10+ concepts)

**spot_the_difference** — "Which version is better and why?"
- Show two 3-5 line snippets side by side
- Both work, but one is better for a specific reason
- Tests judgment, not just correctness

**what_would_you_change** — "If [condition changed], what's the first thing you'd change?"
- Pose a scaling or requirements change scenario
- Tests whether the user understands the limits of the current approach
- Directly connects to "the trap" section of earlier lessons

---

## Output Constraints

- Total lesson output: 25-40 lines (readable in 30-60 seconds)
- Code snippets: 3-8 lines (simplified from actual code)
- No emojis unless the user's conversation style uses them
- Use the exact section headers shown in the templates (CONCEPT:, What it is:, etc.)
- Every lesson MUST include an exercise — no exceptions
- The answer is never shown inline — always prompt the user to ask for it
