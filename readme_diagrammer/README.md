# diagram_skill

This repository is a Claude skill package.

The reusable skill is located at `.claude/skills/readme-diagrammer` and is intended to be copied or loaded into other projects.

## Purpose

The `readme-diagrammer` skill generates an Architecture diagram from a selected Markdown file by:

1. Reading the document's `Architecture` section.
2. Building an intermediate representation.
3. Producing Mermaid output.
4. Rendering a high-resolution PNG.
5. Embedding the PNG in the Markdown file.

## Reusable skill location

- `.claude/skills/readme-diagrammer/SKILL.md`
- `.claude/skills/readme-diagrammer/references/workflow.md`
- `.claude/skills/readme-diagrammer/references/ir-template.json`
- `.claude/skills/readme-diagrammer/references/markdown-patching.md`

These files are the actual portable skill implementation.

## Test fixtures in this repo

The following files are provided for testing and validation only:

- `Sample_Architecture.md`
  - Sample architecture source for running the skill
- `Sample_DB_Schema.md`
  - Additional sample document for diagram extraction experiments
- `assets/ecommerce-db-schema.mmd`
  - Example Mermaid output used for testing
- `assets/ecommerce-db-schema.png`
  - Rendered diagram image from the sample Mermaid file

## How to reuse in another project

1. Copy `.claude/skills/readme-diagrammer` into the target project's `.claude/skills/` directory.
2. Ensure the target project has Markdown files with an `Architecture` section.
3. Invoke the `readme-diagrammer` skill in that project.
4. Confirm generated files in `assets/` and the updated Markdown image reference.

## Notes

- If no `Architecture` section exists, the skill should skip generation and log a skip outcome.
- Output filenames should be descriptive and shared between `.mmd` and `.png`.
