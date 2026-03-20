# Markdown Patch Rules

When inserting a diagram back into a Markdown file:

1. Patch only the Architecture section.
2. Preserve the existing heading and paragraph content.
3. Insert the image near the top of the Architecture section.
4. Use this pattern:

```md
## Architecture

![Architecture](assets/<slug>.png)
```

5. Do not add Mermaid fenced code to the selected Markdown file.
6. Detect an existing Architecture image and replace it instead of appending a duplicate.
7. Keep spacing and list formatting consistent with the original document.
8. Preserve the chosen descriptive slug consistently across references during replacement.