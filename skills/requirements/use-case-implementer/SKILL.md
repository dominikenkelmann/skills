---
name: use-case-implementer
description: >
  Implements, updates, and verifies source code, domain logic, and tests directly from formal use case specifications (UC-XXX format) and supplementary specifications using clean, domain-driven vertical slices. Use when translating use cases in /docs/usecases/ into working code, scaffolding use-case interactors/handlers, or verifying that implementations cover all basic and alternative flows. Depends on use-case-expert for specification structure, completeness criteria, and domain conventions. Do NOT trigger for authoring or refining use case specifications (use use-case-expert), reverse-engineering code into use cases (use use-case-reverse-engineer), or generic coding tasks not driven by formal use case documents.
metadata:
  author: Dominik Enkelmann
  version: "1.1.0"
---

# Use Case Implementer

Translates formal Use Case Specifications and Supplementary Specifications into clean, well-tested, domain-driven code.

> [!IMPORTANT]
> **Dependency on `use-case-expert`**: This skill implements specifications authored according to `use-case-expert` standards. It relies on `use-case-expert` for specification templates, completeness lifecycle (`Minimum` | `Intermediate` | `Complete`), section conventions, and domain glossary alignment (`CONTEXT.md`).

---

## Core Principles

1. **Domain-Driven Vertical Slices**
   - Favor clean architecture / domain-driven slices (e.g., dedicated use case interactor or handler per `UC-XXX`).
   - Decouple use case core business logic from transport delivery (controllers, routes, CLI) and persistence mechanisms.

2. **Strict Traceability & Ubiquitous Language**
   - Keep naming of classes, types, methods, and variables strictly aligned with the terms used in the specification and `CONTEXT.md` (root domain glossary, if present).
   - Never invent synonyms or alter established domain terminology.

3. **Completeness Gatekeeping**
   - Implement specifications marked with `completeness: Intermediate` or `Complete`.
   - If `completeness: Minimum`, stop and advise the user to complete the specification first via `use-case-expert` (unless explicitly instructed to bypass).

4. **Flow & Step Fidelity**
   - The **Basic Flow** defines the baseline happy-path execution sequence and structure.
   - Every **Alternative Flow** must be explicitly covered with corresponding conditional logic, guard clauses, or error handlers.
   - When a use case includes other use cases (`The system invokes UC-XXX`), preserve separation in code: the calling use case delegates to the included use case interactor or service. Never duplicate or inline the included use case's logic.

5. **Pluggable Verification from Postconditions**
   - Map **Postconditions (`POST1, POST2...`)** directly to test assertions: success postconditions verify persisted state changes, events, and responses; failure postconditions verify rollback, error state, and error responses.
   - Maintain a modular testing structure that cleanly verifies every flow and facilitates easy integration with dedicated testing skills (e.g., E2E testing).

---

## Specification-to-Code Mapping Matrix

| Specification Section | Code Target (Clean Slice) | Verification Focus |
|---|---|---|
| **Preconditions (`PRE1...`)** | Route guards, auth checks, session guards, precondition assertions | Guard rejections & unauthorized access paths |
| **Basic Flow** | Use case interactor/handler happy path, step-by-step atomic actions | Happy-path end-to-end / unit flow execution |
| **Alternative Flows** | Branching conditions, domain error handlers, recovery logic | Negative test case per alternative flow condition |
| **Included Use Cases** | Invocations of included use-case interactors / services | Dependency call verification & mock integration |
| **Postconditions (`POST1...`)** | DB mutations, state changes, emitted domain events | Primary test assertions for state and output |
| **Data Requirements** | Domain entities, DTOs, schemas (e.g. Zod/Joi/TypeScript types) | Schema boundary and data validation tests |
| **UI Sketch & Requirements** | UI components, form inputs, action controls, dynamic visibility | Component rendering & interaction assertions |
| **Special Requirements** | Rate limiters, concurrency controls, timeout limits, encryption | Non-functional / integration checks |
| **API Contract** | Route declarations, HTTP verbs, response status codes, payloads | API contract & integration tests |

---

## Workflow

### 0. Dependency Check
Verify availability of the `use-case-expert` skill before performing any implementation work:
- Check for `use-case-expert` in available skills or at `skills/use-case-expert/SKILL.md`.
- **Conditional Block**: If `use-case-expert` is **not** available:
  - Halt execution immediately.
  - Inform the user:
    > `Execution Halted: The use-case-implementer skill depends on use-case-expert for specification structure, completeness criteria, and domain rules. Please provide use-case-expert, or explicitly confirm if you wish to bypass this check.`
  - Proceed only if the user explicitly instructs to bypass this check.
- Once verified, read `use-case-expert/SKILL.md` (and `CONTEXT.md` in the workspace root if present) via `view_file` to ensure all specification rules and ubiquitous language terms are active.

### 1. Scope Discovery & Completeness Gate
- Locate the target use case files (defaulting to `/docs/usecases/` or the paths specified by the user).
- Check the front matter `completeness`:
  - If `completeness: Minimum`:
    - **Halt** and notify the user that the specification is not ready for implementation. Direct them to refine it using `use-case-expert`.
    - Proceed only if the user explicitly confirms to implement despite open items.
- Identify referenced supplementary specifications (`/docs/usecases/supplementary-specifications.md` or similar) and included use cases.

### 2. Architecture & Slice Design
- Design a vertical slice following domain-driven / clean architecture principles:
  - Define input/output DTOs and domain models from **Data Requirements**.
  - Define the use case interactor/handler matching the **Basic Flow** and branching into **Alternative Flows**.
  - Define external ports/interfaces for persistence, messaging, or included use cases.

### 3. Implementation
- Implement the vertical slice components adhering to the **Specification-to-Code Mapping Matrix**.
- Keep naming identical to terms in the specification and `CONTEXT.md`.
- Preserve modular invocation boundaries for all included use cases.
- Implement UI components and API controllers exactly as outlined in the UI Sketch and API Contract.

### 4. Pluggable Verification & Testing
- Implement automated tests covering the full vertical slice:
  - Verify every step of the Basic Flow and all Alternative Flows.
  - Verify success postconditions on happy path and failure postconditions on alternative branches.
- Structure verification modularly so it easily connects with future dedicated E2E testing workflows.
- Run the test suite to ensure all tests pass with zero regressions.

### 5. Implementation Report
On completion, produce a summary table followed by cross-cutting observations:

| Use Case | Files Created / Modified | Tests | Flows Covered | Open Issues |
|---|---|---|---|---|
| UC-001 Register User | `auth/registerUser.ts`, `auth/registerUser.test.ts` | 3 | Basic + AF 3.1, 3.2 | — |
| UC-002 Lock Account | `auth/lockAccount.ts`, `auth/lockAccount.test.ts` | 2 | Basic + AF 2.1 | AF 2.2 skipped — requires webhook |

**Columns:**
- **Use Case** — UC-ID and name as in the specification.
- **Files Created / Modified** — relative paths only; one cell, comma-separated.
- **Tests** — count of test cases written (E2E + integration + unit combined).
- **Flows Covered** — Basic Flow always listed; Alternative Flows by reference label.
- **Open Issues** — anything blocked, ambiguous, or deliberately skipped, with a one-line reason.

Follow the table with bullet points for cross-cutting observations (shared utilities introduced, architectural decisions made, deviations from the specification).

Do **not** add prose summaries — table and bullets only.
