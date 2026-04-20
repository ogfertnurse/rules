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

## Planning Phase

Before converting, build a structured plan that breaks the work into manageable units.

### Step 1: Extract the Table of Contents

Read the source's table of contents (or sitemap for web sources) to build an authoritative list of every chapter, section, and subsection with its page range or URL.

### Step 2: Compute Word Counts Per Chapter

Extract a baseline word count for each chapter or section. The method depends on the source type.

**For PDF sources**, use `pdftotext` to extract word counts per chapter:

```bash
# Determine the PDF page offset (front matter pages before page 1)
# Then for each chapter:
pdftotext -f <pdf_start> -l <pdf_end> source.pdf - | wc -w
```

**For web/URL sources**, fetch each page and count words from the visible text:

```bash
# Using curl + sed to strip HTML tags:
curl -s "https://example.com/docs/chapter1" | sed 's/<[^>]*>//g' | wc -w

# Or using lynx for cleaner text extraction (if available):
lynx -dump -nolist "https://example.com/docs/chapter1" | wc -w
```

For web sources where exact word counts are impractical (e.g., dynamically rendered pages, content behind authentication), use a **heading-based audit** instead: list every `<h2>` and `<h3>` heading on the source page and verify each appears in the markdown. This serves as the completeness check in place of word counts.

Record a reference table of chapter/section titles, page ranges (or URLs), and word counts. This table is the baseline for verification.

### Step 3: Design the Directory Structure

Map each chapter/section to a target markdown file or subdirectory:

- **Small chapters** (< 5,000 words): Single markdown file.
- **Medium chapters** (5,000-15,000 words): Single file or split into 2-3 files if the chapter has distinct subsections.
- **Large chapters** (> 15,000 words): Subdirectory with its own `llms.txt` and multiple files, grouped by logical subsections (e.g., alphabetical parameter ranges).

### Step 4: Create Chapter Plans

Group chapters into execution batches. Each batch plan should include:

- File names and target paths
- PDF page ranges (or URLs)
- Sections to include from the TOC
- Estimated word count

### Step 5: Identify Cross-Reference Strategy

Decide upfront how to handle internal cross-references:

- During initial conversion, write references as-is (e.g., "see page 147").
- After all content batches complete, run a dedicated cross-reference linking pass to convert page references to relative markdown links.
- References to external publications stay as plain text.

### Step 6: Present the Plan and Wait for Approval

**Required.** Before creating any directories, writing any markdown files, or dispatching any conversion agents, present the plan to the user and explicitly wait for approval. Do not proceed until the user says "go", "approved", "proceed", or equivalent.

The plan must include:

- **Source summary**: document title, total pages, total source word count.
- **Scope**: which chapters/sections will be converted (and which will be excluded, if the user specified a subset).
- **Target directory**: the topic directory under `docs/` that will be created (e.g., `docs/ip-users-guide/`).
- **Per-unit plan table** with one row per target markdown file:

  | # | Target file | Source pages | Source words | Size bucket |
  |---|---|---|---|---|
  | 1 | `00-section/00-topic.md` | p.43–76 | 11,750 | medium |
  | 2 | `00-section/01-next.md` | p.77–87 | 4,928 | small |

  `Size bucket`: small (< 5K), medium (5K–15K), large (> 15K).
- **Split rationale**: for every chapter split across multiple files, one line explaining the split boundary (alphabetical range, subtopic grouping, etc.).
- **Execution plan**: how many parallel agents will run per wave, and the total number of waves. Example: "Wave 1 = 5 agents for Ch1-5; Wave 2 = 4 agents for Ch6-10; Wave 3 = verification."
- **Cross-reference strategy** (from Step 5): whether page references will be linked in a post-conversion pass.

After presenting the plan, stop and ask: *"Approve this plan, or would you like changes?"* If the user requests changes, revise the plan and re-present it. Only begin execution once the user has explicitly approved.

If the user has pre-authorized autonomy for this task ("just do it", "don't ask, proceed"), you may skip the approval step — but still present the plan first as a reference.

## Conversion Phase

### Workflow Per Batch

1. **Read** the source pages for the batch (for PDFs, max 20 pages per read).
2. **Write** the markdown file(s) following all formatting and structure rules above.
3. **Verify** word count of generated markdown vs source word count.
4. **Inspect** if the delta exceeds the threshold — re-read source and fix gaps.

### Parallel Execution

For large documents, use sub-agents to convert multiple chapters in parallel:

