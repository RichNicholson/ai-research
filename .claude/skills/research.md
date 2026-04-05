---
description: "Deep-dive research on a topic, producing MkDocs pages with source links"
user_invocable: true
argument: "The topic to research"
---

# Research Skill

You are researching the topic: **$ARGUMENTS**

## Process

### 1. Plan the research

Briefly state the topic and any specific sub-questions, then proceed to dispatch researchers covering all required angles (see below).

### 2. Dispatch researchers in parallel

Launch 5-8 researcher agents simultaneously. Each agent should:
- Search the web for their specific angle
- Follow promising leads to get depth
- Return structured notes with source URLs

Use `subagent_type: "researcher"` and `run_in_background: true` for all agents.

#### Required research angles

Every topic must be researched from **all** of these angles (adapt the specific queries to the topic):

| # | Angle | What to search for |
|---|---|---|
| 1 | **Official sources** | Official documentation, project websites, specs, RFCs. The authoritative "how it works" material. |
| 2 | **Practical usage** | Getting started guides, tutorials, best practices, common patterns, code examples. |
| 3 | **Community experience** | Reddit (r/programming, topic-specific subs), Hacker News, developer forums. Real opinions, success/failure stories, gotchas. |
| 4 | **Industry analysis** | Thoughtworks Technology Radar, Gartner, InfoQ, ThoughtWorks blog, Martin Fowler's site, major tech company engineering blogs. What do industry voices say about maturity, adoption, direction of travel? |
| 5 | **Authoritative guides** | Seminal blog posts, official guides from major vendors (Anthropic, Google, Microsoft, etc.), influential articles that shaped how people think about this topic. |
| 6 | **Academic research** | arXiv papers, survey papers, Google Scholar results. Taxonomies, benchmarks, research findings. Especially useful for establishing rigorous definitions and classifications. |
| 7 | **Future direction** | Where is this heading? What are the open problems? What are major companies investing in? Conference talks, roadmaps, predictions from credible sources. |

Not every angle will be equally relevant to every topic — some topics are more academic, others more practical. But the researcher for each angle should still try and report back what they found (even if it's "not much academic work on this yet").

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
