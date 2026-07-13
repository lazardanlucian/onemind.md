# ONEMIND-DIY — build a persistent project mind for any system

**An abstract specification for giving any project a memory, using whatever tools you have.**

This file describes the *concept* of a project mind, independent of any specific tool. For the git
implementation, see `ONEMIND.md`.

---

## What is a "mind"?

A mind is a **persistent record of reasoning and context** that lives alongside a project. It
captures *why* decisions were made, *what* was learned, and *what* the user cares about — things
that code commits and issue trackers don't typically preserve.

## What is a "thought"?

A thought is a single unit of recorded context — a decision, a discovery, a preference, a rejection,
or any other reasoning that a future session might need.

A thought has:
- **Content** — prose
- **A unique identifier** — so it can be referenced later
- **A timestamp** — when it was recorded
- **Optional metadata** — whatever structure the implementation chooses (tags, status, etc.)

## What a mind needs

A mind system must support at minimum:

| Operation | Description |
|---|---|
| **Write** | Record a thought. Should be append-only. |
| **Read** | Retrieve a thought by its identifier. |
| **Search** | Find thoughts by content or metadata. |
| **List** | Get recent or filtered thoughts, ordered by time. |

Pruning (removing old or low-value thoughts) is optional but useful for long-lived projects.

## Storage properties

The backing store should have these properties, in rough priority order:

1. **Portable** — the mind travels with the project
2. **Append-only** — old thoughts are not silently changed
3. **Searchable** — thoughts are findable by content, not just identifier
4. **Versioned** — history of the mind itself is recoverable
5. **Durable** — thoughts persist until explicitly removed

The specific implementation depends on what's available. Git repos, file directories, structured
files (JSON/YAML), or databases can all work.

## Getting started

### For agents implementing a mind

1. **Check if a mind already exists.** Look for a mind protocol file (e.g., `ONEMIND.md`,
   `.mind/`, or equivalent). If found, follow it.
2. **Detect the environment.** What version control is available? What tools are installed?
3. **Pick the simplest approach** that satisfies the storage properties above.
4. **Implement the four operations** (Write, Read, Search, List) for your chosen store.
5. **Seed the mind** with an initial thought so future searches have something to find.
6. **Link it** — add a note in the project's agent config (e.g., `AGENTS.md`) so future agents
   know the mind exists.

### For agents using an existing mind

1. **At session start:** read recent thoughts. Open with what's relevant.
2. **Before acting:** search the mind for prior context.
3. **During work:** record decisions, discoveries, and context as they happen.
4. **After tasks:** log surprising outcomes or changed approaches.

### What to record

Anything a future session (which might be you, next week) would benefit from knowing:
- Decisions and their reasons
- Rejections and why
- User preferences and constraints
- Non-obvious findings
- Context from the user (answers to your questions)

### What to skip

Routine progress, mechanical steps, and normal code changes — that's what commits and changelogs
are for. Never record tokens, secrets, credentials, or PII. The mind stores *reasoning*, not a
work log or a keychain.

## Examples

### Git

See [`ONEMIND.md`](ONEMIND.md) — uses git plumbing (commits on a hidden ref, index refs, notes, bundles).

### File-based

A `.mind/` directory with an index file and one file per thought:

```
.mind/
  index.json          # {id, timestamp, tags, summary} per entry
  thoughts/
    001.md
    002.md
```

### Structured file

A single `mind.json` or `mind.yaml` at project root, with an array of thought objects.

---

*This spec is tool-agnostic. For the git implementation, see `ONEMIND.md`.*
