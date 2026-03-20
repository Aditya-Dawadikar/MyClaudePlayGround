---
name: readme-diagrammer
description: 'Generate an Architecture diagram from a Markdown file. Use when a document may contain an Architecture section and you want a PNG diagram embedded automatically.'
argument-hint: 'Markdown path optional; if omitted and multiple .md files exist, ask user to choose one; emit a SKIP log if Architecture is missing'
---

# README Diagrammer

Generate a diagram from the selected Markdown file's Architecture section.

## When to Use

- The source is a Markdown file.
- The output must be a PNG diagram embedded in the selected Markdown file.

## Core Rules

- Only target the first `## Architecture` (or `# Architecture`) section in the selected Markdown file.
- If no Architecture section exists, stop immediately and do not generate files.
- Always emit status logs. Never stop silently.
- Always ask which Markdown file to use when more than one `.md` file is available or when no explicit path is provided.
- If exactly one `.md` file exists, use it without asking.
- Build an intermediate representation before Mermaid.
- Do not invent components or relationships that are not in the Architecture text.
- Emit one Mermaid statement per line with minimal spacing.
- Render PNG output and embed the PNG reference in the selected Markdown file.
- Render PNG at high resolution (default 2400x1350 with scale 2) to avoid blur on large screens.
- Use descriptive output filenames (same base name for `.mmd` and `.png`) derived from the document context.
- Do not embed Mermaid code blocks in the selected Markdown file.

## Procedure

1. Resolve the target Markdown file:
	- If a path is explicitly provided, use it.
	- If multiple `.md` files exist and no explicit path is provided, ask the user to choose one before continuing.
	- If exactly one `.md` file exists and no explicit path is provided, select it automatically.
2. Read the target Markdown file and locate the Architecture section.
3. If Architecture section is missing, end process and emit a skip log.
4. Extract Architecture section content only.
5. Produce an intermediate representation using [IR template](./references/ir-template.json).
6. Convert IR to Mermaid flowchart.
7. Normalize Mermaid to one statement per line and validate syntax.
8. Choose a descriptive slug for output files (for example: `system-overview`, `service-topology`, `checkout-architecture`).
9. Save Mermaid source to `assets/<slug>.mmd`.
10. Render PNG to `assets/<slug>.png` using Mermaid CLI with high-resolution settings (`-w 2400 -H 1350 --scale 2`).
11. Patch the selected Markdown file Architecture section with `![Architecture](assets/<slug>.png)`.
12. Replace existing Architecture image reference if already present.

## Logging Requirements

Always print concise logs for every run using this format:

```text
[readme-diagrammer] LEVEL CODE: message
```

Required levels:

- `INFO` for start and successful completion.
- `SKIP` when Architecture section is missing.
- `ERROR` when validation or PNG render fails.

Minimum required log events:

1. Start: target Markdown path.
2. Detection result: Architecture found or not found.
3. Generation result: Mermaid written path.
4. Render result: PNG written path or render failure.
5. Patch result: Markdown file updated path.
6. Final outcome summary.

Example skip log:

```text
[readme-diagrammer] SKIP ARCH_SECTION_MISSING: No 'Architecture' section found in selected markdown file. No files were changed.
```

## Output Expectations

- Always generate `assets/<slug>.mmd` and `assets/<slug>.png` with a descriptive shared base name.
- Always embed `![Architecture](assets/<slug>.png)` in the selected Markdown file.
- Never add Mermaid fenced code to the selected Markdown file.

## References

- [Workflow details](./references/workflow.md)
- [IR template](./references/ir-template.json)
- [Markdown patch rules](./references/markdown-patching.md)