# Rule: Writing an API_REFERENCE.md for a CLI Tool

This rule defines the structure, formatting, and verification process for creating a comprehensive `API_REFERENCE.md` file that documents a command-line interface tool.

---

## Prerequisites — What the User Must Provide

Before writing an API_REFERENCE.md, the following inputs are required:

### Required

| Input | Description |
|-------|-------------|
| **Command reference** | The tool's `--help` output, man page, or official command documentation. This is the source of truth for every command, flag, argument, and default value. |
| **Tool name and version** | The exact CLI binary name (e.g., `playwright-cli`, `kubectl`, `gh`) and the version being documented. |
| **Scope decision** | Which commands and features to cover. Some CLI tools have hundreds of commands — the user decides what is in scope. |

### Recommended

| Input | Description |
|-------|-------------|
| **Existing usage examples** | Tutorials, quickstart guides, README examples, or internal scripts that show real workflows. These become the basis for the Quick Start and Workflow Examples sections. |
| **Configuration reference** | Documentation for config files, environment variables, and global flags the tool reads. |
| **Known issues or gotchas** | Common mistakes, breaking changes, or FAQ items. These feed the Troubleshooting and Best Practices sections. |

### Required for Verification Phase

| Input | Description |
|-------|-------------|
| **Installed tool** | The CLI tool must be installed and runnable on the machine where verification happens. |
| **Mock data or test targets** | Sample URLs, test applications, form data, credentials, or files needed to make examples runnable. |
| **Target application** | If the CLI interacts with a running application (e.g., a web app, browser, API server), that application must be available during verification. |

---

## Document Structure

The API_REFERENCE.md for a CLI tool is organized around commands, not concepts. The document flows from quick start through full command reference, into topic deep-dives, and ends with reference material.

### Required Sections (in order)

| # | Section | Purpose |
|---|---------|---------|
| 1 | Installation & Setup | Prerequisites, install command, and fallback via package runner |
| 2 | Quick Start | The most common workflow in 4-8 commands |
| 3 | Commands | Every command grouped by functional category |
| 4 | Raw Output | Machine-readable output mode (if supported) |
| 5 | Global Options | Flags that apply across all commands |
| 6 | Core Concepts | Tool-specific concepts users must understand (2-4 concepts) |
| 7 | Workflow Examples | 3-6 real-world end-to-end workflows |
| 8 | Topic Deep-Dives | Detailed reference for complex features |
| 9 | Configuration | Config file schema and environment variables |
| 10 | Best Practices | Numbered list of 3-5 rules |
| 11 | Troubleshooting | Common issues with fixes |

---

## Document Header

```markdown
# <Tool Name> - Complete CLI Reference

This document contains the comprehensive command reference and advanced usage patterns for `<tool-name>`.

## Table of Contents

- [Installation & Setup](#installation--setup)
- [Quick Start](#quick-start)
- [Commands](#commands)
- ...
```

---

## Installation & Setup Section

Include prerequisites, install commands, and fallback via package runner. This section comes first so users can get the tool running before reading anything else.

```markdown
## Installation & Setup

### Prerequisites

```bash
# Check prerequisite version
<prerequisite> --version
```

### Install

```bash
# Primary install method
<package-manager> install <tool>

# Fallback via package runner (no install)
<package-runner> <tool>
```

### Verify Installation

```bash
# Confirm the tool is available
<tool> --version
```
```

Rules:
- Prerequisites check command must be included
- Show the primary install method first, then fallback via package runner
- Include a verification command so the user can confirm the install worked
- If the tool requires global installation, note that explicitly

---

## Quick Start Section

Show the most common workflow in a single code block. This is the highest-signal section — it must demonstrate the full lifecycle of the tool.

```markdown
## Quick Start

```bash
# <step 1: initialize/open>
<tool> <init-command>
# <step 2: navigate/configure>
<tool> <setup-command> <arg>
# <step 3: perform core actions>
<tool> <action-command> <arg>
<tool> <action-command> <arg>
# <step 4: capture/verify results>
<tool> <capture-command>
# <step 5: cleanup>
<tool> <close-command>
```
```

Rules:
- 4-8 commands total
- Include inline comments for every command explaining its purpose
- Commands must be runnable in sequence as-is

---

## Commands Section

Group all commands into functional categories. Each category gets an H3 heading and a single code block.

### Category Organization

Choose categories that match the tool's conceptual model. Common category patterns:

| Category Type | Example Categories |
|---------------|-------------------|
| Action-based | Core, Navigation, Input, Selection |
| Resource-based | Files, Sessions, Storage, Network |
| Lifecycle-based | Setup, Execute, Monitor, Cleanup |

### Command Listing Format

Within each category code block, list commands from simplest to most complex:

