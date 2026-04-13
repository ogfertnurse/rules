# Verifying Markdown Conversions from PDFs

When reviewing or auditing existing markdown documentation that was converted from a PDF source, follow this process to identify missing content, structural issues, and opportunities for better organization.

## Phase 1: Build a Source Map

1. **Read the PDF table of contents.** Extract every chapter, section, and subsection heading with its page number. This is the authoritative list of what should exist in the markdown.
2. **Read the `llms.txt` indexes** in the target directory (and any child directories). List every markdown file and its described scope.
3. **Cross-reference.** For each PDF section, identify which markdown file (if any) covers it. Flag sections with no corresponding markdown as "missing entirely."

## Phase 2: Check Coverage Within Each File

For every markdown file that maps to PDF content:

1. **Compare the `> Source:` page range** against the PDF TOC. If the file claims p.62-85 but the TOC shows 6 sections spanning p.62-107, the file may be truncated.
2. **Scan section headings.** List the `##` and `###` headings in the markdown and compare against the PDF's section/subsection headings for the same page range. Flag any PDF headings that have no corresponding markdown heading.
3. **Check section completeness.** For each section that does exist in the markdown, read the corresponding PDF pages and verify:
   - All paragraphs of explanatory text are present (not just the first sentence).
   - All tables are present with complete rows and columns.
   - All code examples, JCL samples, and command-line examples are included.
   - All numbered/bulleted lists are complete (not truncated mid-list).
   - All subsections are present (a section may have its heading but be missing child subsections).
4. **Check for abrupt endings.** A section that ends after only 1-2 sentences when the PDF has a full page of content is a strong signal of truncation.

## Phase 3: Check for Duplication

1. **Identify overlapping content.** When multiple files cover adjacent page ranges, check whether content is duplicated across files (e.g., the same table or code block appears in both).
2. **Choose the authoritative location.** Keep the more complete or contextually appropriate version. Remove the duplicate from the other file.

## Phase 4: Evaluate Organization

1. **File size balance.** If a single topic spans multiple files totaling 700+ lines, or if a single file exceeds 500 lines with clearly distinct subtopics, consider reorganizing into a subdirectory with its own `llms.txt` and focused files.
2. **Logical grouping.** Sections should be grouped by purpose:
   - Installation and configuration (setup, env files, security definitions)
   - APIs and interfaces (REST endpoints, request/response formats)
   - Operations (starting/stopping, PROCLIB, PARMLIB, dynamic commands)
   - User-facing features (web UIs, profile management, interactive tools)
   - Diagnostics and troubleshooting (logging, status codes, verification)
3. **Content placement.** If a section is in a file titled "APIs" but its content is about security configuration, it belongs in the configuration file. Match content to the file's purpose, not just the original PDF ordering.

## Phase 5: Produce a Findings Report

Summarize findings in the plan file before making changes:

1. **Missing sections** — PDF sections with no markdown equivalent. Include the section name and page range.
2. **Truncated sections** — Markdown sections that exist but have incomplete content. Note what is present vs. what the PDF contains.
3. **Misplaced sections** — Content that exists but is in the wrong file given its topic.
4. **Duplicated content** — Content that appears in multiple files.
5. **Structural recommendations** — Whether to reorganize into subdirectories, merge files, or split files.

## Phase 6: Plan and Execute Fixes

1. Create new files or subdirectories as needed, following the rules in `pdf-to-markdown-conversion.md`.
2. Add missing content by reading the relevant PDF pages and converting according to the formatting and content rules.
3. Move misplaced content to the correct file.
4. Remove duplicated content, keeping the version in the most appropriate file.
5. Update all affected `llms.txt` indexes and `CLAUDE.md`.

## Content Fidelity Rule

**The PDF is the source of truth. Do not skip, summarize, or condense content when converting.**

When writing or fixing a markdown section, include the full content from the corresponding PDF pages:

- **Every paragraph** of explanatory text, not just the first sentence or a summary.
- **Every example** — if the PDF shows 4 examples, the markdown must have all 4. Do not pick a subset.
- **Every code sample** — commands, scripts, configuration snippets, and sample output must be reproduced in full.
- **Every note, warning, and tip** — including multi-bullet note blocks.
- **Every table row and column** — do not truncate tables or omit rows.
- **Every numbered step and sub-step** — including lettered sub-steps (a, b, c) and their full descriptions.
- **Every parameter description** — including "where" blocks that define variables and placeholders.
- **Every subsection** — if a PDF section has child headings, each must appear in the markdown with its complete content.
- **Contextual flow** — include the explanatory text that connects sections together. Introductory paragraphs, prerequisite descriptions, and process overviews that explain how components interact are essential for the reader to understand the full picture.

A section that has a heading and 1-2 sentences when the PDF has a full page under that heading is **incomplete**, not done. Read the full PDF page range for every section you write and convert all of it.
