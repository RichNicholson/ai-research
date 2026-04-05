---
description: "Deep-dive research on a topic, producing MkDocs pages with source links"
user_invocable: true
argument: "The topic to research"
---

# Research Skill

You are researching the topic: **$ARGUMENTS**

## Process

### 1. Plan the research

Break the topic into 3-5 research angles. Typical angles include:
- Official documentation and core concepts
- Practical usage, getting started, best practices
- Community opinions, gotchas, real-world experience (Reddit, HN, forums)
- Comparisons with alternatives
- Advanced patterns and edge cases

Write the plan briefly, then proceed.

### 2. Dispatch researchers in parallel

Launch 3-5 researcher agents simultaneously, each covering a different angle. Each agent should:
- Search the web for their specific angle
- Follow promising leads to get depth
- Return structured notes with source URLs

Use `subagent_type: "researcher"` and `run_in_background: true` for all agents.

### 3. Synthesise into MkDocs pages

Once all researchers return, create a topic directory under `docs/` and write these pages:

1. **`index.md`** — Overview: what it is, why it matters, key concepts, how it works at a high level
2. **`guide.md`** — Practical guide: getting started, common patterns, best practices, code examples
3. **`community.md`** — Community insights: real-world opinions, gotchas, tips from practitioners, common mistakes
4. **`sources.md`** — All sources referenced, organised by category, with URLs

### 4. Update navigation

Edit `mkdocs.yml` to add the new topic section to the `nav`.

### 5. Update the home page

Edit `docs/index.md` to list the new topic with a brief description.

### 6. Build and verify

Run `mkdocs build` to verify everything compiles.

## Writing guidelines

- Write for a technically capable reader who is new to this specific topic
- Lead with the "so what" — why should someone care about this topic?
- Use admonitions (`!!! tip`, `!!! warning`, `!!! note`, `!!! info`) for callouts
- Include inline source links like `[source](url)` and also collect them on the sources page
- Code examples should be practical and runnable where possible
- Use tables for comparisons
- Keep sections scannable — short paragraphs, clear headings, bullet points
- Don't pad or waffle — every sentence should add value

## Slug convention

Convert the topic to a URL-friendly slug for the directory name. For example:
- "Kubernetes networking" → `kubernetes-networking`
- "React Server Components" → `react-server-components`