```bash
# simple form
<tool> <command>
# with required argument
<tool> <command> <arg>
# with optional argument
<tool> <command> <arg> "value"
# non-obvious behavior explained by comment
# --flag does X (only when behavior is not self-evident)
<tool> <command> <arg> "value" --flag
```

Rules:
- Use inline comments (`#`) only for non-obvious behavior. Self-explanatory commands get no comment.
- Show the simplest invocation of each command first, then variations with arguments, then variations with flags.
- Group related commands together (e.g., all cookie commands, all storage commands).
- One code block per category — do not split a category across multiple blocks.

### Argument Notation

Use consistent notation for arguments:

| Notation | Meaning |
|----------|---------|
| `<arg>` | Required argument |
| `[arg]` | Optional argument |
| `<arg1> <arg2>` | Multiple required arguments |
| `<key> <value>` | Key-value pair |
| `<ref>` | Element/resource reference |
| `<url>` | URL argument |
| `<file>` | File path argument |

Use this notation in the document header or any narrative text describing commands. Within code blocks, show actual example values instead of placeholders.

---

## Raw Output Section

If the tool supports a machine-readable output mode (e.g., `--raw`, `--json`, `--format`), document it with piping examples:

```markdown
## Raw Output

The `--raw` flag strips <what it strips> from output, returning only the result value.

```bash
<tool> --raw <command> | <pipe-target>
<tool> --raw <command> > file.json
VARIABLE=$(<tool> --raw <command>)
```
```

---

## Global Options Section

Document flags that apply across all or most commands:

```markdown
## Global Options

```bash
# <Category: connection/session>
<tool> <command> --option=value
<tool> -s=<session> <command>

# <Category: runtime/behavior>
<tool> <command> --headed
<tool> <command> --persistent

# <Category: configuration>
<tool> <command> --config=<file>
```
```

---

## Core Concepts Sections

Document 2-4 tool-specific concepts that users must understand to use the tool effectively. Each concept gets its own H2 section with 15-30 lines of content.

Example concepts: snapshots, sessions, element targeting, state management.

Structure each concept section as:

```markdown
## <Concept Name>

<1-2 sentence explanation of what this concept is.>

```bash
# Basic usage
<tool> <concept-command>
```

<Additional detail: output format, variations, options.>

```bash
# Variation 1
<tool> <concept-command> --option
# Variation 2
<tool> <concept-command> <arg>
```
```

---

## Workflow Examples Section

Include 3-6 examples that demonstrate real-world end-to-end workflows. Each example covers a different archetype:

1. **Happy path** — The most common workflow (e.g., form submission)
2. **Multi-resource** — Working with multiple instances (e.g., tabs, sessions)
3. **Debugging / Observability** — Using diagnostic features (e.g., console, tracing)
4. **Advanced** — Combining multiple features

### Example Format

```markdown
## Example: <Descriptive Workflow Name>

```bash
<tool> <setup>
<tool> <action-1>
<tool> <action-2>
<tool> <verify>
<tool> <cleanup>
```
```

Rules:
- Use descriptive workflow names, not "Example 1". Good: `Example: Form submission`. Bad: `Example 1`.
- 4-8 commands per example
- Include inline comments only where the purpose is non-obvious
- Group related commands without blank lines; separate logical phases with blank lines
- Each example must be runnable in sequence

---

## Topic Deep-Dives Section

For complex features that cannot be adequately covered in the Commands section, create detailed subsections. Link to them from a central index:

```markdown
## Topic Deep-Dives

* **<Topic 1>** — <One-line description>
* **<Topic 2>** — <One-line description>
* **<Topic 3>** — <One-line description>

### <Topic 1>

<Topic content following the format below.>
```

Alternatively, if the document is getting long (800+ lines), move deep-dives to separate files in a `references/` directory and link to them:

```markdown
## Specific Tasks

* **<Topic 1>** [references/<topic-1>.md](references/<topic-1>.md)
* **<Topic 2>** [references/<topic-2>.md](references/<topic-2>.md)
```

### Topic Deep-Dive Format

Each deep-dive (whether inline or in a separate file) follows this structure:

```markdown
### <Topic Title>

<One sentence describing purpose.>

#### Basic Usage

```bash
<2-5 commands showing the simplest way to use this feature>
```

#### <Subtopic A>

```bash
<commands or code for this subtopic>
```

#### <Subtopic B>

```bash
<commands or code for this subtopic>
```

#### Common Patterns

<2-3 real-world workflows combining the topic's commands.
Each pattern gets a descriptive heading and a code block.>

##### <Pattern Name>

```bash
<workflow commands>
```

#### Best Practices

1. **<Rule>** — <Brief rationale with code example>

#### Limitations

- <Constraint or known issue>
- <Constraint or known issue>
```

