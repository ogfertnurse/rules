# Documentation Structure and Conversion Rules

Rules for organizing markdown documentation and converting content from PDF or web sources.

## Directory Structure

Documentation lives under `docs/` and uses a recursive directory tree. Every directory that contains content has an `llms.txt` index. Both directories and files use zero-padded numeric prefixes with kebab-case naming.

```
docs/
├── llms.txt                                    (root index — no header blockquote)
└── <topic>/                                     (e.g., zos-debugger/)
    ├── llms.txt                                 (topic index → links to ../llms.txt)
    └── <NN-section>/                            (e.g., 00-general-reference/)
        ├── llms.txt                             (section index → links to ../llms.txt)
        │
        ├── <NN-subsection>/                     (directory with files)
        │   ├── llms.txt                         (lists markdown files)
        │   ├── 00-topic-name.md
        │   └── 01-next-topic.md
        │
        └── <NN-subsection>/                     (directory with child directories)
            ├── llms.txt                         (lists child llms.txt files)
            ├── <NN-child>/
            │   ├── llms.txt                     (lists markdown files)
            │   ├── 00-file.md
            │   └── 01-file.md
            └── <NN-child>/
                ├── llms.txt
                └── 00-file.md
```

### Key rules

- The tree can nest to any depth. A directory either contains markdown files, child directories, or both.
- Every directory that has content (markdown files or child directories) must have an `llms.txt`.
- Both directories and files use `NN-kebab-case` naming: `00-topic-name/`, `01-next-topic.md`.
- Numbering is sequential within each directory: `00`, `01`, `02`, ... with no gaps.
- New files or directories continue from the last existing number in that directory.

## llms.txt Format

### Root index (`docs/llms.txt`)

No header blockquote. Lists topic areas.

```markdown
# Project or Domain Title

> One-line description of the documentation scope.

## Docs

- [Topic Name](topic-dir/llms.txt): One-line description of what this topic covers.
```

### All other `llms.txt` files

Every non-root `llms.txt` starts with a header blockquote linking to the parent `../llms.txt`.

An `llms.txt` can list **child directory indexes**, **markdown files**, or **both**:

```markdown
> ## Documentation Index
> Fetch the complete documentation index at: [llms.txt](../llms.txt)
> Use this file to discover all available pages before exploring further.

# Section Title

> One-line description of the documentation scope and source material.

## Docs

- [Child Section](00-child-dir/llms.txt): One-line description.
- [Another Child](01-another-dir/llms.txt): One-line description.
```

Or when listing markdown files directly:

```markdown
> ## Documentation Index
> Fetch the complete documentation index at: [llms.txt](../llms.txt)
> Use this file to discover all available pages before exploring further.

# Section Title

> One-line description of the documentation scope and source material.

## Docs

- [Page Title](00-filename.md): One-line description of what this page covers.
- [Page Title](01-filename.md): One-line description of what this page covers.
```

Or both (child directories and markdown files in the same directory):

```markdown
## Docs

- [Overview](00-overview.md): Overview of this section.
- [Subsection A](01-subsection-a/llms.txt): Detailed subsection with multiple pages.
- [Subsection B](02-subsection-b/llms.txt): Another detailed subsection.
- [Summary](03-summary.md): Summary and cross-references.
```

## Markdown File Structure

Every markdown file follows this layout:

```markdown
> ## Documentation Index
> Fetch the complete documentation index at: [llms.txt](llms.txt)
> Use this file to discover all available pages before exploring further.

# Page Title

> Source: Document Name, Chapter/Section, page range (e.g., p.103-105)

## Overview

Content starts here...
```

- **Lines 1-3**: Documentation index header as a blockquote, linking to `llms.txt` in the same directory.
- **Blank line**, then the `# Title`.
- **Blank line**, then `> Source:` attribution with document name, chapter, and pages.
- **Blank line**, then `## Overview` and content.
- For web-sourced documentation, the `> Source:` line uses a clickable markdown link: `> Source: [Title](url)`.

## Formatting Rules

1. **Tables**: Use standard markdown table format (`| col1 | col2 |`). Preserve all data from source tables.
2. **Code samples**: Use fenced code blocks with appropriate language tags (e.g., ` ```jcl `, ` ```cobol `, ` ```c `, ` ```pli `, ` ```text `, ` ```bash `).
3. **Syntax diagrams / railroad diagrams**: Convert to ASCII art inside fenced code blocks (` ```text `).
4. **Screen panels / terminal output**: Render as ASCII art inside fenced code blocks (` ```text `). Preserve field labels, input areas, PF key assignments, and panel layout.
5. **Inline references** to commands, options, parameters, or code: Use inline code formatting with backticks.
6. **No images**: Convert all visual content to text or ASCII representations.
7. **Cross-references**: Use relative markdown links between docs files (e.g., `[link text](07-other-doc.md)`).

## Content Fidelity Rules

**The source document is the source of truth. Do not skip, summarize, or condense content when converting.**

### What must be preserved 1:1

- **Every paragraph** of explanatory text, not just the first sentence or a summary.
- **Every example** — if the source shows 4 examples, the markdown must have all 4. Do not pick a subset.
- **Every code sample** — commands, scripts, JCL, configuration snippets, and sample output must be reproduced in full.
- **Every JCL sample** — PROC, JOB, EXEC, DD statements reproduced completely.
- **Every configuration sample** — .env files, PARMLIB members, properties files, config file contents in full.
- **Every security definition** — RACF commands (RDEFINE, PERMIT), facility class profiles, certificate commands.
- **Every screen panel / green screen image** — converted to ASCII art preserving layout, field labels, and PF key assignments.
- **Every note, warning, and tip** — including multi-bullet note blocks.
- **Every table row and column** — do not truncate tables or omit rows.
- **Every numbered step and sub-step** — including lettered sub-steps (a, b, c) and their full descriptions.
- **Every parameter description** — including "where" blocks that define variables and placeholders.
- **Every subsection** — if a source section has child headings, each must appear in the markdown with its complete content.
- **Inline examples** — text that says "For example:" or "For instance:" within body text are examples too, not just content under "Examples" headings.
- **Sample output** — command responses, log output, message output with exact values.
- **Contextual flow** — introductory paragraphs, prerequisite descriptions, and process overviews that connect sections together.

A section that has a heading and 1-2 sentences when the source has a full page under that heading is **incomplete**, not done.

## Extending Existing Documentation

When adding new pages to an existing documentation set:

1. Continue the numbering sequence from the last existing file or directory in that directory.
2. Add new entries to `llms.txt` in the appropriate position.
3. Every new file must include the documentation index header at the top.
4. Update cross-references in existing files if the new content is referenced.

## When to Use Subdirectories

Use subdirectories instead of flat files when any of the following apply:

- **Logical grouping**: Multiple files cover the same topic from different angles (e.g., a configuration guide and a reference for the same component). Group them in one subdirectory.
- **Eliminating numbering gaps**: Flat files with non-sequential numbers (e.g., 00, 01, 02, 06, 07) indicate a structural problem. Reorganize into subdirectories with sequential numbering.
- **Scalability**: A section is likely to grow. A subdirectory with its own `llms.txt` is easier to extend than adding more flat files to a crowded parent directory.

A directory may contain both markdown files and child subdirectories. The `llms.txt` for such a directory lists both (files as `NN-name.md` links, subdirectories as `NN-name/llms.txt` links).

When creating subdirectories, files within are renumbered starting from `00-`. Each subdirectory gets its own `llms.txt` following the standard format.
