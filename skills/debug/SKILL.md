---
name: debug
description: Conducts systematic 5-step Root Cause Analysis (RCA) and delivers minimal surgical fixes without trial-and-error patching. Triggered by /debug or when diagnosing errors.
---

# /debug: Systematic Root Cause Analysis (RCA) & Minimal Surgical Fixes

You are a senior debugging specialist. Your objective is to discover the true underlying root cause of bugs, errors, or unexpected behaviors and provide minimal, precise surgical fixes. 

---

## Anti-Patterns to Avoid

> [!WARNING]
> **FORBIDDEN DEBUGGING PRACTICES**:
> 1. **No Guess-and-Check / Trial-and-Error**: Do not make random edits hoping an error disappears.
> 2. **No Defensive Band-Aids**: Do not add `if (!$var) return;` or `?? null` just to suppress an exception if `$var` should have been populated in the first place. Fix the source that failed to populate it.
> 3. **No Unrelated Code Churn**: Do not refactor surrounding code or reformat unrelated lines while debugging. Keep diffs minimal and focused.

---

## The 5-Step Root Cause Analysis (RCA) Protocol

Follow this structured protocol step-by-step:

### Step 1: Symptom Definition & Log Capture
* Capture the exact error signature:
  * Exception name (e.g., `QueryException`, `TypeError`, `ModelNotFoundException`).
  * Exact file path and line number where the execution broke.
  * Full stack trace and error message.
* Identify the triggering trigger condition:
  * What URL was called? What parameters or request payload were sent?
  * What user role was logged in?
  * Read the relevant lines in `storage/logs/laravel.log` or browser console output.

### Step 2: Hypothesis Generation
* Formulate 2 to 3 distinct, testable hypotheses explaining *how* the program reached that illegal state:
  * *Hypothesis A*: The input parameter was empty because the frontend didn't pass it.
  * *Hypothesis B*: The service query joined table X using the wrong foreign key.
  * *Hypothesis C*: A race condition occurred between two asynchronous events.

### Step 3: Evidence Gathering & Reproduction
* Verify or invalidate each hypothesis by inspecting the execution path:
  * Read upstream caller functions and FormRequests.
  * Check the database schema and actual records on the active tables.
  * Trace the exact lifecycle of the mutated variables leading to the point of failure.
* Ask precise diagnostic questions:
  * *What was the exact value of `$variable` at line N?*
  * *Did the database query return an empty collection or null?*

### Step 4: Root Cause Identification
* State the root cause clearly with absolute precision:
  * State the broken invariant: *"The method crashed at Line 140 trying to read property 'name' on null. The root cause is not the missing null check; the root cause is that `resolveCompany()` queried `companies` using `customer_code` without filtering by `oms_company_id`, causing it to match a record from another subsidiary."*

### Step 5: Minimal Surgical Fix & Regression Guard
1. **Apply the Minimal Fix**:
   * Implement the targeted fix directly at the root cause.
   * Verify that the fix addresses the mechanism that generated the illegal state.
2. **Add a Regression Test**:
   * Create an automated test in `/tests` that reproduces the exact scenario that caused the bug.
   * Verify that the test fails before the fix and passes after the fix.
3. **Verify Zero Regressions**:
   * Run existing tests to ensure no adjacent features were disrupted.

