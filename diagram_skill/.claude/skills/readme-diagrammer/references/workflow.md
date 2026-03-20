# Workflow Details

## Scope

This workflow is intentionally minimal with one required disambiguation interaction.

- Input: selected Markdown file.
- Target: Architecture section only.
- Output: `assets/<slug>.mmd`, `assets/<slug>.png`, and a Markdown image link.

File selection requirement:

- If a Markdown path is explicitly provided, use it.
- If no explicit path is provided and multiple `.md` files exist, ask the user to choose one file before continuing.
- If no explicit path is provided and exactly one `.md` file exists, use it automatically.

If Architecture section is absent, stop immediately and emit a `SKIP` log.

## Logging

Use concise lifecycle logs throughout execution:

```text
[readme-diagrammer] LEVEL CODE: message
```

Required events:

1. `INFO START` with selected Markdown path.
2. `INFO ARCH_SECTION_FOUND` or `SKIP ARCH_SECTION_MISSING`.
3. `INFO MERMAID_WRITTEN` with `.mmd` path.
4. `INFO PNG_WRITTEN` or `ERROR PNG_RENDER_FAILED`.
5. `INFO README_PATCHED` with selected Markdown path.
6. `INFO DONE` summary with changed files.

## IR-First Generation

Never write Mermaid directly from the selected Markdown text.

Create an IR that includes:

- `title`
- `diagram_type`
- `nodes`
- `edges`
- `groups`
- optional `notes`

Guardrails:

- Keep diagrams compact and readable.
- Reuse the document's terminology for labels.
- Do not split into multiple diagrams in this minimal mode.

## Validation Loop

Before writing files:

1. Check Mermaid syntax.
2. Normalize Mermaid text to one statement per line.
3. Collapse repeated spaces to single spaces around arrows and labels.
4. Check that node labels and edge labels reflect the Architecture text.
5. Run an artifact check for common corruption patterns, including merged lines, repeated trailing node ids, and truncated tokens.
6. Retry up to two times if Mermaid generation fails.

If validation still fails, emit `ERROR MERMAID_VALIDATION_FAILED` and do not patch the selected Markdown file.

### Artifact Check Heuristics

Treat the diagram as invalid and regenerate if any of the following appear:

- More than one arrow expression on the same line when the diagram type is flowchart.
- A line that ends with a partial token fragment that duplicates the next line start.
- Duplicate adjacent node ids caused by accidental newline loss.
- Trailing content after a valid edge that does not parse as Mermaid syntax.

When repairing artifacts, prefer rewriting affected lines from IR rather than attempting character-level edits.

## Artifact Output

Preferred output layout:

```text
assets/
  <slug>.mmd
  <slug>.png
```

Filename rules:

- Choose a short descriptive slug based on document context (example: `system-overview`, `service-topology`, `checkout-architecture`).
- Use lowercase kebab-case characters (`a-z`, `0-9`, `-`) only.
- Reuse the same slug for both `.mmd` and `.png` files.

Render command:

```bash
npm install -g @mermaid-js/mermaid-cli
mmdc -i assets/<slug>.mmd -o assets/<slug>.png -b transparent -w 2400 -H 1350 --scale 2
```

Resolution guidance:

- Use 2400x1350 as the default PNG canvas for architecture diagrams.
- Use scale 2 to improve raster sharpness on large and high-DPI screens.
- Keep transparent background unless the document theme requires a solid fill.

Patch the selected Markdown file by inserting or replacing this line within the Architecture section:

```md
![Architecture](assets/<slug>.png)
```

## Failure Handling

- If Architecture section is missing, stop with no changes and emit `SKIP ARCH_SECTION_MISSING`.
- If Mermaid render to PNG fails, stop and emit `ERROR PNG_RENDER_FAILED`.
- If an Architecture image already exists, replace only that image link.