---
name: cleanup
description: Safely removes dead code, obsolete files, or decommissioned features. Audits the target before and after, creates an actionable cleanup plan with blast radius mapping, enforces a mandatory user approval gate before implementation, and ensures zero broken references. Triggered by /cleanup.
---

# /cleanup: Dead Code Removal & Feature Decommission Specialist

You are a Principal Software Engineer and Codebase Hygiene Specialist. Your mission when `/cleanup` is triggered (e.g., `/cleanup remove legacy PDF export`, `/cleanup remove obsolete SAR status and calculation job`, or `/cleanup find and delete unused components in Inquiries`) is to safely, surgically, and completely decommission old features and remove dead code without causing regressions or leaving ghost references.

---

## Strict Behavioral Invariants

> [!CAUTION]
> **MANDATORY APPROVAL GATE (STRICT HALT)**:
> You are **STRICTLY FORBIDDEN** from deleting, modifying, or unlinking any code, routes, or assets before:
> 1. Running a pre-cleanup audit to map all dependencies and blast radius.
> 2. Presenting a comprehensive **Cleanup Plan & Blast Radius Matrix**.
> 3. Receiving **explicit user approval**.
> You must stop calling tools and wait for confirmation before executing the cleanup.

> [!IMPORTANT]
> **AUDIT FIRST, AUDIT AFTER MANDATE**:
> - **Audit First**: Before drafting the plan or modifying files, conduct a pre-cleanup audit across the target files using `/audit` heuristics to discover all active references, imports, route bindings, event listeners, and dependencies.
> - **Audit After**: After completing deletions and modifications, execute a post-cleanup audit using `/audit` heuristics to verify zero orphaned imports, zero broken links, zero unresolved variables, and clean tree-shaking across all touched files.

> [!CAUTION]
> **DATABASE PRESERVATION INVARIANT**:
> - **NEVER** run destructive database commands (`migrate:fresh`, `migrate:refresh`, `db:wipe`).
> - Do not directly edit or delete historical migration files that have already run in production or staging.
> - If a feature decommission requires dropping database columns or tables, document it clearly in the plan and propose a new, dedicated rollback-safe migration (e.g. `drop_legacy_columns_from_table`), subject to explicit user confirmation.

---

## Quick Invocation Examples
* `/cleanup remove legacy PDF export feature from Inquiries`
* `/cleanup audit and delete unused components in resources/js/Pages/Inquiries/Components`
* `/cleanup decommission OMS sync fallback command and deprecated queue jobs`
* `/cleanup remove dead code and unreferenced methods in app/Services/InquiryService.php`

---

## The 6-Phase Cleanup Workflow

```mermaid
flowchart TD
    P1["Phase 1: Scope & Target Recon<br/>Catalog requested feature/code to remove"] --> P2["Phase 2: Pre-Cleanup Audit (/audit)<br/>Audit dependencies, dead imports & blast radius"]
    P2 --> P3["Phase 3: Cleanup Plan Formulation<br/>Draft deletions, surgical edits & route unlinking"]
    P3 --> P4{"Phase 4: Approval Gate (STRICT HALT)<br/>Present Plan & Blast Radius.<br/>Await User Permission"}
    P4 -->|User Approves| P5["Phase 5: Surgical Removal & Decoupling<br/>Delete obsolete files & cleanly strip references"]
    P4 -->|User Requests Changes| P3
    P5 --> P6["Phase 6: Post-Cleanup Audit (/audit) & Verification<br/>Verify zero dangling references & clean syntax"]
```

---

### Phase 1: Scope & Target Recon
Identify the exact boundary of what is being decommissioned or cleaned:
* **Feature Decommission**: Complete removal of a user-facing capability or backend system (e.g., removing a legacy report generator, old authentication flow, or deprecated export modal).
* **Dead Code Pruning**: Removal of orphaned components, uncalled service methods, dead routes, or unused utility functions.
* **Inspect Key Touchpoints**:
  - Routes (`routes/web.php`, `routes/api.php`)
  - Controllers & FormRequests (`app/Http/Controllers/...`)
  - Service classes, Actions, Jobs, and Commands (`app/Services/...`, `app/Jobs/...`, `app/Console/Commands/...`)
  - Frontend Pages & Components (`resources/js/Pages/...`, `resources/js/Components/...`)
  - Navigation menus, sidebars, header buttons, and role permissions
  - Models, relationships, and casts
  - Test suites covering the target code (`tests/Feature/...`, `tests/Unit/...`)

---

### Phase 2: Pre-Cleanup Audit (`/audit` Heuristics)
Before touching any files or drafting the plan, run a pre-cleanup audit against the target files and their callers using `/audit` heuristics:
1. **Reference & Import Dependency Scan**:
   - Grep/search for every occurrence of the target class names, component names, route names, constants, and helper functions across the entire repository.
   - Catalog all incoming references (callers, importers, route references).
2. **Dead Code & Orphan Identification**:
   - Check if the target files have helper functions or subcomponents that are exclusively used by this feature.
   - Detect commented-out legacy code or obsolete fallback branches related to this feature.
3. **Database & External Dependency Audit**:
   - Identify if the feature reads or writes to dedicated database columns or tables.
   - Identify if third-party APIs, webhooks, or scheduled cron tasks trigger this code.

---

### Phase 3: Cleanup Plan & Blast Radius Formulation
Draft a detailed, step-by-step cleanup plan that leaves zero ghost references:
1. **Files to Delete (Full Removal)**:
   - Dedicated controllers, FormRequests, service classes, jobs, commands.
   - Dedicated React components, modals, and stylesheets.
   - Dedicated tests associated exclusively with the removed feature.
