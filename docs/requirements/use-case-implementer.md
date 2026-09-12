## What it does

`use-case-implementer` turns an approved Use Case (`UC-XXX`) into a fully tested vertical slice in code. It works in two strict phases: first the test suite (red), then the production code (green).

It refuses to write production code before generating automated tests that cover every Main Flow step and every declared Extension branch. Once tests exist, it implements the minimum code to turn them green — verifying that every postcondition invariant (`System confirms THAT [...]`) is satisfied.

## When to reach for it

| Situation | What it does |
| --- | --- |
| Building from spec | Takes a completed `UC-XXX` and builds the full vertical slice — domain, service, UI, tests. |
| Generating test harnesses | Creates comprehensive E2E or unit tests from a Use Case without touching production code. |
| Regression hardening | Re-runs against an updated Use Case with new extensions — generates failing tests first. |

Invoke it with `/use-case-implementer <path-to-use-case>`.

**Not** for authoring or refining the spec itself (→ `use-case-expert`) or extracting specs from legacy code (→ `use-case-reverse-engineer`).

## Prerequisites

An approved Use Case file (`docs/usecases/UC-XXX <Verb Noun>.md`) following the `use-case-expert` standard.

It writes:
- Automated tests (`tests/`, `*.spec.ts`, `*_test.py`)
- Application source files across the vertical slice (Domain, Application, Infrastructure, UI)

## How it executes

```
[Approved UC-XXX.md]
        │
        ▼
Phase 1: Generate Test Suite (Red)
├── Happy path test (Main Flow steps 1…N)
└── Branch tests (Extensions 3a, 3b, 4a…)
        │
        ▼
Phase 2: Implement Vertical Slice (Green)
├── Domain models & state transitions
├── Service layer & external adapters
└── UI elements & validation handlers
        │
        ▼
[All Tests Green · Postconditions Verified]
```

## What happens after

Run your code review or linter, then commit. The Use Case file stays in the repo as living documentation as long as the implemented code exists — every test traces back to a numbered step or extension.
