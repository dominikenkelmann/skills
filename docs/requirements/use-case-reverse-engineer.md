## What it does

`use-case-reverse-engineer` reads existing source code — controllers, models, endpoints, CLI handlers — and reconstructs the formal Use Case that describes its actual runtime behaviour.

It never alters code. It traces control flow, database mutations, external calls, and error branches to surface the implicit contract hidden inside the implementation.

## When to reach for it

| Situation | What it does |
| --- | --- |
| Legacy refactoring | Extracts a ground-truth specification before tearing down legacy code. |
| Vibe-code cleanup | Recovers requirements from prototypes or rapid AI-generated code. |
| Missing documentation | Documents undocumented endpoints or background workers. |

Invoke it with `/use-case-reverse-engineer <path-to-code>`.

**Not** for refining or authoring a new specification (→ `use-case-expert`) or building code against an existing spec (→ `use-case-implementer`).

## Prerequisites

Readable source files or directories in the project.

It writes:
- `docs/usecases/UC-XXX <Verb Noun>.md` — draft extracted Use Case
- A list of unhandled edge cases or dead branches discovered during analysis

## How it extracts specifications

The skill maps code constructs directly to Cockburn Use Case elements:

| Code Construct | Extracted Element |
| --- | --- |
| Method signature / Route / HTTP method | **Trigger & Primary Actor** |
| Auth guards, input validators, null checks | **Preconditions & Step Validations** |
| Success execution path & DB commit | **Main Flow & Success Guarantees (`THAT`)** |
| `catch`, `try/except`, `if (err) return …` | **Extension Branches (`3a`, `4a`, …)** |
| Unhandled thrown exceptions | **Missing Extension Candidates** |

## What happens after

Pass the draft to **`use-case-expert`** to normalise vocabulary and add updated business rules, then hand off to **`use-case-implementer`** for a clean re-implementation or regression testing.
