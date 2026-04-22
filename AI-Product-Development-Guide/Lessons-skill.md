# Lessons.skill

---

## name: lessons
description: >
Persistent self-learning memory system for AI agents. Use this skill whenever
you are working in a project that has a tasks/lessons.md file, or whenever
the user mentions "lessons", "learnings", "agent memory", "feedback loop",
or "self-learning system". Also trigger when the user asks the agent to
remember mistakes, avoid repeating errors, or improve over time. After any
task where something unexpected happened or failed, always consult this skill
to write a structured lesson. Before starting complex tasks, always consult
this skill to load relevant past lessons.

# Lessons — Persistent Self-Learning System

This skill describes how an agent stores its own knowledge persistently, structures it,

and reuses it. The file `tasks/lessons.md` is the data store; this `SKILL.md` is the interface.

---

## When to use this skill

| Situation | Action |
| --- | --- |
| Before a complex task | Load relevant lessons (filter by tags) |
| After an error or an unexpected difficulty | Write a new entry |
| An existing learning is confirmed again | Increase confidence |
| A new learning contradicts an existing one | Mark a conflict |

---

## Entry format

Every entry **must** follow this schema — free-form text is not allowed:

```markdown
## [CATEGORY] Short problem title

- **Date:** YYYY-MM-DD
- **Context:** In which project / technology did the problem occur?
- **Wrong assumption:** What did the agent assume or do incorrectly?
- **Correct solution:** What is the correct approach?
- **Example:** (optional, code snippet if helpful)
- **Confidence:** 🔴 New | 🟡 Confirmed (2×) | 🟢 Reliable (3×+)
- **Scope:** `global` | `project:NAME`
- **Tags:** #technology #topic
```

Full category list and example entries: see `references/format.md`

---

## Rules for the agent

### Before a task

1. Check whether `tasks/lessons.md` exists.
2. Extract relevant tags from the current task.
3. Load only entries whose tags match the task — never load the entire file blindly.

### After a task

1. Was there an error, a wrong assumption, or an unexpected difficulty?
    - **No** → Do nothing.
    - **Yes** → Continue with step 2.
2. Does a similar entry already exist?
    - **No** → Write a new entry with confidence 🔴.
    - **Yes, confirmed** → Increase confidence (🔴 → 🟡 → 🟢).
    - **Yes, contradicts** → Mark a conflict (see below); do not overwrite.
3. Do **not** record trivial or one-off situations as a lesson.

### Contradiction detection

If a new learning contradicts an existing one, **never** overwrite silently:

```markdown
## ⚠️ CONFLICT – [CATEGORY] Title

New learning contradicts entry from YYYY-MM-DD.
Please resolve manually.

- **Existing:** ...
- **New:** ...
```

---

## Confidence levels

| Symbol | Meaning | Behavior |
| --- | --- | --- |
| 🔴 New | Observed once, not confirmed | Waits for human confirmation |
| 🟡 Confirmed | Confirmed independently twice | Used as a reference |
| 🟢 Reliable | Confirmed three times or more | High confidence; apply actively |

Entries with 🔴 are not treated as definitive truth until a human confirms them.

---

## Scope

| Scope | When | Example |
| --- | --- | --- |
| `global` | Applies to all projects | SVG in React Native needs a library |
| `project:NAME` | Only for a specific project | This client does not want TypeScript generics |

---

## [AGENTS.md](http://AGENTS.md) snippet

Add this block to the project’s `AGENTS.md`:

```markdown
### Lessons system

This project uses a persistent learning log at `tasks/lessons.md`.

**Before every complex task:**
- Load and consider relevant entries from `tasks/lessons.md` by filtering for tags.

**After every task with an error or unexpected difficulty:**
- Write a structured entry to `tasks/lessons.md` following the schema.
- Check existing entries for overlap and increase confidence if applicable.
- If there are contradictions: mark a conflict; do not overwrite.

Entry format and categories: see the `lessons` skill’s SKILL.md.
```

---

## Context efficiency

This file grows over time. Always filter by tags:

```
Current task: React Native navigation
→ Load: #react-native #navigation
→ Ignore: #typescript #api #tooling
```

For the complete format reference and example entries: `references/format.md`