# llm-wiki-os

A generic operating system for LLM-maintained wikis. Based on [Andrej Karpathy's LLM Wiki pattern](https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f).

## What this is

Four Claude Code skills that operate on any markdown wiki that follows the LLM Wiki pattern. The skills are domain-agnostic — they read the wiki's `CLAUDE.md` schema to learn the domain-specific conventions, page types, and philosophical framework.

## Skills

| Skill | What it does |
|-------|-------------|
| `/wiki:ingest` | Process a source (dataset, article, URL) into the wiki, dispersing knowledge across multiple pages |
| `/wiki:query` | Ask a question, answer from wiki first, disperse findings, file the answer back |
| `/wiki:lint` | Health-check: structural integrity, content gaps, quality, and suggest next questions |
| `/wiki:discover` | The compounding loop: answer → identify gaps → research online → propose new data sources |

## How it works

The skills are the **operating system**. The wiki's `CLAUDE.md` is the **configuration**. The wiki pages are the **data**.

```
llm-wiki-os (this repo)     — HOW to operate (process, behaviour)
    reads ↓
wiki/CLAUDE.md               — WHAT the wiki is (schema, conventions, frameworks)
    operates on ↓
wiki/**/*.md                  — THE KNOWLEDGE (pages, links, synthesis)
```

The skills never reference any domain-specific content. They say "read the schema and do what it says." You could point them at:
- A UK government accountability wiki (uk-legalize-wiki)
- A personal health wiki
- A startup due diligence wiki
- A book-reading companion wiki

Same skills. Different `CLAUDE.md`. Different content.

## Installation

Symlink the commands directory into your project's `.claude/commands/`:

```bash
# From your project root:
ln -s /path/to/llm-wiki-os/commands .claude/commands/wiki
```

Then create a wiki repo with a `CLAUDE.md` schema and symlink it as `wiki/` in your project.

## The compounding loop

```
/wiki:discover → finds gaps → proposes data sources → updates backlog
    ↓ (human approves)
/wiki:ingest   → processes source → disperses across pages → fills stubs
    ↓
/wiki:query    → answers from wiki → disperses findings → reveals new gaps
    ↓
/wiki:lint     → health-check → suggests next questions → reads backlog
    ↓
/wiki:discover → cycle continues, wiki compounds
```

Each skill reads and writes `wiki/backlog.md` — the shared state that makes the loop actually loop.

## Key principles baked into the skills

1. **Information dispersal** — every operation enriches many pages, not just one
2. **Pre-compilation** — write facts into pages during ingest so future queries don't re-derive from raw data
3. **No bare links** — every `[[wikilink]]` needs a why-clause (associative trails)
4. **Single-answer analysis is always wrong** — multiple lenses, multiple interpretations
5. **Source epistemology** — question who produced the data and why
6. **Schema co-evolution** — skills propose schema improvements when patterns emerge
7. **Suggested next** — every skill ends by recommending the highest-value next action

## Credits

Pattern: [Andrej Karpathy](https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f)
