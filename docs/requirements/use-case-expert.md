## What it does

`use-case-expert` is a requirements engineer. It takes a raw feature idea, a user story, or a vague PRD and turns it into a formal Cockburn/RUP-style Use Case specification.

It never writes application code. Its job is to eliminate ambiguity *before* anyone touches the codebase — by probing for unstated boundaries, enforcing active-voice alternating steps, extracting edge cases into extension branches, and locking system guarantees with the `System confirms THAT [...]` formula.

## When to reach for it

| Situation | What it does |
| --- | --- |
| New feature or idea | Turns a loose user story or PRD into a formal, build-ready `UC-XXX` specification. |
| Auditing existing specs | Reviews a draft Use Case for vague terminology, missing error paths, or untestable postconditions. |
| Post-incident hardening | Converts an unhandled production failure into a documented Extension branch. |

Invoke it with `/use-case-expert` or by prompting with your feature request.

**Not** for implementing an approved spec into code (→ `use-case-implementer`) or extracting specs from legacy code (→ `use-case-reverse-engineer`).

## Prerequisites

None.

It writes to:
- `docs/usecases/UC-XXX <Verb Noun>.md` — the formal Use Case
- `docs/usecases/supplementary-specification.md` — cross-cutting NFRs, UI constraints, and business rules

## The core standard

Every Use Case follows four structural rules:

1. **Preconditions** — verifiable system state before the flow begins.
2. **Alternating steps** — strict back-and-forth between Primary Actor and System. No step without a counterpart.
3. **Exhaustive extensions** — every validation, every branch condition gets a numbered extension (`3a`, `3b`).
4. **The THAT formula** — postconditions assert concrete state mutations:
   - ❌ *"The user is updated."*
   - ✅ *"System confirms THAT `User.email` is persisted AND confirmation token is dispatched."*

## What happens after

Once reviewed and approved, hand the Use Case to **`use-case-implementer`** — it generates the TDD test harness and builds the vertical slice against the spec.