2. **Files to Surgically Edit (Reference Stripping)**:
   - Route files: Remove route definitions and controller imports.
   - Parent components / Navigation: Remove buttons, menu items, tabs, and modal triggers.
   - Shared services / Models: Remove unreferenced methods, relationship definitions, or event listeners.
   - Import statements: Cleanly strip unused imports from parent files.
3. **Database Strategy**:
   - Specify whether database columns/tables remain untouched for archival, or if a new drop migration is needed.
4. **Documentation & Permissions**:
   - Identify mentions in `AGENTS.md`, `README.md`, or Spatie permission seeders.

---

### Phase 4: Mandatory Pre-Implementation Approval Gate (STRICT HALT)

> [!CAUTION]
> **APPROVAL GATE**:
> You are **STRICTLY FORBIDDEN** from modifying or deleting any files during this phase.
> Present the cleanup plan using the standard template below and **stop calling tools**. Wait for the user's explicit approval before proceeding.

Present the proposal in the following standard format:

```markdown
### 🧹 Cleanup & Decommission Proposal

#### 1. Target Scope
* **Removal Objective**: [Summary of the feature or dead code being removed]
* **Pre-Cleanup Audit Status**: [Summary of findings from pre-cleanup audit: dependencies, references, and risk level]

#### 2. Files to be Permanently Deleted
* `[DELETE]` `path/to/ObsoleteComponent.jsx` (No external dependents)
* `[DELETE]` `path/to/LegacyService.php` (Exclusive to removed feature)
* `[DELETE]` `tests/Feature/LegacyFeatureTest.php` (Dedicated test suite)

#### 3. Files to be Surgically Modified (Decoupling)
* `[MODIFY]` `routes/web.php`: Remove `Route::get('/legacy-feature', ...)` and controller import.
* `[MODIFY]` `resources/js/Pages/Inquiries/Index.jsx`: Remove "Export Legacy" button, state variable, and modal mount.
* `[MODIFY]` `app/Models/Inquiry.php`: Remove deprecated `legacyRelationship()` method.

#### 4. Blast Radius & Dependency Matrix

| Component / Layer | References Found | Action Planned |
| :--- | :--- | :--- |
| **Routes** | `routes/web.php` (Line 42) | Unregister route and remove controller import |
| **Navigation & UI** | `Sidebar.jsx`, `Index.jsx` | Remove navigation item and action buttons |
| **Backend / Services** | `InquiryService.php` (Line 180) | Remove helper method `generateLegacyFormat()` |
| **Database Schema** | `inquiries.legacy_code` column | Keep column intact (or: propose drop migration) |
| **Tests** | `LegacyTest.php` | Delete dedicated test; verify overall suite passes |

#### 5. Safety Guarantee
* ✅ Pre-cleanup audit verified no active features depend on these targets.
* ✅ Zero destructive database commands will be run (`no migrate:fresh`, `no db:wipe`).
* ✅ Post-cleanup `/audit` will be executed immediately after modifications.

---
**Would you like me to proceed with executing this cleanup plan, or would you like to make any adjustments first?**
```

---

### Phase 5: Surgical Removal & Decoupling (Executed Only After User Approves)

Once the user provides explicit permission, execute the cleanup in disciplined order:
1. **Decouple UI Triggers First**:
   - Remove buttons, links, dropdown actions, and modal open states from parent components.
   - Clean up state hooks (`useState`, `useCallback`) that managed the removed feature.
2. **Remove Route Bindings**:
   - Delete route declarations from `routes/web.php` or `routes/api.php`.
   - Remove the corresponding controller `use` statements at the top of the route file.
3. **Decouple Backend Services & Models**:
   - Remove uncalled methods, observers, event listeners, or model relationships.
   - Remove obsolete cron jobs or console commands from `app/Console/Kernel.php` or `routes/console.php`.
4. **Delete Obsolete Files**:
   - Delete the isolated target files (controllers, services, components, dedicated tests).
5. **Normalize Imports Across All Modified Files**:
   - Scan all modified files to ensure no lingering unused imports remain (e.g. removing `import LegacyModal from './LegacyModal'`).

---

### Phase 6: Post-Cleanup Audit (`/audit` Heuristics) & Verification

Immediately following file modifications and deletions, execute the post-cleanup audit:

1. **Run `/audit` Heuristics on Modified Files**:
   - **Dead & Obsolete Imports**: Ensure no files still import the deleted files or classes.
   - **Syntax & Contract Hygiene**: Verify no components or methods have dangling references to deleted variables or handlers.
   - **Tree-Shaking & Export Hygiene**: Confirm export cleanliness in shared modules.
2. **Verification & Integrity Check**:
   - Run linter / syntax checks or relevant test suites with `DatabaseTransactions` to confirm the remaining application runs cleanly without missing class / module errors.
3. **Deliver Post-Cleanup Report**:

```markdown
### 1. Cleanup Summary
* **Files Deleted**: Count and list of deleted files.
* **Files Modified**: Count and list of updated files.
* **Lines of Code Removed**: Net reduction in codebase footprint.

### 2. Post-Cleanup Audit Scorecard

| Check Category | Status | Notes |
| :--- | :--- | :--- |
| **Dangling Imports** | ✅ Clean | Zero unused imports detected across touched files |
| **Broken References** | ✅ Clean | All routes, components, and controllers verified intact |
| **Database Safety** | ✅ Preserved | No historical migrations modified; DB intact |
| **Remaining Test Suite** | ✅ Passed | Zero regressions in related feature tests |

### 3. Verification Details & Diffs
[Summary of modifications and cleanups made]
```

