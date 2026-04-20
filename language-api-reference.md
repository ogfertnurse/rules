# Rule: Writing a Skill-Ready API Reference for a Language or Library

This rule defines the structure, file layout, and formatting for creating an `API_REFERENCE.md` that is designed to be consumed by a future agent skill **and** remain usable as a standalone reference.

The output is not a single file — it is a dedicated directory containing a main `API_REFERENCE.md` (the "skill body") plus a `references/` subdirectory holding on-demand topic files. A future `SKILL.md` (or equivalent agent-facing manifest) can inline the main file's content with a few additions (frontmatter, activation text) and lazy-load reference files when specific tasks require them.

This rule is language- and domain-agnostic. It applies equally to a Python MVS-files API, a TypeScript UI framework, a Go gRPC client, a Rust parser library, a SQL query-builder, or a Bash scripting toolkit. Where the rule prescribes a shape, the shape is a *template* to adapt — section titles, example conventions, and verification steps vary with the target language and domain.

---

## Prerequisites — What the User Must Provide

### Required

| Input | Description |
|-------|-------------|
| **Official API documentation** | The library's API docs, SDK reference, or source-level doc comments. Source of truth for every function/method/endpoint signature, parameter, return type, and behavior. |
| **Target language** | The programming language or execution environment (Python, TypeScript, Go, Rust, SQL dialect, Bash, …). Determines code-fence language tags, import/require syntax, docstring style, and idiomatic conventions. |
| **Domain / scope decision** | Which APIs, modules, or feature areas to cover. The domain name drives the directory name and document title. |

### Recommended

| Input | Description |
|-------|-------------|
| **Existing sample code** | Tutorials, quickstarts, example repos, internal scripts. These seed the Common Patterns and cookbook sections. |
| **Configuration reference** | Config files, environment variables, flags or initialization options the library reads. |
| **Known gotchas** | Common mistakes, breaking changes, FAQ items. These feed Troubleshooting and Best Practices. |

### Optional (only if the user opts into live verification)

| Input | Description |
|-------|-------------|
| **Runnable environment** | A machine where the library is installed and examples can be executed. |
| **Mock data / test targets** | URLs, credentials, API keys, mock servers, or files needed to make examples runnable. |

---

## Output Shape

The reference is a **directory** — not a single file — so it can serve a future skill directly.

### Directory Name

`<language>-<domain>-api-reference/` at the repo root. The pattern works for any language + domain combination:

| Language | Domain | Directory |
|----------|--------|-----------|
| python | zoau mvs files | `python-mvs-files-api-reference/` |
| python | zoau jobs | `python-zoau-jobs-api-reference/` |
| typescript | react hooks | `typescript-react-hooks-api-reference/` |
| go | grpc client | `go-grpc-client-api-reference/` |
| rust | parser combinators | `rust-parser-combinators-api-reference/` |
| sql | bigquery analytics | `sql-bigquery-analytics-api-reference/` |
| bash | gnu coreutils | `bash-gnu-coreutils-api-reference/` |

