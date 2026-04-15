# Rule: Writing an API_REFERENCE.md for a Language or Library

This rule defines the structure, formatting, and verification process for creating a comprehensive `API_REFERENCE.md` file that documents a programming language library or SDK.

---

## Prerequisites — What the User Must Provide

Before writing an API_REFERENCE.md, the following inputs are required:

### Required

| Input | Description |
|-------|-------------|
| **Official API documentation** | The library's API docs, SDK reference, or source-level doc comments. This is the source of truth for every function signature, parameter, return type, and behavior. |
| **Target language** | Which programming language the reference is for (e.g., TypeScript, Python, Go, Rust). Determines code block language tags, doc comment format, and idioms. |
| **Scope decision** | Which APIs, modules, or feature areas to cover. The tier system (below) helps prioritize, but the user decides what is in scope. |

### Recommended

| Input | Description |
|-------|-------------|
| **Existing sample code** | Tutorials, quickstart guides, example repos, or internal scripts that show real usage. These become the basis for the Common Patterns and Workflow Examples sections. |
| **Configuration reference** | Documentation for config files, environment variables, and CLI flags the library reads. |
| **Known issues or gotchas** | Common mistakes, breaking changes, or FAQ items. These feed the Troubleshooting and Best Practices sections. |

### Required for Verification Phase

| Input | Description |
|-------|-------------|
| **Runnable environment** | A machine where the library is installed and examples can be executed. |
| **Mock data or test targets** | Sample URLs, test credentials, API keys, mock servers, or data files needed to make examples runnable. |
| **Access to the tool under test** | If examples interact with a running application (e.g., a web app, API server), that application must be available during verification. |

---

## Document Structure

The API_REFERENCE.md follows a four-tier section hierarchy. Not every library needs every section, but the ordering must be preserved. Omit sections that do not apply.

### Tier 1 — Foundation (always include)

| # | Section | Purpose |
|---|---------|---------|
| 1 | Installation & Setup | Prerequisites, install commands, basic configuration file |
| 2 | Core Patterns | The fundamental usage pattern with a complete runnable example |
| 3 | Selectors / Identifiers / Addressing | How to target things (elements, resources, endpoints, objects) |
| 4 | Common Actions | The 10-20 most frequent operations, grouped by subcategory |

### Tier 2 — Intermediate (include when applicable)

| # | Section | Purpose |
|---|---------|---------|
| 5 | Waiting / Synchronization Strategies | How to wait for async operations, polling, timeouts |
| 6 | Assertions / Validation | How to verify expected state |
| 7 | Composition Patterns | Page Object Model, service layers, test fixtures, reusable abstractions |
| 8 | Network / IO / API Interaction | Intercepting, mocking, modifying external calls |
| 9 | Authentication & Session Management | Login flows, token management, state persistence |

### Tier 3 — Advanced (include when the library supports them)

| # | Section | Purpose |
|---|---------|---------|
| 10 | Visual / Snapshot Testing | Screenshot comparison, visual regression |
| 11 | Device / Environment Emulation | Mobile, different locales, permissions |
| 12 | Debugging | Inspector tools, logging, tracing |
| 13 | Performance Testing | Timing, profiling, benchmarks |
| 14 | Parallel Execution | Concurrency, worker pools, sharding |
| 15 | Data-Driven Patterns | Parameterized tests, fixtures, table-driven tests |
| 16 | Accessibility | A11y auditing, ARIA checking |
| 17 | CI/CD Integration | GitHub Actions, Docker, pipeline config |

### Tier 4 — Reference (always include)

| # | Section | Purpose |
|---|---------|---------|
| 18 | Best Practices | Numbered list of 5-10 rules with brief rationale |
| 19 | Common Patterns & Solutions | Cookbook of 5-10 specific problems with solutions |
| 20 | Troubleshooting | Common issues with causes and fixes |
| 21 | Quick Reference Commands | Cheat sheet of the most-used commands/invocations |

---

## Document Header

Every API_REFERENCE.md begins with:

```markdown
# <Library Name> - Complete API Reference

This document contains the comprehensive <Library Name> API reference and advanced patterns.

## Table of Contents

- [Installation & Setup](#installation--setup)
- [Core Patterns](#core-patterns)
- ...
```

The Table of Contents must link to every H2 section in the document.

---

## Code Example Format

### Preference Ordering

When multiple approaches exist for the same task, show them in this order with labels:

