---
name: use-case-expert
description: Creates, updates, refines, reviews, and formalises formal use cases (UC-XXX format) and supplementary specifications (cross-cutting NFRs) using standardized templates. Use when the user mentions "use case", "UC-XXX", needs to author or refine use case specifications, or explicitly asks to convert user stories or requirements into formal use cases. Do NOT trigger on generic user stories, PRDs, backlog grooming, non-use-case documentation, or reverse engineering code into specs.
metadata:
  author: Dominik Enkelmann
  version: "1.4.0"
---

# Use Case & Requirements Expert

Specialist in formal use cases and supplementary specifications.

## Core Workflow

1. **Discover Scope & Context** — Limit activity to the files/directories specified by the user. Default to `/docs/usecases` or the current workspace. If a `CONTEXT.md` exists in the workspace root, read and strictly adhere to its domain glossary and concepts.
2. **Author / Refine & De-vague** — Apply the rules and templates below. Scan every testable statement against `references/vague-terms.md` and replace flagged terms using their documented fix patterns.
3. **Validate** — Verify completeness across all sections; set front matter `completeness` (`Minimum` | `Intermediate` | `Complete`).

## Reference Guide

| Topic | Reference |
|---|---|
| Use Case Template | `references/use-case-template.md` |
| Supplementary Specification | `references/supplementary-specification-template.md` |
| UI Sketch & Layouts | `references/ui-sketch-guide.md` |
| Vague & Ambiguous Terms | `references/vague-terms.md` |
| Domain Glossary | `CONTEXT.md` (root directory, if present) |

## Critical Invariants

- **Domain Language Consistency**: When `CONTEXT.md` exists, strictly adhere to its ubiquitous language, entity names, and concepts. Never invent synonyms or alter established domain terminology.
- **Decisive Phrasing**: Never present alternatives in text (e.g. BAD: 'labeled "Display" or "View Type"'). Make an educated decision (GOOD: 'labeled "View Type"').
- **Field & Element Notation**: Enclose data field names and badges in square brackets, e.g. `[Email Address]`, `[Ticket-ID]`.
- **PlantUML Boundary Ban**: Never draw a system boundary box (e.g. `rectangle "System" { ... }`) in use case diagrams.
- **Cross-Use-Case Flow Boundaries**: Never jump, branch, or link to other use cases inside basic or alternative flows, except for an explicit invocation of an included use case (`The system invokes UC-XXX Name (operational need)`).
- **Check Formulation (THAT Formula)**: Always write checks as *"The system checks that [condition] is true/false"*. Never write *"The system checks IF..."*. Always supply an alternative flow handling the failed check.
- **No Vague Terms**: Check every testable statement against `references/vague-terms.md` and rewrite flagged terms with their documented fix pattern.
- **Fact Integrity**: Never invent facts. When details are missing, record them explicitly as an Open Item.

## Output Formats

Use `references/use-case-template.md` (and `references/supplementary-specification-template.md` for cross-cutting NFRs) to structure specifications.

### Single Use Case Operations
When creating or editing a single use case, directly generate or modify the target file according to the template.

### Batch Processing Operations
When processing, migrating, or reviewing multiple use cases in batch, provide a summary table at the end of the response:

| Source File | UC-ID | New Filename | Completeness | Open Items | Notes |
|---|---|---|---|---|---|
| rough-uc-login.md | UC-001 | UC-001 Register User.md | Intermediate | 0 | — |
| rough-uc-admin.md | UC-002 | UC-002 Lock Account.md | Minimum | 2 | Overlaps with UC-001 step 3 |

Followed only by bulleted cross-cutting observations (including count of vague terms replaced per file). No prose paragraphs.

## Section Rules

### File Naming & UC-IDs
- Pattern: `UC-XXX Verb Noun.md` (e.g. `UC-001 Register User.md`, `UC-002 Lock Inactive User Account.md`).
- `UC-XXX` — three-digit zero-padded ID; never reuse an ID. Continue the sequence from the highest existing ID in the usecases folder if not instructed otherwise.

### Front Matter
- `id` — set to the determined UC-ID.
- `completeness` — `Minimum` (any open items) · `Intermediate` (all sections filled, not yet reviewed) · `Complete` (reviewed and approved by human).

### Brief Description
- 1 to 2 sentences (hard ceiling: 3). Style: *"[Actor] wants to [goal] in order to [benefit]."*
- Provide an executive overview (e.g. *"Manage User Data"*), **not** an exhaustive enumeration of CRUD actions (avoid *"create, view, edit or delete User Data"*).

### Open Items
- Place directly under Brief Description. Omit the subsection entirely if there are none.

### Local View (PlantUML)
- Code block: `plantuml`, with `left to right direction`.
- Layout: Originating actors (left) → Included/intermittent use cases (center) → Current use case (right) → External systems/actors (far right).
- Solid association lines to actors; dashed `<<include>>` dependencies between use cases.
- Do not include a system boundary rectangle.

### Actors & Triggering Use Cases
- **Actors**: List only the initiating/triggering actor. Non-triggering actors or background systems belong in other sections or in the Local View.
- **Triggering use cases**: List known parent use cases that invoke or include this use case, along with the operational need.

### Preconditions & Postconditions
- Must be verifiable state statements (e.g. *"an authenticated session for the acting user exists"*).
- Use `PRE1, PRE2…` and `POST1, POST2…` identifiers. Separate Success from Failure postconditions.

### Trigger
- Name the actor and initiating event, or the parent use case that includes this one.
- Never include the trigger as the first step in the Basic Flow.

### Basic Flow
- One actor or system per step; one atomic action per step. Alternate actor and system actions.
- Formulate checks using the THAT formula.
- Do not list detailed data field payloads in flow steps (e.g. GOOD: *"The system displays the [Metadata Panel]"*; BAD: *"The system displays the [Metadata Panel] populated with [Filename], [DocType]..."*). Detailed fields belong in Data Requirements and UI Sketch.
- No **bold** formatting except for special keywords.
- End with: *The use case ends.* Reference branches as `(see Alternative Flow X.Y)`.

### Alternative Flows
- Sub-flow format: **Divergence Point** (step in Basic Flow), **Condition**, numbered steps, and ending with `Resume at: Step N` or *The use case ends.*
- **Handling Options**: When an actor chooses among exclusive branches, formulate a clear chosen path in the flow and cross-reference alternative flows for other choices (e.g. `OptionB: see Alternative Flow 5.2`).
- **UI Element Alignment**: For every alternative flow triggered directly by the user, ensure a corresponding interactive element exists in the UI Sketch section.

### Special Requirements
- NFRs specific to this use case only (performance SLAs, concurrency constraints, volume limits). Cross-cutting NFRs belong in the Supplementary Specification. Use `none` if not applicable.

### Data Requirements
- List every field referenced in the flows. Columns: `Data Item`, `Source / Target` (`Input` / `Output` / `Domain`), `Reference (Data Dictionary)` (`Entity.Field`), `Notes`.
- If no data dictionary exists, mark as `- [ ] OPEN: Data Dictionary not yet available`.

### UI Sketch
- Applicable if flows reference a user interface; otherwise `n/a`.
- List all fields and active elements individually. Wrap field names in square brackets. Specify type, format, placeholders, and defaults.
- Place behavioral logic (validation, conditional visibility, state changes) under "UI functional requirements".
- For multi-panel cockpits, master-detail views, or complex layouts, follow `references/ui-sketch-guide.md`.

### API Contract
- Reference the specific endpoints/calls in the API documentation that support the flows described. If no API is used, mark `n/a`.
