---
description: "Health-check the wiki: structural integrity, content gaps, quality, and suggest next questions"
---

# /wiki-lint

Health-check the wiki, fix issues, and **suggest what to investigate next**. Lint is not just maintenance — it's also the wiki's self-awareness about what it knows and doesn't know.

## Process

### Step 1: Read the full wiki state

1. Read `wiki/CLAUDE.md` — refresh on conventions and data quality requirements
2. Read `wiki/index.md`
3. List all markdown files in `wiki/` (all subdirectories)
4. Read every page's frontmatter and body

### Step 2: Run checks

**Structural issues:**
- [ ] **Broken wikilinks**: `[[links]]` pointing to pages that don't exist
- [ ] **Orphan pages**: Pages not listed in `index.md`
- [ ] **One-way links**: A links to B but B doesn't link back
- [ ] **Bare links**: Cross-references without a why-clause (just `- [[page]]` with no annotation)
- [ ] **Frontmatter issues**: Missing `type`, `created`, or `updated`

**Content issues:**
- [ ] **Uncovered datasets**: ETL datasets in `apps/web/public/data/` with no wiki page
- [ ] **Missing era pages**: Government eras from `governments.json` without wiki pages
- [ ] **Missing entity/event pages**: Referenced in wikilinks or body text but lacking own page
- [ ] **Empty sections**: Placeholder text like "To be created"
- [ ] **Stale claims**: Pages with old `updated` dates that newer data may supersede

**Data quality (mandatory per schema):**
- [ ] **Splice discontinuities**: Spliced dataset pages that don't document the splice point
- [ ] **Coverage gaps**: Datasets ending before present that don't note what they're missing
- [ ] **Overlap divergence**: Overlapping datasets from different sources without comparison

**Quality issues:**
- [ ] **Thin pages**: Content pages with fewer than 100 words body
- [ ] **Isolated pages**: Pages with fewer than 3 outbound `[[wikilinks]]`
- [ ] **Zero synthesis pages**: Red flag if 5+ datasets exist
- [ ] **Zero contradictions surfaced**: Suspicious if multiple sources exist
- [ ] **Missing data citations**: Dataset pages without specific observations (dates, values)
- [ ] **Missing temporal context**: Event pages without government attribution
- [ ] **Pre-compilation gaps**: Event/era pages that mention a dataset but don't include specific data points from it (e.g., an event page links to [[datasets/inflation]] but doesn't say what inflation was during that event)

**Schema health:**
- [ ] **Page type coverage**: All types in CLAUDE.md actually used?
- [ ] **Convention drift**: Patterns in pages not documented in schema?
- [ ] **Overview staleness**: Does `overview.md` reflect current wiki state?

### Step 3: Present the report

```
## Wiki Lint Report

### Critical (must fix)
- ...

### Important (content gaps)
- ...

### Quality
- ...

### Data quality
- ...

### Schema health
- ...

### Stats
- Content pages: N
- Total wikilinks: N (bare: M)
- Avg links/page: N.N
- Datasets covered: N/M
- Eras covered: N/M
- Synthesis pages: N
- Question pages: N
- Contradiction sections: N
- Pre-compiled cross-temporal facts: estimated N/M
```

### Step 4: Check the backlog pipeline

Read `wiki/backlog.md` and report pipeline status:

```
### Backlog pipeline
- Discovered (awaiting approval): N entries
- Approved (awaiting ETL build): M entries
  - <name>: <one-line desc> — approved on <date>
- Built (awaiting /wiki:ingest): K entries
  - <name>: <one-line desc> — ETL exists, run /wiki:ingest <name>
- Ingested (complete): J entries
- Stale stubs: <any stub pages whose backlog entry was never approved/built>
```

Flag:
- **Approved entries sitting >7 days** without being built → remind user
- **Built entries not yet ingested** → these are one `/wiki:ingest` command away from enriching the wiki
- **Stale stubs** → pages with `status: stub` that aren't connected to a backlog entry

### Step 5: Fix issues

Ask the user which severity levels to fix. Then apply fixes.

### Step 6: Suggest next questions

**This is a Karpathy requirement.** Lint doesn't just report problems — it suggests what the wiki should investigate next.

Read the backlog, the wiki state, and the gaps to generate:

```
### Suggested next

#### Highest-value action right now
<single recommendation: the one thing that would most enrich the wiki>

#### Questions to investigate (/wiki:query)
1. <question the wiki's existing data could answer but hasn't been asked>
2. <question connecting datasets not yet linked by a synthesis page>
3. <question motivated by a gap or contradiction discovered in this lint>

#### Sources to ingest (/wiki:ingest)
1. <highest-value item from backlog "Built" section, if any>
2. <un-ingested ETL dataset that would enrich the most existing pages>

#### Topics to discover (/wiki:discover)
1. <topic where the wiki's answer would be weakest — worth researching online>

#### Backlog status
- Pipeline: N discovered → M approved → K built → J ingested
```

### Step 6: Update log

Full format with every page touched.

### Step 7: Propose schema improvements

If lint revealed conventions not in the schema, or schema rules nobody follows, propose updates.

### Step 8: Commit

```bash
cd wiki && git add -A && git commit -m "lint: fix N issues — [key fixes]"
```
