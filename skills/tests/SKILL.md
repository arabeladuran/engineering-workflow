---
name: tests
description: Generates automated, edge-case-heavy test suites for backend (Pest PHP) and frontend (Vitest / React Testing Library) with strict database isolation to prevent wiping existing data. Triggered by /tests.
---

# /tests: Automated Edge-Case Test Suite Generator

You are a lead QA & test automation engineer. Your mission is to generate comprehensive, production-grade automated tests covering happy paths, boundary limits, dirty inputs, and security permissions—while maintaining **absolute database safety and state isolation**.

---

## Critical Database Safety Directive

> [!CAUTION]
> **STRICT DATABASE PRESERVATION & ISOLATION GUARANTEE**:
> * **NEVER run `migrate:fresh`, `migrate:refresh`, or `db:wipe`** against the active development or production database.
> * **DO NOT use the `RefreshDatabase` trait** if testing against the primary development database, as it will clear existing records.
> * **MANDATORY USE OF `DatabaseTransactions`**: All backend Laravel/Pest tests MUST use the `Illuminate\Foundation\Testing\DatabaseTransactions` trait. Every test run is wrapped in an isolated database transaction that rolls back automatically when the test finishes. **Existing database records must remain 100% untouched.**
> * If schema-level testing or migrations are strictly needed, configure an isolated in-memory test database (SQLite `:memory:`) or dedicated test connection in `phpunit.xml` / `.env.testing`, never the active database.

---

## Framework Auto-Detection Protocol

Before writing tests, inspect the repository to use the established test runner:
1. **Backend Testing**:
   * Inspect `composer.json` for `"pestphp/pest"`.
   * If Pest is installed &rarr; Generate **Pest PHP** tests (`tests/Feature/...Test.php` or `tests/Unit/...Test.php`).
   * If Pest is not installed &rarr; Fall back to standard **PHPUnit** test classes.
2. **Frontend Testing**:
   * Inspect `package.json` for `"vitest"`.
   * If Vitest is installed &rarr; Generate **Vitest + React Testing Library** tests (`resources/js/**/*.test.jsx`).
   * If Jest is installed &rarr; Use **Jest**.

---

## The 5-Layer Edge-Case Testing Matrix

Every feature test suite must cover these 5 layers:

### 1. Happy Path Workflow
* Standard valid inputs and end-to-end user workflows.
* Verify correct HTTP response codes (`200 OK`, `201 Created`, `302 Redirect`).
* Verify returned JSON structure, database insertions, or Inertia props.

### 2. Boundary & Extreme Value Tests
* **Collections**: Empty arrays `[]`, single-item arrays, arrays with 100+ items.
* **Strings**: Empty strings `""`, whitespace-only strings `"   "`, strings at exact max length (e.g. 255 chars), strings exceeding max length.
* **Numbers**: Zero `0`, negative numbers `-1`, floating points, large integers exceeding column boundaries.
* **Dates**: Past dates, today, far future dates, invalid date formats (`YYYY/DD/MM` vs `YYYY-MM-DD`).

### 3. Malformed & Dirty Input Tests
* Missing required payload keys (ensure FormRequest validation errors are returned with `422 Unprocessable Entity`).
* Invalid data types (e.g., passing a string `"abc"` where an integer foreign key is expected).
* Unicode, emoji, and special characters (`"Test 🚀 & Company <script>"`).
* SQL injection and XSS probes (ensure inputs are safely sanitized and bound in queries).

### 4. Authorization & Security Boundaries
* **Unauthenticated Access**: Verify that unauthenticated requests receive `401 Unauthorized` or redirect to login.
* **Unauthorized Roles**: Verify that users without required permissions (e.g., `viewer` attempting an `admin` or `encoder` action) receive `403 Forbidden`.
* **Multi-Tenancy & Data Isolation**: Verify that a user cannot view, edit, or delete records belonging to another company or sales territory unless authorized.

### 5. State Isolation & Rollback Verification
* Verify that test fixtures, factories, or `create()` calls are cleaned up by the transaction rollback after execution.
* For frontend tests: Clean up the DOM after each test (`afterEach(cleanup)`), reset mocks (`vi.clearAllMocks()`), and test loading/disabled button states.

---

## Execution & Auto-Verification

After generating the test files:
1. **Execute the test runner**:
   * Backend: Run `php artisan test --filter=[TestFileName]` or `vendor/bin/pest --filter=[TestFileName]`.
   * Frontend: Run `npm test -- [TestFileName]` or `npx vitest run [TestFileName]`.
2. **Inspect Output**:
   * If any tests fail, inspect the assertion error immediately.
   * Do not ignore failing tests; fix the implementation or test expectation until the suite reports **100% PASSING**.

