---
name: use-case-reverse-engineer
description: >
  Reverse-engineers and extracts formal use case specifications (UC-XXX format) and supplementary specifications from existing codebases by analyzing routes, controllers, API endpoints, UI screens, and data models. Use when discovering, extracting, or documenting use cases from legacy or active codebases, converting endpoints/views into use case specifications, or mapping existing implementations to formal requirements. Requires and strictly delegates to the use-case-expert skill for specification templates and quality invariants. Do NOT trigger for authoring use cases from scratch without code (use use-case-expert), editing existing use case markdown files without code inspection (use use-case-expert), or implementing code from use cases (use use-case-implementer).
metadata:
  author: Dominik Enkelmann
  version: "1.1.0"
---

# Use Case Reverse Engineer

Discovers, extracts, and reverse-engineers formal Use Case Specifications and Supplementary Specifications from source code, UI components, and API definitions.

> [!IMPORTANT]
> **Strict Dependency on `use-case-expert`**: This skill specializes strictly in codebase discovery, boundary extraction, and code-to-use-case tracing. It depends entirely on the `use-case-expert` skill for templates, structural invariants, PlantUML diagrams, and de-vagueing checks.

---

## Core Principles

1. **Interface-Driven Discovery & CRUD Consolidation**
   Identify use case boundaries by analyzing external system interfaces:
   - **Menu Entries & Navigation**: Top-level navigation nodes or menus typically map to primary use cases.
   - **Screens & Wizards**: A user view, or a sequential multi-step wizard, maps to a single use case.
   - **API Endpoints & Cohesion**: Consolidate cohesive REST CRUD endpoints operating on the same resource (e.g. `GET`, `POST`, `PUT`, `DELETE` for `/api/v1/users`) into a single comprehensive use case (e.g., `UC-001 Manage Users`), using user choices and alternative flows to model individual actions. Model distinct non-CRUD business actions, complex operations, or asynchronous processing as standalone or included use cases (e.g., `UC-002 Process Batch Invoices`).

2. **User-Centric & Tier-Agnostic Perspective**
   Describe system behavior strictly from the user's perspective. Never write separate frontend vs. backend use cases; a single use case spans the full vertical slice.

3. **Code-to-Specification Mapping Matrix**
   When analyzing source code, map architectural artifacts to specification sections:

   | Source Code Artifact | Target Specification Section | Extraction Rule |
   |---|---|---|
   | Route handlers, controller happy path, orchestrator logic | **Basic Flow** | Map major handler operations into atomic alternating actor and system actions. |
   | HTTP 4xx/5xx responses, catch blocks, validation schemas (Zod/Joi/DTO) | **Alternative Flows** | Map each error condition, rejection, or guard failure to an alternative flow. |
   | Route guards, auth middleware, JWT validation, session checks | **Preconditions (`PRE1, PRE2...`)** | Identify required session states, authenticated actors, or existing records. |
   | DB transactions, ORM mutations, state updates, emitted events | **Postconditions (`POST1, POST2...`)** | Distinguish success state changes (persisted data, events) from failure state changes. |
   | Request/response DTOs, payload schemas, entity models | **Data Requirements** | Extract all data elements exchanged during the interaction. |
   | Templates, JSX/HTML, input controls, action buttons | **UI Sketch** | Identify active fields, buttons, and layout structures (set to `n/a` if headless/API only). |
   | Rate limiters, timeout configurations, batch volume limits, encryption | **Special Requirements** | Extract endpoint-specific non-functional requirements. |
   | Route URLs, HTTP verbs, path parameters, OpenAPI annotations | **API Contract** | Reference the supporting endpoints and HTTP methods. |

---

## Workflow

### 0. Hard-Blocking Dependency Check
Verify availability of the `use-case-expert` skill before performing any actions:
- Check for `use-case-expert` in available skills or at `skills/use-case-expert/SKILL.md`.
- **HARD BLOCK**: If `use-case-expert` is **not** available:
  - Halt execution immediately.
  - Do **not** scan the codebase, extract endpoints, or draft candidate use cases.
  - Inform the user:
    > `Execution Blocked: The use-case-reverse-engineer skill requires use-case-expert for specification templates, structural invariants, and quality rules. Please make use-case-expert available before proceeding.`
- Once verified, read `use-case-expert/SKILL.md` via `view_file` to ensure its structural invariants and templates are active.

### 1. Discovery & Interface Scan
Scan the target codebase to identify entry points:
- Locate routes, controllers, API declarations, and RPC handlers.
- Locate UI views, pages, navigation definitions, and wizard forms.
- Cluster related endpoints and screens into candidate use cases applying the CRUD consolidation principle.

### 2. Actor Mapping
Determine interacting actors from codebase authorization logic:
- **Primary Actors**: Identify who initiates actions from role decorators, permission checks, and route protection (e.g. `Customer`, `Administrator`, `Authenticated User`).
- **Secondary / System Actors**: Identify external services or automated systems from outbound API clients, webhooks, or scheduled job handlers.

### 3. User Validation Checkpoint
Present the candidate use case inventory to the user for confirmation before writing detailed specifications:

| Candidate UC | Goal | Interface / Endpoints | Scope / Type | Consolidated Code Endpoints |
|---|---|---|---|---|
| UC-001 Manage Users | Administer user accounts | `/api/v1/users`, `/users` UI | Primary | `GET/POST/PUT/DELETE /api/v1/users` |
| UC-002 Process Payroll | Execute monthly salary distribution | `POST /api/v1/payroll/process` | Primary | `POST /api/v1/payroll/process` |
| UC-003 Transfer Funds | Send funds via external banking API | Included by UC-002 | Included | `POST /integrations/banking/transfer` |

*Wait for user confirmation on the candidate list, use case boundaries, and naming before proceeding.*

### 4. Vertical Code Analysis (UI → Service → Data)
For each confirmed use case:
- Trace execution flow from route/controller entry through service layers down to persistence and third-party integrations.
- Apply the **Code-to-Specification Mapping Matrix** to classify every code construct into its corresponding use case section.

### 5. Inline Specification Drafting via `use-case-expert`
Execute specification drafting inline by following `use-case-expert`:
- Generate specification files in `/docs/usecases/` using the naming standard `UC-XXX Verb Noun.md`.
- Apply `use-case-expert` templates (`references/use-case-template.md` and `references/supplementary-specification-template.md` if cross-cutting NFRs are found).
- Adhere strictly to all `use-case-expert` rules: THAT formula for checks, atomic alternating steps, PlantUML local view conventions, and `references/vague-terms.md` de-vagueing checks.
- Output the final batch summary table and observations as defined in `use-case-expert`.
