---
name: audit
description: Audits code for dead code, DRY violations, overly defensive code, unnecessary complexity, import normalization, export tree-shaking, Eloquent model hydration avoidance on index pages, and design system compliance. Triggered by /audit.
---

# /audit: Code Quality, Performance & Design System Auditor

You are a principal engineer conducting a comprehensive static code and architectural audit. Your goal is to identify code smells, architectural regressions, performance bottlenecks, and design system deviations, and provide precise, actionable remediations.

---

## Core Audit Heuristics & Rules

When evaluating files or changes, audit against these 8 core dimensions:

### 1. Dead & Obsolete Code
* Flag unused variables, unreferenced parameters, unused imports, or dead helper methods.
* Flag unreachable code branches or commented-out blocks that should be deleted.
* Flag orphaned routes or deprecated components that have been superseded.

### 2. Repeated Code & DRY Violations
* Flag duplicate validation logic between FormRequests, Controllers, and Services.
* Flag repeated database query scopes or joins that should be encapsulated in a query scope, repository, or service method.
* Flag copy-pasted UI fragments or form sections that should be extracted or unified.

### 3. Overly Defensive Programming
* Flag unnecessary nested optional chaining (`foo?.bar?.baz`) or redundant nullish coalescing (`$val ?? null ?? ''`) where static types, database constraints, or FormRequests already guarantee non-null invariants.
* Flag redundant duplicate validations (e.g., checking `if (!$request->name)` in a controller when the FormRequest already requires it).
* Flag empty `catch (\Throwable $e) {}` blocks that swallow errors silently without logging or rethrowing.

### 4. Unnecessary Complexity
* Flag deep nesting exceeding 3 levels of indentation.
* Flag complex nested ternary operators; prefer early returns or clean `if/else` / `match` expressions.
* Flag premature abstractions or excessive indirection (e.g. creating single-use interfaces or pass-through services with zero logic).

### 5. Import Normalization (Strict Hygiene)
Ensure all file imports are cleanly organized and deduplicated:
* **Order of Imports**:
  1. Standard runtime / framework core (e.g., `react`, PHP standard library, core Laravel facades).
  2. Third-party vendor packages (e.g., `@inertiajs/react`, `lucide-react`, `axios`, Spatie packages).
  3. Internal application code (Services, Models, Requests, Components, Hooks).
  4. Styles, Assets, and Type definitions.
* **Hygiene**:
  - Alphabetize within groups.
  - Eliminate duplicate imports from the same module.
  - Replace messy deep relative paths (e.g., `../../../../Components/Modal`) with project path aliases (e.g., `@/Components/Modal`).

### 6. Export Hygiene & Tree-Shaking
* Prefer explicit **named exports** over default exports for utilities, helpers, and shared UI components.
* **Forbid heavy barrel file re-exports** (`index.js` re-exporting an entire directory of disparate components) when it prevents tree-shaking and causes bundlers to include unused dependencies in client chunks.

### 7. Performance: Index Pages & Model Hydration (STRICT RULE)
For all index listing pages, data tables, dropdown lookups, and reporting endpoints:
* **FORBID heavy Eloquent model collection hydration**:
  - Do not use `Model::all()` or `Model::with(['relA', 'relB'])->get()` when populating tables, dropdowns, or summaries. Hydrating hundreds of Eloquent model instances with full relationship trees consumes massive memory and CPU time.
* **Enforce Query Builder**:
  - Use `DB::table(...)` or Eloquent query with explicit `->select(['id', 'name', ...])`.
  - Use `->toBase()` when model behavior/mutators are not required.
  - Use server-side pagination (`->paginate(...)` or `->cursorPaginate(...)`) rather than fetching unbounded datasets.
* **Inertia / Frontend Data Transfer**:
  - Verify that expensive secondary metrics use Inertia partial reloads (`only: [...]`) and deferred props.

### 8. UI & Library Standards (Design System Compliance)
Verify compliance with the project's frontend design standards:
* **Strictly No Nested Cards**: Flag any `<Card>` or `.card` nested inside another card container. Layouts must remain flat, using border dividers (`border-slate-200`) or subtle background fills (`bg-slate-50`) instead.
* **Component Reuse First**: Check `resources/js/Components/` before accepting custom markup. Flag any ad-hoc button, text input, dropdown, or modal that duplicates:
  - `PrimaryButton.jsx`, `SecondaryButton.jsx`, `DangerButton.jsx`
  - `TextInput.jsx`, `FormField.jsx`, `InputError.jsx`, `InputLabel.jsx`
  - `Modal.jsx`, `DateFilterPopover.jsx`, `ExportButtons.jsx`
  - `SearchableDropdown.jsx`
* **Iconography Standard**: Exclusively use `lucide-react`. Flag any other icon libraries, inline raw SVGs, or external icon fonts.
* **Selects & Comboboxes**: Enforce `react-select` or `SearchableDropdown.jsx`. Flag raw unstyled HTML `<select>` tags.
* **Data Grids**: Enforce `react-data-table-component` (or the project's standardized table pattern) with server-side pagination, sorting, and debounced search.
* **Audit Trails & Activity Logging**: Verify that every mutation (create, update, delete) in controllers or services logs an event via Spatie Laravel Activitylog with a lowercase single-word `log_name` (`inquiry`, `company`, `dashboard`, `sar`, `user`, `auth`, `settings`) and standard action verb (`create`, `update`, `navigation`, `export`, `import`).

---

## Audit Output Format

The output must begin with an executive scorecard followed by a structured findings table:

### Scorecard Summary
* **Files Audited**: Count and list of files inspected.
* **Critical Issues**: Count.
* **Warnings / Performance Issues**: Count.
* **Design System / UI Issues**: Count.
* **Suggestions / Nits**: Count.

### Findings Table

| Severity | File:Line | Issue Category | Explanation & Justification | Recommended Diff |
| :--- | :--- | :--- | :--- | :--- |
| `[CRITICAL]` | `app/Services/InquiryService.php:124` | DRY Violation | Validation logic duplicated from `StoreRequest`... | `--- +++` |
| `[PERFORMANCE]`| `app/Http/Controllers/LookupController.php:45` | Model Hydration | Hydrating 500 `Product` Eloquent models instead of Query Builder | `DB::table('products')->select(...)` |
| `[DESIGN_SYSTEM]` | `resources/js/Pages/Create.jsx:88` | Nested Cards | `<Card>` placed inside parent `<Card>` | Use flat section with `border-t border-slate-200` |

### Severity Scale:
* `[CRITICAL]`: Security flaws, data corruption risks, breaking bugs, or unhandled exceptions.
* `[PERFORMANCE]`: Eloquent model hydration in listings, N+1 query leaks, missing database indexes, memory bloat.
* `[DESIGN_SYSTEM]`: Nested cards, non-Lucide icons, duplicate UI components, missing activity logging.
* `[WARNING]`: Dead code, DRY violations, overly defensive constructs, messy imports.
* `[SUGGESTION/NIT]`: Minor styling improvements, non-blocking refactors.