Pick the domain name **after** surveying scope. If the surface covers more than the obvious primary resource (e.g., sequential datasets, VSAM, and GDGs together), use a broader umbrella term (see [Domain-Aware Naming Checklist](#domain-aware-naming-checklist)).

### Directory Contents

```
<language>-<domain>-api-reference/
├── API_REFERENCE.md          # Tier 1 + Tier 4 — the "skill body"
└── references/
    ├── <topic-1>.md          # Tier 2 topic (intermediate mechanics)
    ├── <topic-2>.md          # Tier 2 topic
    ├── <topic-3>.md          # Tier 3 topic (domain surface)
    └── <topic-4>.md          # Tier 3 topic
```

No other files. No `index.md`, no `SUMMARY.md` — the main file already links to every reference.

---

## Tier System

Content is divided into four tiers. **Tier 1 and Tier 4 live in `API_REFERENCE.md`. Tier 2 and Tier 3 live in `references/`, one file per topic.**

| Tier | Destination | Purpose |
|------|-------------|---------|
| **Tier 1 — Foundation** | `API_REFERENCE.md` | Everything a reader needs to write their first working program |
| **Tier 2 — Intermediate Mechanics** | `references/` | Cross-cutting concerns most callers eventually need (validation, composition, streaming, auth, error handling) |
| **Tier 3 — Domain Surfaces** | `references/` | Specific subsystems the library exposes. Distinguished from Tier 2 by being *scope-specific*, not *depth-specific* |
| **Tier 4 — Reference & Guidance** | `API_REFERENCE.md` | Cheat-sheet material — rules, recipes, symptoms, quick calls |

### Rule of Thumb for Topic Placement

- **Keep in Tier 1** if skipping the topic would break a typical ten-minute workflow with the library.
- **Spill to `references/`** if the topic is load-on-demand — a reader touches it only when their specific task needs it.
- **Never put Tier 3 topics in `API_REFERENCE.md`** even when short. Domain surfaces stay in their own file so a skill can load them atomically.

### Examples of Tier 3 Topics Across Domains

| Library type | Likely Tier 3 topics (one file each) |
|--------------|---------------------------------------|
| z/OS Python | GDGs, VSAM clusters, archiving, exceptions |
| React framework | Routing, SSR, devtools, accessibility |
| gRPC client | Interceptors, streaming, retry policies, deadlines |
| SQL dialect | Window functions, window-frame specs, JSON functions, geospatial |
| Bash toolkit | Signal handling, process substitution, named pipes, traps |

Each maps to one file in `references/`. A skill loads only the files relevant to the current task.

---

## `API_REFERENCE.md` — Main File Layout

Sections must appear in this order. Omit sections that do not apply. Rename the heading of any section to match domain terminology (see [Section Title Adaptation](#section-title-adaptation)).

### Section 1 — Installation & Setup

**Slim.** One smoke-test snippet in the target language that, when it runs cleanly, proves the library is ready (typical forms: version-attribute print, a trivial no-op API call, or a connection handshake). Follow with 2–3 sentences describing common setup failure modes and their fixes.

Do **not** reproduce the library's full installation procedure. If a full install guide is needed, put it in `references/installation.md`.

### Section 2 — Core Patterns

The canonical full-lifecycle example (setup → action → teardown, where applicable) plus the canonical program/script layout (imports/requires → parameters → operations → cleanup). The reader should be able to clone and adapt this pattern for 80% of tasks.

### Section 3 — Identifiers & Addressing *(rename if the domain calls for it)*

How the library names or locates the resources it operates on. Ranked most-stable → least-stable with labeled alternatives. Rename freely:

| Library type | Better section title |
|--------------|----------------------|
| Data-store / filesystem | "Identifiers & Addressing" or "Resource Naming" |
| HTTP / REST client | "URLs & Endpoints" |
| UI framework | "Component & Prop Naming" |
| Query builder | "Table & Column References" |
| Process / OS API | "Process & Signal Handles" |

Omit the section entirely if the library has no concept of resource identity (e.g., a pure math library).

### Section 4 — Common Actions

The 10–20 most-used operations. Group by subcategory, one H3 per subcategory. Every exported symbol from the main module / namespace / package should have a snippet here (or a short entry stating it exists and pointing to a reference file).

This section may grow past the 300-line guideline in Tier 1 alone, and **that is acceptable for this one section** because it is the reader's primary landing zone.

### Section 5 — Topic Deep-Dives

A bulleted link list to every file in `references/`. Group by tier:

```markdown
## Topic Deep-Dives

### Intermediate — Mechanics

- **[Topic Title](references/topic-slug.md)** — one-line description.
- **[Topic Title](references/topic-slug.md)** — one-line description.

### Advanced — Domain Surfaces

- **[Topic Title](references/topic-slug.md)** — one-line description.
- **[Topic Title](references/topic-slug.md)** — one-line description.
```

This section is the seam a future skill uses to decide which reference file to load for a given task. Keep descriptions action-oriented so the skill can match on user intent.

### Section 6 — Best Practices

Numbered list of 6–10 rules. Each rule fits on one line and states the rationale. When a rule points at deeper material, link to the relevant file in `references/`.

### Section 7 — Common Patterns & Solutions

Cookbook of 5–10 task-oriented recipes. Each recipe has an H3 descriptive heading and one code block. Recipes should combine multiple APIs to show real workflows (e.g., "Retry with exponential backoff", "Stream a large paginated result set", "Rotate a cached credential"), not just isolated function calls.

### Section 8 — Troubleshooting

Numbered list. Each item has the shape: **Symptom** — Cause. **Fix:** action. Cover the most common 5–10 failure modes.

### Section 9 — Quick Reference

Single code block with the 10–15 most-used calls. Annotated with one-line comments and section cross-references for anything in `references/`.

### Document Header

```markdown
# <Domain> - Complete API Reference

This document contains the comprehensive <library> API reference and advanced patterns for <scope statement>.

## Table of Contents
...
```

The scope statement should enumerate the resources covered so the reader can confirm the reference covers their task before reading further.

---

## `references/<topic>.md` — Per-Topic File Layout

Every reference file is self-contained — it does not assume the reader has the main file open, and it does not link back into source documentation. Structure:

```markdown
# <Topic Title>

> One-sentence purpose statement. For the core API workflow, see [API_REFERENCE.md](../API_REFERENCE.md).

## <Subtopic 1>

<explanation + code block>

## <Subtopic 2>

<explanation + code block>

...

## Gotchas

- **<Gotcha 1>** — cause and fix in one sentence.
- **<Gotcha 2>** — cause and fix in one sentence.

## Related

- [Peer Topic](peer-topic.md) — one-line why-it-relates.
- [API_REFERENCE.md — Section](../API_REFERENCE.md#section-slug) — cross-link to main file.
```

Rules:

- **Title matches the Topic Deep-Dives link** in the main file.
- **First line is a blockquote** with one-sentence purpose and a pointer back to the main file.
- **Every subsection has at least one code block.** A heading with only prose but no example is a structural defect.
- **`## Gotchas` is optional** but encouraged — it captures the knowledge that would otherwise live in Troubleshooting.
- **`## Related` is required** — at minimum a pointer to `../API_REFERENCE.md`. It gives a skill navigation signals between references.
- **Max 300 lines.** If a topic grows past 300 lines, split it into two related files and cross-link.

---

## Snippet Format — Copy-Paste Ready

Every code block is a **self-contained snippet that runs out of the box**. The target workflow: the reader opens their language's REPL (Python, Node.js, IRB, GHCi), or pastes the snippet into a fresh file / notebook cell / SQL editor, substitutes parameter values, and the snippet runs.

Snippets are **not** full programs with `main()` and entry-point guards. They are focused demonstrations of one API surface at a time.

### Required shape of every code block

1. **Dependencies** — bring in what the snippet needs, at the top of the block. The form depends on the language:

   | Language | Form |
   |----------|------|
   | Python, TypeScript, Ruby, Swift | explicit `import` / `require` / `use` |
   | Go | `import "..."` |
   | Rust | `use ...` |
   | Java | `import ...;` |
   | C / C++ | `#include` |
   | Bash | `source file.sh` if needed; otherwise note required commands in a comment |
   | SQL | `WITH` / CTE setup, or a note about required tables |

2. **Parameters** — identifiers with realistic but generic values, near the top, each with a trailing comment explaining what the reader should substitute. Use the language's idiomatic constant style (UPPER_CASE in Python/JS, `const` declarations in Rust/Go, PascalCase in C#, `DECLARE` statements in SQL, `readonly` variables in Bash).

3. **API call(s)** — the focus of the snippet.

4. **Observable result** — a `print` / `console.log` / `fmt.Println` / `SELECT` / `echo` so the snippet has visible output. For side-effect-only APIs, a confirmation message is sufficient.

### Additional rules

- **<25 lines per snippet.** Longer flows split into several labeled snippets, each self-contained.
- **Error handling only when it is the point of the snippet**, or when the happy-path API raises a meaningful exception the reader should see. Avoid wrapping every line in `try/catch` / `Result` / `defer`-recover boilerplate.
- **No setup/teardown scaffolding inside the snippet.** If a snippet needs a prerequisite resource, state it as a one-line note above the block (*"Requires table `users` to exist — create it with the snippet in [Creating tables](#creating-tables)."*), not as code inside the snippet.
- **Realistic generic data.** Use stable, recognizable placeholders like `example.com`, `user@example.com`, `"John Doe"`, `USER.TEST.SEQ`. No `<your value>` tokens.
- **No emojis** in snippet output or prose unless the user explicitly requests them.
- **Docstrings only on helper functions** the reader might keep and reuse. Use the language's idiomatic style:

  | Language | Docstring convention |
  |----------|----------------------|
  | Python | Google-style / Sphinx / NumPy |
  | TypeScript / JavaScript | JSDoc |
  | Rust | rustdoc (`///`) |
  | Go | godoc (preceding `//` comment) |
  | Java | Javadoc (`/** */`) |
  | C# | XML doc comments (`///`) |
  | Ruby | YARD tags |
  | SQL | `-- comment` blocks with headings |
  | Bash | `#` comment block at the top of the function |

### Preference Ordering for alternatives

When multiple ways exist to do the same task, label them with comments in the language's idiomatic comment syntax:

```
# PREFERRED: <approach> (reason this is best)
<code>

# GOOD: <alternative> (when to use instead)
<code>

# OK: <acceptable-but-limited>
<code>

# AVOID: <anti-pattern>
<code>  # inline explanation of why this is wrong
```

Adapt the `#` to the language's comment character (`//` for C-family, `--` for SQL, `'` for VB, etc.). Every `AVOID:` block must be paired with the correct approach immediately above it. Never show an anti-pattern in isolation.

---

## Specification Documentation

### Environment Variables / Configuration

Document as a markdown table in the Installation section (when the library reads them at import / init time) or in the relevant topic file (when they are surface-specific):

```markdown
| Variable | Purpose |
|----------|---------|
| `LIB_HOME` | Install root |
| `LIB_TIMEOUT` | Default operation timeout |
```

For libraries configured by file rather than env vars, show complete, annotated configuration samples — every field has an inline comment explaining its purpose and default.

### Timeout / Retry Defaults

If the library performs IO: document the default timeout, how to override it, and what happens on timeout (error / fallback / partial result).

---

## Section Title Adaptation

The section titles in this rule are templates. Adapt them to match the domain's vocabulary — but **keep the ordering and the underlying purpose of each section**.

| Template title | Rename when the domain is … | Example adapted title |
|----------------|------------------------------|------------------------|
| Installation & Setup | always | *(usually unchanged)* |
| Core Patterns | task-oriented library | "Core Workflow" |
| Identifiers & Addressing | HTTP / REST | "URLs & Endpoints" |
| Identifiers & Addressing | query builder | "Table & Column References" |
| Identifiers & Addressing | UI framework | "Component & Prop Naming" |
| Common Actions | CLI / command library | "Common Commands" |
| Common Actions | event-driven library | "Event Handlers" |
| Topic Deep-Dives | always | *(usually unchanged)* |
| Best Practices | always | *(usually unchanged)* |
| Common Patterns & Solutions | task-oriented library | "Recipes" |
| Troubleshooting | always | *(usually unchanged)* |
| Quick Reference | always | *(usually unchanged)* |

The skill-migration path depends on predictable slot ordering — do not reorder sections, even when renaming.

---

## Domain-Aware Naming Checklist

Before finalizing the directory name and document scope statement:

1. **List every module, class, endpoint, or resource** the reference covers.
2. **Identify the umbrella term.** If the reference covers several sibling concepts, use the broadest term that still honestly describes the scope. Examples:
   - Covers sequential datasets, PDSes, GDGs, VSAM → "MVS files", not "datasets"
   - Covers React hooks, context, error boundaries → "React state and lifecycle", not "hooks"
   - Covers GET / POST / PATCH / WebSocket → "HTTP + WebSocket", not "HTTP"
   - Covers SELECT / INSERT / CTEs / Window functions → "SQL queries and analytics"
3. **Say the umbrella in the title** (`# <Domain> - Complete API Reference`) and in the one-sentence description below.
4. **Match the directory name.** The umbrella term becomes `<domain>` in `<language>-<domain>-api-reference/`.

If you cannot pick a single umbrella that honestly covers the scope, split the reference into multiple directories. Each remains independently skill-loadable.

---

## Verification

Execution verification is **optional**. The default is a static cross-check performed while writing.

### Static Cross-Check (always performed)

- Every symbol (function, class, method, endpoint, kwarg, attribute, flag) used in the output must appear in the official source documentation.
- Every signature must match the documented signature exactly — positional args, keyword args, defaults, return type.
- Every import / require / include path must match a module actually exposed by the library.
- No back-references to source documentation exist in the output (self-containment check).

### Live Execution (only if the user opts in)

If the user has a runnable environment, execute every example verbatim:

1. **Build a test-spec table** — Test ID, section, example description, expected outcome, status.
2. **Present the table to the user** for scope adjustment.
3. **Collect mock inputs** — URLs, credentials, data files, env vars.
4. **Execute each example unchanged** — substituting only the user-provided inputs.
5. **Record pass / fail** against the expected outcome.
6. **Propose fixes as `old_string → new_string`** diffs for failed tests; await approval before applying.
7. **Re-run failed tests** after fixes; repeat until all pass.
8. **Present the final test-spec table** as the delivery artifact.

If the user declines live execution, do not add a "Verification Status" section to the output file — the absence of a live run is understood.

---

## Skill Migration Path

The output is designed so a future `SKILL.md` can reuse it with minimal changes:

- **Body content** — `API_REFERENCE.md` content is copied into `SKILL.md` with minor framing.
- **Additions in `SKILL.md`**:
  1. **Manifest metadata** — a name and a description (the trigger text), in whatever frontmatter or metadata format the target agent runtime expects.
  2. **Activation blurb** — 1–2 sentences describing when to use and when not to use the skill.
- **References** — `references/<topic>.md` files are kept alongside `SKILL.md` and loaded on-demand by the agent runtime.
- **Workflow guidance** — already provided by Core Patterns, Best Practices, Common Patterns, and Troubleshooting. Do not add a separate "workflow" section at skill-migration time; the existing sections already serve that role.

The reference author does not need to know the target runtime's skill schema — leave skill-specific framing for the skill-creation step. The reference's job is to produce correct, self-contained, skill-loadable content regardless of target language, domain, or agent.
