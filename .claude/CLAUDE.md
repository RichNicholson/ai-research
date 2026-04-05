# AI Research Repository

This repo contains deep-dive research notes published as a MkDocs Material site.

## Structure

- `docs/` — MkDocs source files (Markdown)
- `docs/<topic>/` — each researched topic gets its own directory
- `mkdocs.yml` — site configuration and navigation
- `site/` — built output (gitignored)

## Commands

- `mkdocs serve` — local preview at http://127.0.0.1:8000
- `mkdocs build` — build static site to `site/`
- `mkdocs gh-deploy` — deploy to GitHub Pages

## Research workflow

Use `/research <topic>` to kick off a deep-dive. This dispatches multiple researcher agents in parallel, then synthesises findings into MkDocs pages.

Each topic produces:
- An overview page (concepts, architecture, how it works)
- A practical guide (getting started, best practices, common patterns)
- A community insights page (opinions, gotchas, real-world experience from Reddit etc.)
- A sources page (all references with links)

## Writing conventions

- Use admonitions for tips, warnings, important notes
- Include source links inline and collected on the sources page
- Code examples should be practical and runnable where possible
- Write for someone technically capable but new to the topic
