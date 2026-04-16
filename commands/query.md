---
description: "Research a question using the wiki, disperse findings across pages, and identify knowledge gaps"
argument-hint: "<question in natural language>"
---

# /wiki-query

Ask a question of the wiki. The answer isn't just a response in chat — it's an operation that:
1. **Answers** from the wiki pages (not raw JSON — if you need raw JSON, the wiki has failed)
2. **Disperses** findings across every relevant existing page
3. **Creates** synthesis pages aggressively
4. **Files** the answer back into the wiki so explorations compound
5. **Suggests** follow-up questions and next actions

## Core Principle: The Wiki Should Already Know

Karpathy: *"The cross-references are already there. The contradictions have already been flagged. The synthesis already reflects everything you've read."*

If you have to go read raw `apps/web/public/data/*.json` files to answer a query, that's a **wiki failure** — the relevant facts should already be compiled into wiki pages. When this happens:
1. Answer the question anyway (use the raw data)
2. But then **write the answer INTO the wiki pages** so next time it's already there
3. Flag this as a gap: "The wiki didn't have this pre-compiled. I've now added it."

## Input

`$ARGUMENTS` — a question in natural language.

## Process

### Step 1: Search the wiki FIRST

1. Read `wiki/index.md` to identify relevant pages
2. Read every page that could contribute to the answer
3. **Try to answer entirely from wiki pages.** Can you? If yes, great — the wiki is working.
4. If not, note what's missing and proceed to Step 2

### Step 2: Fall back to raw data (only if wiki is insufficient)

If the wiki pages don't contain specific numbers needed:
1. Read the relevant `apps/web/public/data/<name>.json` files
2. Extract the specific observations
3. **Flag this**: "I had to read raw data because the wiki didn't have [X] pre-compiled."

### Step 3: Synthesize and answer

Present the answer:

```
## Answer: <question>

<The factual answer with citations to wiki pages>

**Answered from**: wiki pages only / wiki + raw data fallback
**Confidence**: complete / partial (explain what's missing)
```

### Step 4: Disperse findings

**The answer must enrich the wiki, not just live in chat.**

For every fact you discovered or derived:
- Write it into every relevant existing page as **natural prose with context**
- NOT as a bare link — as a sentence or paragraph that a browser would find naturally
- Update era pages with temporal context
- Update event pages with cross-dataset facts

**Create synthesis pages aggressively:**
If the answer connects 2+ datasets → create `synthesis/<slug>.md`. The threshold is LOW. Synthesis pages are the wiki's highest-value output.

**File the answer:**
If the question required significant research, create a `questions/<slug>.md` page. The question page preserves the full analysis so it doesn't disappear into chat history.

**Cross-reference rules:**
Every new `[[wikilink]]` must have a why-clause. No bare links.

### Step 5: Handle contradictions

If data contradicts existing wiki claims → `### Disputed` section with both claims cited.

### Step 6: Pre-compile adjacent facts

While you're looking at the data for this question, **pre-compile nearby facts** that someone might ask next. If you pulled inflation data for 1999, also note inflation for 1998, 2000, 2001 — write those into the relevant era and event pages. Don't wait for someone to ask.

### Step 7: Update overview

If `wiki/overview.md` exists and this answer adds significant new knowledge, update it.

### Step 8: Identify knowledge gaps and check the backlog

Read `wiki/backlog.md`. Are any of the gaps already in the pipeline?

```
### Knowledge gaps
- <gap>: The wiki doesn't have [X]. Would need [source].
  → Backlog status: <not in backlog / discovered / approved / built>
- <gap>: I had to read raw JSON for [Y] — now added to wiki, but coverage is thin.
```

If a gap is significant AND not already in the backlog: *"This would be a good topic for `/wiki:discover`."*
If a gap IS in the backlog as "built": *"This data exists — run `/wiki:ingest <name>` to fill this gap."*

### Step 9: Update index and log

Full log format — list every page touched and what was added to each.

### Step 10: Suggest what to do next

Read the backlog to make the suggestion actionable:

```
### Suggested next
- **Follow-up questions**: <2-3 questions this answer raises>
- **Gaps to discover**: <topics for /wiki:discover — not already in backlog>
- **Ready to ingest**: <backlog entries in "built" that would help>
- **Related queries**: <adjacent questions the wiki could now answer>
- **Backlog**: N discovered, M approved, K built, J ingested
```

### Step 11: Commit

```bash
cd wiki && git add -A && git commit -m "query: <question summary>"
```

## The dispersal test

Before finishing: **if someone browsing any updated page stumbles onto it tomorrow, will they find the new information naturally?** They shouldn't need to know this question was asked. The knowledge should be woven in as if it was always there.

## The pre-compilation test

Before finishing: **could a future query on a related topic be answered from wiki pages alone, without touching raw JSON?** If not, pre-compile more facts.