```<lang>
// PREFERRED: <Approach> (reason this is best)
<code>

// GOOD: <Alternative approach> (when to use this instead)
<code>

// OK: <Acceptable but limited approach>
<code>

// AVOID: <Anti-pattern> (reason this is wrong)
<code>  // <Inline explanation>
```

Always pair an anti-pattern with the correct approach. Never show an anti-pattern in isolation.

### Runnable Example Structure

Every code example in the Common Patterns sections must follow this structure:

```<lang>
// Imports / requires
<import statements>

// Parameterized configuration at top
const TARGET = '<placeholder>';  // <-- Comment explaining source

<main function or entry point> {
  <setup / initialization>

  try {
    <core logic>
    <output statement>  // Use console output to show results
  } catch (error) {
    <error output>
  } finally {
    <resource cleanup>
  }
}
```

Rules for examples:
- Every example must be syntactically complete and runnable as-is (or clearly marked as a fragment with `// Fragment:` prefix)
- Parameterize inputs as constants at the top of the example, never hardcoded inline
- Include error handling (`try-catch-finally` or language equivalent) in any example that performs IO
- Include resource cleanup in `finally` blocks (close connections, browsers, files)
- Keep each example under 25 lines; split longer examples into labeled subsections
- Use realistic but generic data (`example.com`, `user@example.com`, `"John Doe"`)
- Use inline comments to explain **why**, not **what**

### Console Output Convention

Use emoji prefixes in console output for machine-parseable results:

| Prefix | Meaning |
|--------|---------|
| `✅` | Success / passed |
| `❌` | Failure / error |
| `📸` | Screenshot / visual output saved |
| `🔍` | Detection / discovery |
| `📦` | Installation / setup |
| `📄` | File operation |

Example:
```javascript
console.log('✅ Login successful, redirected to dashboard');
console.error('❌ Error:', error.message);
console.log('📸 Screenshot saved to /tmp/screenshot.png');
```

---

## Section Content Format

### Foundation Sections (Tier 1)

**Installation & Setup** — Include:
- Prerequisites check command
- Install command
- Basic configuration file (complete, runnable)

**Core Patterns** — Include:
- Basic usage example showing the full lifecycle (setup → action → teardown)
- Test/script structure example showing the canonical file layout

**Selectors / Identifiers** — Include:
- Ranked list from most stable to least stable, each with a labeled code example
- Advanced patterns subsection (filtering, chaining, combining)

**Common Actions** — Group into subcategories. Each subcategory gets its own H3 heading:

```markdown
## Common Actions

### Form Interactions
<code block with 6-10 actions>

### Mouse Actions
<code block with 4-6 actions>

### Keyboard Actions
<code block with 4-6 actions>
```

### Intermediate Sections (Tier 2)

Each section must contain at least one complete code example. Structure each as:

```markdown
## <Section Title>

### <Subtopic>

```<lang>
<complete, runnable example>
```
```

### Advanced Sections (Tier 3)

These sections can be shorter (a single code block is acceptable) but must still demonstrate a working pattern:

```markdown
## <Section Title>

```<lang>
<working example>
```
```

### Reference Sections (Tier 4)

**Best Practices** — Numbered list, each item on one line with a brief rationale:
```markdown
1. **Rule name** - Brief rationale
2. **Rule name** - Brief rationale
```

**Common Patterns & Solutions** — Each pattern gets an H3 heading and a code block:
```markdown
### Handling Popups

```<lang>
<solution code>
```
```

**Troubleshooting** — Numbered list, each item describes the symptom, cause, and fix:
```markdown
1. **Symptom** - Cause. Fix: <action>
```

**Quick Reference Commands** — Single code block with the most-used invocations:
```markdown
```bash
# Task description
<command>
```
```

---

## Helper Function Documentation

When the API_REFERENCE.md documents helper/utility functions, use the language-appropriate doc comment format:

**JavaScript/TypeScript (JSDoc):**
```javascript
/**
 * One-line description.
 * @param {Type} name - Description
 * @param {Object} [options] - Description
 * @param {number} [options.timeout=30000] - Description with default
 * @returns {Promise<Type>} Description
 */
```

**Python (Google-style docstrings):**
```python
def function_name(param: Type, options: Optional[Dict] = None) -> ReturnType:
    """One-line description.

    Args:
        param: Description.
        options: Description.
            timeout: Timeout in seconds. Defaults to 30.

    Returns:
        Description.

    Raises:
        ValueError: When param is invalid.
    """
```

**Go (godoc):**
```go
// FunctionName does X.
//
// It accepts options via the Options struct. If timeout is zero,
// it defaults to 30 seconds.
func FunctionName(param string, opts ...Option) (Result, error) {
```

