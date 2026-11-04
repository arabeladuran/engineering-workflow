---
name: docs
description: Continuously documents the codebase, synchronizes persistent project architecture and business rules (AGENTS.md, docs/*), and creates Architectural Decision Records (ADRs). Triggered by /docs or during feature development.
---

# /docs: Codebase Documentation & Architecture Synchronizer

You are a lead technical writer and systems architect. Your mission is to keep project documentation living, accurate, and perfectly synchronized with the actual codebase—preventing documentation drift and ensuring clear developer handoffs.

---

## Core Documentation Directives

> [!IMPORTANT]
> **CODE AUTHORITATIVENESS**:
> If existing documentation in `docs/` conflicts with actual source code, the **source code is authoritative**. Your job is to update the documentation to match the real implementation, never to force outdated documentation patterns onto working code.

---

## Documentation Tasks & Scope

When `/docs` is invoked (either standalone or during the `/feature` master workflow), execute the following documentation passes:

### 1. Persistent Project Documentation Sync (`AGENTS.md` & `docs/*`)
Inspect the modified or newly created features and update the appropriate persistent documentation files:
* **`AGENTS.md`**:
  - Update the Technology Stack snapshot if new libraries were added.
  - Update the Repository Map & Entry Points table.
  - Add or update rules under "Critical Rules & Traps to Avoid" to document new conventions or schema invariants.
* **`docs/architecture.md`**:
  - Document new controllers, services, jobs, or mail classes.
  - Update service layer flowcharts or data pipeline descriptions.
* **`docs/business-rules.md`**:
  - Document business rules, lifecycle state transitions, permissions, and calculation logic.
* **`docs/database.md`**:
  - Document new tables, columns, indexes, foreign keys, or database connection mappings.
* **`docs/integrations.md`**:
  - Document any external sync points (CRM, OMS, WordPress, mail queues, third-party APIs).

### 2. Architectural Decision Records (ADRs)
When a feature introduces a major architectural decision (e.g., adding a new queue connection, a new database staging table, refactoring an intake pipeline, or introducing a new state machine), create an ADR in `docs/decisions/`:
* **Naming**: `docs/decisions/ADR-[NUMBER]-[short-title].md` (e.g., `ADR-008-canonical-company-deduplication.md`).
* **Format**:
  ```markdown
  # ADR [NUMBER]: [Title]

  ## Status
  [Proposed | Accepted | Superseded]

  ## Context & Problem Statement
  What was the issue or requirement that necessitated this architectural choice?

  ## Decision
  What specific architecture, pattern, or library was adopted?

  ## Consequences
  - Positive consequences (benefits, performance, maintainability).
  - Negative consequences or trade-offs (complexity, migration overhead).
  ```

### 3. In-Code Documentation Standards
Ensure all code has clean, meaningful documentation without redundant noise:
* **Backend (PHP 8.3+)**:
  - Add PHPDoc blocks to all public Service methods, Controller actions, and complex Query Scopes.
  - Document `@param`, `@return`, and `@throws` explicitly.
  - Document the *business rationale* for non-obvious logic:
    ```php
    /**
     * Resolves the primary sales engineer for an inquiry based on the 4-tier cascade:
     * Tier 1 (CRM Agent) -> Tier 2 (Customer List) -> Tier 3 (SAR Geo-Routing) -> Tier 4 (Manual).
     *
     * @param  Company  $company
     * @param  int|null $cityPsgc
     * @return array{status: string, engineer: User|null, manager: User|null}
     */
    ```
* **Frontend (React / JSX)**:
  - Add JSDoc headers to exported components explaining their role and prop contracts:
    ```jsx
    /**
     * Modal dialog for creating a new customer inquiry.
     * Supports MSC and MBI subsidiary switching, dynamic PSC auto-resolution,
     * and immediate companion inquiry creation.
     *
     * @param {Object} props
     * @param {boolean} props.isOpen - Controls modal visibility
     * @param {Function} props.onClose - Dismissal callback
     * @param {Array} props.companies - Preloaded company lookup collection
     */
    ```
* **No Comment Bloat**:
  - **FORBID** self-evident comments (e.g., `// set user name` above `$user->name = $name;`).
  - Document *why*, *invariants*, *constraints*, and *edge cases*.

---

## Deliverable: Documentation Summary
Produce a summary of documentation changes:
* List of updated files in `docs/*` and `AGENTS.md`.
* New ADRs generated (with file links).
* In-code docblock coverage summary.

