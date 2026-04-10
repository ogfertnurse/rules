# PDF-to-Markdown Conversion Rules

When converting PDFs to markdown documentation, follow this structure and these formatting rules.

## Project Structure

Documentation uses a nested directory layout under `docs/` with three levels of `llms.txt` indexes:

```
docs/
├── llms.txt                              (top-level index — lists topic areas)
└── <topic>/                              (e.g., zos-debugger/)
    ├── llms.txt                          (topic-level index — lists subtopic llms.txt files)
    └── <subtopic>/                       (e.g., test-runtime-options/)
        ├── llms.txt                      (subtopic-level index — lists markdown files)
        ├── 00-topic-name.md
        ├── 01-next-topic.md
        └── ...
```

- `docs/llms.txt` is the top-level documentation index listing all topic areas. It has no header blockquote.
- Each topic directory has its own `llms.txt` that links up to the top-level `../llms.txt` and down to subtopic `llms.txt` files.
- Each subtopic directory has its own `llms.txt` that links up to the topic-level `../llms.txt` and lists the markdown files in that directory.
- Markdown files link to the `llms.txt` in their own directory (the subtopic-level index).
- Name markdown files with zero-padded numeric prefixes and kebab-case: `00-topic-name.md`, `01-next-topic.md`, etc.

## llms.txt Format

### Top-level index (`docs/llms.txt`)

No header blockquote. Lists topic areas linking to their `llms.txt` files.

```markdown
# Project or Domain Title

> One-line description of the documentation scope.

## Docs

- [Topic Name](topic-dir/llms.txt): One-line description of what this topic covers.
```

### Topic-level index (`docs/<topic>/llms.txt`)

Header links up to the top-level `../llms.txt`. Lists subtopic areas linking to their `llms.txt` files.

```markdown
> ## Documentation Index
> Fetch the complete documentation index at: [llms.txt](../llms.txt)
> Use this file to discover all available pages before exploring further.

# Topic Name Documentation

> One-line description of the documentation scope and source material.

## Docs

- [Subtopic Name](subtopic-dir/llms.txt): One-line description of what this subtopic covers.
```

### Subtopic-level index (`docs/<topic>/<subtopic>/llms.txt`)

Header links up to the topic-level `../llms.txt`. Lists the markdown files in the same directory.

```markdown
> ## Documentation Index
> Fetch the complete documentation index at: [llms.txt](../llms.txt)
> Use this file to discover all available pages before exploring further.

# Subtopic Title

> One-line description of the documentation scope and source material.

## Docs

- [Page Title](00-filename.md): One-line description of what this page covers.
- [Page Title](01-filename.md): One-line description of what this page covers.
```

All non-top-level `llms.txt` files must include the documentation index header (lines 1-3) linking to the parent `../llms.txt`.

## Markdown File Structure

Every markdown file must follow this layout:

```markdown
> ## Documentation Index
> Fetch the complete documentation index at: [llms.txt](llms.txt)
> Use this file to discover all available pages before exploring further.

# Page Title

> Source: Document Name, Chapter/Section, page range (e.g., p.103-105)

## Overview

Content starts here...
```

- **Lines 1-3**: Documentation index header as a blockquote, linking to `llms.txt` (the subtopic-level index in the same directory)
- **Blank line**, then the `# Title`
- **Blank line**, then `> Source:` attribution with document name, chapter, and pages
- **Blank line**, then `## Overview` and content

## Formatting Rules

1. **Tables**: Use standard markdown table format (`| col1 | col2 |`). Preserve all data from source tables.
2. **Code samples**: Use fenced code blocks with appropriate language tags (e.g., ```jcl, ```cobol, ```c, ```pli, ```text, ```bash).
3. **Syntax diagrams / railroad diagrams**: Convert to ASCII art inside fenced code blocks (```text).
4. **Screen panels / terminal output**: Render as ASCII art inside fenced code blocks (```text).
5. **Inline references** to commands, options, parameters, or code: Use inline code formatting with backticks.
6. **No images**: Convert all visual content from the PDF to text or ASCII representations.
7. **Cross-references**: Use relative markdown links between docs files (e.g., `[link text](07-other-doc.md)`).

## Content Rules

- Preserve ALL code examples, sample code, scripts, and command-line examples from the source PDF
- Preserve ALL tables with their complete data
- Preserve ALL syntax definitions and parameter descriptions
- Split content into separate files by chapter or logical topic to keep each file focused
- When a chapter references content in other chapters that is essential context, include that content as additional markdown files and note the cross-reference

## Extending Existing Documentation

When adding new pages to an existing documentation set:

1. Continue the numbering sequence from the last existing file
2. Add new entries to `llms.txt` in the appropriate position
3. Every new file must include the documentation index header at the top
4. Update cross-references in existing files if the new content is referenced