Rules for deep-dives:
- Every section must contain at least one code block
- Start with the simplest usage, then progress to advanced scenarios
- Self-contained: no dependencies on other deep-dive sections
- Comparison tables are encouraged when choosing between alternatives:

```markdown
| Feature | Option A | Option B | Option C |
|---------|----------|----------|----------|
| Format | .trace | .webm | .png |
| Best for | Debugging | Demos | Quick capture |
```

- Security notes go at the end if the topic involves credentials or sensitive data
- Maximum 300 lines per deep-dive (split further if larger)

---

## Flag and Option Documentation

### Flag Types

Document flags consistently:

```bash
# Boolean flag (presence = true)
<tool> <command> --headed
<tool> <command> --persistent

# Value flag (key=value, no space)
<tool> <command> --browser=chrome
<tool> <command> --filename=output.png

# Value flag with special characters (quoted)
<tool> <command> --body='{"mock": true}'

# Multi-value flag (comma-separated)
<tool> <command> --remove-header=cookie,authorization

# Short flag
<tool> -s=<name> <command>
```

Rules:
- Always show flags in context with a real command, never in isolation
- Use `--option=value` (equals sign, no space) for value flags
- Document flag defaults only in the Configuration section, not inline with commands

---

## Configuration Section

### Config File Schema

Show as a TypeScript type definition (even for non-TypeScript tools) inside a collapsible block:

```markdown
<details>
<summary>Configuration file schema</summary>

```typescript
{
  /**
   * Description of option.
   */
  optionName?: 'value1' | 'value2' | 'value3';

  /**
   * Description with default noted.
   * Defaults to 5000ms.
   */
  timeout?: number;

  /**
   * Nested configuration section.
   */
  section?: {
    setting?: boolean;
  };
}
```

</details>
```

Rules:
- Use TypeScript optional properties (`?:`) for non-required fields
- Use union types for enumerated values
- Use JSDoc comments for descriptions
- Include defaults in the JSDoc comment
- Nest related options under a parent object

### Environment Variables

Document in a collapsible block:

```markdown
<details>
<summary>Configuration via environment variables</summary>

| Environment |
|-------------|
| `TOOL_SESSION` default session name. |
| `TOOL_BROWSER` default browser (`chromium`, `firefox`, `webkit`). |
| `TOOL_TIMEOUT` action timeout in milliseconds. |
| `TOOL_HEADED` set to `true` to run in headed mode. |

</details>
```

Rules:
- Use a consistent prefix for all env vars: `TOOL_NAME_*`
- Description follows the variable name in the same table cell
- Group related env vars together
- Document format expectations: "comma-separated", "JSON object", "WxH pixels"

---

## Example Verification Phase

After writing the API_REFERENCE.md, verify that every command example works by executing this process:

### Step 1: Build Test Specification Table

Extract every code example from the document and create a table:

| Test ID | Section | Example Description | Commands | Expected Outcome | Status |
|---------|---------|---------------------|----------|------------------|--------|
| T-01 | Quick Start | Full lifecycle workflow | `open`, `goto`, `click`, `close` | All commands succeed, browser opens and closes | pending |
| T-02 | Commands > Core | Fill with --submit | `fill e5 "text" --submit` | Element filled and Enter pressed | pending |
| ... | ... | ... | ... | ... | ... |

Mark examples that are configuration schemas, type definitions, or narrative fragments as `skip — not executable`.

### Step 2: Present Table to User

Show the test specification table to the user. The user may:
- Exclude specific tests
- Adjust expected outcomes
- Add additional test criteria

### Step 3: Collect Mock Data from User

Ask the user for the inputs required to make the examples runnable:
- Target URLs or applications to test against
- Sample data for form fills
- Any services that need to be running
- Environment variable values
- Whether to use a specific browser or session configuration

### Step 4: Execute Each Example Identically

Run the **exact commands** from the API_REFERENCE.md — do not modify them. Substitute only:
- URLs (replace `example.com` with the user-provided target)
- Element refs (use actual refs from live snapshots)
- File paths (use actual paths on the test system)

For each test:
1. Set up the required environment (start the tool, navigate to the target)
2. Run the example commands in sequence
3. Capture the output (stdout, stderr, exit code)

### Step 5: Record Pass/Fail

Update the test specification table with results:

| Test ID | Section | Example Description | Expected Outcome | Status | Notes |
|---------|---------|---------------------|------------------|--------|-------|
| T-01 | Quick Start | Full lifecycle | All commands succeed | **pass** | |
| T-02 | Commands > Storage | Cookie set with flags | Cookie persisted | **fail** | `--httpOnly` flag not recognized |

### Step 6: Diagnose and Propose Fixes

For each failed test:
1. Identify the root cause (wrong flag name, incorrect syntax, missing prerequisite command)
2. Propose a specific fix to the example in API_REFERENCE.md
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
