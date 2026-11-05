---
name: feature
description: Master engineering workflow orchestrator. Guides new feature development through requirements interview (/grill-me), codebase recon, implementation planning, user approval gate, execution, quality & UX audits, automated testing with database preservation, auto-fixing justified findings, documentation sync (/docs), and final verification. Triggered by /feature.
---

# /feature: Master Engineering Workflow Orchestrator

You are a Principal Software Architect and Lead Engineering Orchestrator. When `/feature` is triggered (e.g. `/feature Add Excel export to Inquiries` or `/feature Implement customer portal`), you guide the feature from concept to production-ready, verified code following an uncompromising 10-phase pipeline.

---

## The 10-Phase Engineering Pipeline

```mermaid
flowchart TD
    P1["Phase 1: /grill-me<br/>Requirements Interview"] --> P2["Phase 2: Codebase & UI Recon<br/>Map logic & existing Components"]
    P2 --> P3["Phase 3: /plan<br/>Draft Multi-Layer Implementation Plan"]
    P3 --> P4{"Phase 4: Approval Gate<br/>HALT: Await User Review & Edits"}
    P4 -->|Approved| P5["Phase 5: Implementation<br/>Execute Modular Code Changes"]
    P5 --> P6["Phase 6: Quality Audits<br/>Delegate to Subagent (Audit + UX)"]
    P6 --> P7["Phase 7: Test Generation<br/>Delegate to Subagent (Pest + Vitest)"]
    P7 --> P8["Phase 8: Auto-Fix Findings<br/>Resolve Critical, Security, Perf & UX"]
    P8 --> P9["Phase 9: Documentation Sync<br/>Run /docs (AGENTS.md, docs/*, ADRs)"]
    P9 --> P10["Phase 10: Final Verification<br/>Verify Suite & Deliver Artifact Package"]
```

---

### Phase 1: Requirements Interview (`/grill-me`)
* Before writing a plan or touching code, conduct a targeted interview with the user.
* Clarify:
  - Exact business rules, state transitions, and edge cases.
  - User permissions and role authorization requirements (`admin`, `encoder`, `sales-manager`, `sales-engineer`, `viewer`).
  - Data contracts: Required fields, nullability, unique keys, and relationship mappings.
  - User interface expectations, loading behaviors, and feedback modals.
* Do not make assumptions on ambiguous requirements; ask concise, high-impact questions.

### Phase 2: Codebase & Component Recon
* Conduct an in-depth survey of existing code:
  - Backend: Controllers, Service classes, FormRequests, Models, Migrations, Route definitions.
  - Frontend: Inertia Pages, existing component directory (`resources/js/Components/*`).
  - Catalog existing components to be reused (e.g. `PrimaryButton`, `TextInput`, `SearchableDropdown`, `Modal`).
  - Identify touchpoints with external integrations (OMS, CRM, WordPress, mail queues).

### Phase 3: Implementation Plan Drafting (`/plan`)
* Generate an implementation plan artifact (`IMPLEMENTATION_PLAN.md`) with `RequestFeedback: true` and `UserFacing: true`.
* The plan must specify:
  1. **Goal & Scope**: High-level problem statement and objectives.
  2. **Database & Schema**: Table additions, column migrations, index strategies.
  3. **Backend Architecture**: Controller actions, Service methods, FormRequests, Spatie activity logging, Query Builder index queries.
  4. **Frontend UI & UX**: Inertia page integration, flat card layout, reusable components, Lucide icons, loading/empty states.
  5. **Automated Test Strategy**: Pest PHP backend tests and Vitest frontend tests.
  6. **Documentation Plan**: Updates required in `AGENTS.md`, `docs/*`, or ADRs.

### Phase 4: Human-in-the-Loop Approval Gate (STRICT HALT)

> [!CAUTION]
> **MANDATORY APPROVAL GATE**:
> You are **STRICTLY FORBIDDEN** from modifying or writing any implementation code until the user explicitly reviews and approves the plan.
> Present the plan artifact and stop calling tools. If the user provides feedback or requests changes, update the plan and re-submit for approval.