- Each agent receives the file path, page range, target markdown path, and sections to include.
- Agents can run concurrently on non-overlapping page ranges.
- Verify each agent's output after completion.

## Verification Phase

### Word Count Verification

After each batch, compare markdown word count against the source word count from the reference table.

```bash
# Per-file verification
wc -w docs/<path>/file.md

# Per-subdirectory verification
find docs/<path>/<subdir> -name "*.md" -exec cat {} + | wc -w

# Full project verification
find docs -name "*.md" -exec cat {} + | wc -w
```

### Pass Criteria

- **Acceptable delta**: Markdown word count should be within **85-115%** of source word count.
  - Markdown formatting syntax (`#`, `|`, `` ``` ``) inflates word count slightly.
  - Removing headers, footers, and page numbers from the source deflates it slightly.
  - These effects roughly cancel out, so a ±15% range accounts for normal variance.
- **If delta > 15%**: Flag the chapter for inspection. Compare TOC section headings against markdown `##`/`###` headings to identify missing sections. Re-read the source pages and add missing content.
- **If delta < -15%**: Content is likely missing. Re-read source and compare section-by-section.
- **If delta > +15%**: Markdown formatting may be heavy (large tables), or content may be duplicated. Inspect for duplication.

### Heading Audit

For each chapter, extract `##` and `###` headings from the markdown and compare against the source's section/subsection headings for that chapter. Every source heading should have a corresponding markdown heading.

### Structural Integrity Check

After conversion, review the markdown for content completeness beneath every heading and list item. A heading or bullet point that promises content but has nothing underneath it is a structural defect. Check for:

- **Empty sections**: A `##` or `###` heading followed immediately by another heading with no body text between them. Every heading must have content beneath it — at least one paragraph, list, code block, or table.
- **Stub bullet points**: A bullet point that names a topic (e.g., "Examples of the ACCODE parameter") but has no examples, code blocks, or explanatory text following it. If the source has content under that item, the markdown must too.
- **Incomplete lists**: A numbered or bulleted list where the source has N items but the markdown has fewer. Count the items and compare.
- **Missing code blocks**: A section that describes syntax, examples, or commands but contains no fenced code block when the source has one.
- **Orphaned references**: Text like "as shown in the following example" or "the following table shows" with no example or table following it.

To automate part of this check, scan for back-to-back headings with no content between them:

```bash
# Find headings immediately followed by another heading (no content between)
grep -n '^##' docs/<path>/file.md | awk -F: '{
  if (prev_line && $1 == prev_line + 1) print "Empty section at line " prev_line ": " prev_text
  prev_line = $1; prev_text = $0
}'
```

For any flagged sections, re-read the corresponding source pages and add the missing content.

### Reporting Verification Results

When presenting verification results to the user, always follow this format:

**Word count verification must be reported as a markdown table** with one row per file:

| File | Source | Markdown | Ratio | Status |
|---|---:|---:|---:|:---|
| `<path>` | `<src>` | `<md>` | `<pct>%` | PASS / UNDER / OVER |

- Columns: file path (relative to the topic directory), source word count, markdown word count, ratio (markdown/source × 100), status.
- `Status` values: **PASS** (85–115%), **UNDER** (< 85%), **OVER** (> 115%).
- Include a totals row or a one-line total below the table: markdown total / source total = overall ratio%.
- For any file marked OVER or UNDER, include a one-line justification below the table (e.g., "Appendix B is a dense mapping table — pipes inflate `wc` output; content verified 1:1").

**All other verifications must be reported as a short summary**, not a table. Each verification type gets a named heading and a one-to-three-line result:

- **Structural integrity check**: state whether any back-to-back headings, stub bullets, or orphaned references were found. If any, list the offenders (file:line).
- **Heading audit**: state whether every source section/subsection heading has a corresponding markdown heading. If any are missing, list them.
- **File structure check**: state the count of markdown files and `llms.txt` indexes, and whether every directory has an `llms.txt`.
- **Cross-reference linking pass**: state whether it has been run. If not run, say so explicitly ("pending").

Do not repeat raw command output in the report. Extract the relevant facts and present them in the table + summary form above.

### Cross-Reference Linking Pass

After all content batches complete, scan all markdown files for patterns like:

- `see page NNN`
- `"Section Name" on page NNN`
- `see Chapter NN`
- `Table N on page NNN`
- `described on page NNN`

Build a page-to-file mapping from the chapter plan, then replace each reference with a relative markdown link. Skip references to content outside the conversion scope (e.g., excluded chapters, external publications).

Verify no broken links remain by checking that every link target file exists.

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