**Rust (rustdoc):**
```rust
/// One-line description.
///
/// # Arguments
/// * `param` - Description
///
/// # Returns
/// Description
///
/// # Errors
/// Returns `Error::Timeout` if the operation exceeds the timeout.
///
/// # Examples
/// ```
/// let result = function_name("value", Options::default())?;
/// ```
pub async fn function_name(param: &str, options: Options) -> Result<T> {
```

Requirements for all helper function documentation:
- Every exported function must have a doc comment
- All parameter types must be explicit
- Default values must be documented in the doc comment
- Async functions must note they return a future/promise

---

## Specification Documentation

### Environment Variables

Document as a markdown table:

```markdown
| Variable | Type | Default | Description |
|----------|------|---------|-------------|
| `LIB_TIMEOUT` | int (ms) | `30000` | Maximum wait time for operations |
| `LIB_HEADLESS` | bool | `false` | Run without visible UI |
| `LIB_HEADER_NAME` | string | (none) | Custom HTTP header name |
```

When multiple formats exist for the same configuration (e.g., single header vs JSON), show both:

```markdown
**Single value (common case):**
```bash
LIB_HEADER_NAME=X-Custom LIB_HEADER_VALUE=my-value
```

**Multiple values (JSON format):**
```bash
LIB_EXTRA_HEADERS='{"X-Custom":"value","X-Debug":"true"}'
```
```

### Configuration Files

Show complete, annotated configuration files:

```<lang>
// Every field gets an inline comment explaining its purpose
export default {
  field: 'value',      // What this controls
  timeout: 30000,      // Default: 30s. When to override.
};
```

### Timeout and Retry Defaults

If the library performs IO, document:
- Default timeout value and how to override it
- Default retry count (if applicable) and backoff strategy
- What happens on timeout (error, fallback, partial result)

---

## Example Verification Phase

After writing the API_REFERENCE.md, verify that every code example works by executing this process:

### Step 1: Build Test Specification Table

Extract every code example from the document and create a table:

| Test ID | Section | Example Description | Expected Outcome | Status |
|---------|---------|---------------------|------------------|--------|
| T-01 | Core Patterns | Basic browser automation | Launches browser, navigates, closes without error | pending |
| T-02 | Selectors | Data attribute selector | Locates element by data-testid | pending |
| ... | ... | ... | ... | ... |

Mark examples that are clearly fragments (e.g., config files, type definitions, CI/CD YAML) as `skip — not executable`.

### Step 2: Present Table to User

Show the test specification table to the user. The user may:
- Exclude specific tests
- Adjust expected outcomes
- Add additional test criteria

### Step 3: Collect Mock Data from User

Ask the user for the inputs required to make the examples runnable:
- Sample URLs or localhost endpoints to target
- Test credentials (username, password)
- Mock API responses or data files
- Any environment variables that need to be set
- Any services that need to be running

### Step 4: Execute Each Example Identically

Run the **exact code** from the API_REFERENCE.md — do not modify it. Use the user-provided mock data only as external inputs (environment variables, target URLs, files).

For each test:
1. Set up the required environment (env vars, mock server, test files)
2. Run the example code as-is
3. Capture the output (stdout, stderr, exit code)

### Step 5: Record Pass/Fail

Update the test specification table with results:

| Test ID | Section | Example Description | Expected Outcome | Status | Notes |
|---------|---------|---------------------|------------------|--------|-------|
| T-01 | Core Patterns | Basic browser automation | Launches, navigates, closes | **pass** | |
| T-02 | Selectors | Data attribute selector | Locates element | **fail** | `TypeError: Cannot read property 'click'` |

### Step 6: Diagnose and Propose Fixes

For each failed test:
1. Identify the root cause (typo, missing import, incorrect API usage, wrong default)
2. Propose a specific fix to the example code in API_REFERENCE.md
3. Show the exact `old_string → new_string` change

### Step 7: User Approves Fixes

Present all proposed fixes to the user before applying. The user may:
- Approve the fix
- Suggest a different fix
- Mark the test as acceptable (skip)

### Step 8: Apply Fixes and Re-run

After user approval:
1. Apply the approved fixes to API_REFERENCE.md
2. Re-run only the previously-failed tests
3. Update the test specification table

### Step 9: Repeat Until All Pass

Loop steps 5-8 until every non-skipped test shows **pass** in the specification table.

### Step 10: Final Report

Present the final test specification table showing all results. This serves as verification that every example in the API_REFERENCE.md works out of the box.