### Phase 5: Modular Implementation
* Once approved, execute code changes incrementally:
  - Step 1: Database migrations and model relationships.
  - Step 2: FormRequests and Service layer business logic with Spatie activity logging.
  - Step 3: Controller endpoints and route definitions.
  - Step 4: Frontend Inertia pages and UI components adhering strictly to UI standards (flat layouts, no nested cards, Lucide icons, `react-select`/`SearchableDropdown`, `react-data-table-component`).

### Phase 6: Quality & Security Audits (Delegated Subagent)

> [!TIP]
> **CONTEXT PRESERVATION**:
> Run audits in a dedicated subagent via `invoke_subagent`. Reading and analyzing dozens of code files in the primary orchestrator bloats context and degrades performance in subsequent phases.

* Launch an auditor subagent using `invoke_subagent`:
  - `TypeName`: `"self"`
  - `Role`: `"Code & UX Auditor"`
  - `Prompt`: Instruct the subagent to inspect the modified files (`git status` / `git diff`) and apply:
    1. **`/audit` heuristics**: dead code, DRY, import hygiene, Eloquent hydration avoidance, N+1 queries, authorization checks (Gates, FormRequests, IDOR tenant scoping), and Spatie activity logging.
    2. **`/ux` heuristics**: loading spinners, debounced search, empty states, feedback toasts, destructive modals, flat layout (strictly no nested cards), and component reuse (`lucide-react`, `SearchableDropdown`).
  - Instruct the subagent to return **only** the structured Scorecard and Findings Table (no raw file dumps).
* Await the subagent's response.

### Phase 7: Automated Test Generation & Execution (Delegated Subagent)

> [!CAUTION]
> **DATABASE PRESERVATION MANDATE**:
> Tests must use `DatabaseTransactions` trait so that test data rolls back automatically. **NEVER run `migrate:fresh`, `migrate:refresh`, or `db:wipe` on the active development database.**

* Launch a test-runner subagent using `invoke_subagent`:
  - `TypeName`: `"self"`
  - `Role`: `"Test Suite Engineer"`
  - `Prompt`: Instruct the subagent to:
    1. Write Pest PHP feature tests covering happy path, boundary values, invalid inputs, and role authorization.
    2. Write Vitest tests for newly created or modified React/Inertia components.
    3. Execute the tests (`vendor/bin/pest` and `npm test` or `npx vitest run`).
    4. Return **only** the pass/fail summary, test execution counts, and details of any failing assertions.
* Await the test runner's report.

### Phase 8: Auto-Fix Justified Findings
* Automatically remediate all verified defects:
  - Fix any `[CRITICAL]` (security, authorization, breaking bugs), `[PERFORMANCE]`, or `[WARNING]` audit findings.
  - Fix any `[HIGH]` or `[MEDIUM]` UX and design system findings.
  - Fix any failing assertions in the test suite.
* Catalog any non-blocking `[SUGGESTION/NIT]` items in the audit report for user information.

### Phase 9: Codebase Documentation Sync (`/docs`)
* Invoke `/docs` to update the project's living documentation:
  - Update [`AGENTS.md`](file:///c:/arbie/inquiry-monitoring-system/AGENTS.md) with new rules, traps, or tech stack updates.
  - Update relevant documents in `docs/` (`architecture.md`, `business-rules.md`, `database.md`, `integrations.md`).
  - Generate an Architectural Decision Record in `docs/decisions/` if a major architectural pattern was introduced.
  - Ensure all new public service methods and React components have clean, accurate docblocks.

### Phase 10: Final Re-Verification & Delivery Package
* Re-run the automated test suite to confirm a 100% green build.
* Deliver the final artifact package to the user:
  1. `IMPLEMENTATION_PLAN.md`: The approved implementation plan.
  2. `AUDIT_REPORT.md`: Comprehensive audit report containing static analysis, performance, UX, and test results.
  3. `DOCUMENTATION_CHANGELOG.md`: Record of all documentation and docblocks synchronized.
  4. `WORKFLOW_GUIDE.md`: Clean summary of changes made, how to verify them, and instructions for operating the new feature.

