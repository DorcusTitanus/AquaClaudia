---
name: Research Gap Finder
description: AI pipeline that cross-references academic research against patent coverage to surface cross-domain opportunity gaps, filtered to Jefe's skillset
type: project
---

# Project: Research Gap Finder
**Status:** Triage / Future Build
**Tagged:** python, data-pipeline, research, apis, content-worthy
**Estimated effort:** Small-medium (1-2 weeks for MVP)
**Impact:** Optional upside (opportunity surfacing) + guaranteed content value + useful research tool regardless of outcome

---

## Problem Statement

Academic research moves faster than patent coverage in several domains, and polymaths can see cross-domain gaps that specialists miss. But finding those gaps manually is impossible — the volume of papers and patents is too large. A filtered, automated pipeline can surface "high research activity + thin patent coverage" opportunities in domains Jefe actually knows: EdTech, accessibility, creative workflow tools, audio engineering, homelab/secops automation.

**Critical scope boundary:** This project STOPS at opportunity surfacing. It does NOT attempt to write or file patents. See "Why not patents" below.

---

## Why Not Patents (Scope Discipline)

Jefe's friend patented and manufactured a product but couldn't get distribution. Big players waited for the patent to expire then copied it. This is the rule, not the exception:

- Patents are a right to *exclude*, not a right to *sell*
- Distribution is the moat; patents are a speed bump for incumbents
- Lone-inventor patents mostly feed trolls or get ignored
- Filing costs $735+ USPTO minimum, $8-15k with a real attorney
- "No patent exists" is a notoriously unreliable negative — foreign filings, delayed publication (~18 months), terminology drift all obscure coverage
- AI-drafted claims currently produce patents that are rejected or granted so narrowly they're worthless

**What the pipeline IS good for:**
- Research reading lists in Jefe's domains
- Trend spotting (citation velocity, emerging clusters)
- Content ideas for polymathery/wilsonidt
- Rare legitimate opportunities → consult a patent attorney (most do free 30-min consults) before spending a dollar
- The pipeline itself is excellent content: "I built an AI tool to find research gaps"

---

## Data Sources

### Research (all free APIs)
- **OpenAlex** — 240M+ works, authors, institutions, citation graph. Free API. The best general starting point.
- **PubMed E-utilities** — biomedical, free API
- **arXiv API** — physics, CS, math preprints
- **CrossRef** — DOI metadata for everything
- **Semantic Scholar API** — good citation intelligence, free tier

### Patents (free/accessible)
- **PatentsView** (USPTO) — real API, good bulk data
- **Lens.org** — free, excellent search UI, some API access
- **Google Patents** — searchable UI, no official API (scrape carefully if needed)
- **EPO OPS** — European patents, free API with registration

### Explicitly skipped
- Google Scholar (no API, actively blocks scraping)
- Paywalled databases (Scopus, Web of Science)

---

## Architecture

### Stack
- **Language:** Python 3
- **Storage:** SQLite (MVP) → Postgres if scale demands
- **Orchestration:** Plain scripts + cron, no Airflow overhead
- **LLM:** Claude API for clustering, summarization, gap scoring
- **Frontend:** None for MVP — CLI + markdown reports. Optionally add a simple Flask dashboard later.

### Pipeline Stages

```
1. INGEST
   ├── Pull papers from OpenAlex/PubMed/arXiv filtered to Jefe's domains
   ├── Pull patent metadata from PatentsView/Lens
   └── Store raw in SQLite

2. NORMALIZE
   ├── Extract keywords, abstracts, authors, dates
   ├── Deduplicate (DOI, arXiv ID)
   └── Compute citation velocity (Δ citations / time)

3. CLUSTER
   ├── Embed papers with sentence-transformers or Claude
   ├── Cluster by topic (HDBSCAN or k-means)
   └── Label clusters with Claude

4. CROSS-REFERENCE
   ├── For each paper cluster, search patent corpus for overlap
   ├── Score patent coverage density (papers-per-patent ratio)
   └── Flag high-research + low-patent clusters

5. RANK
   ├── Filter to Jefe's skillset domains
   ├── Apply skill-match scoring (rubric below)
   └── Rank by (research heat × skill match × patent thinness)

6. REPORT
   ├── Generate weekly markdown report
   ├── Top 10 opportunities with one-paragraph summaries
   └── Link to source papers + nearest patents for manual review
```

### Skill-Match Rubric
Score each opportunity 0-5 on how well it fits Jefe's domain knowledge:
- **5:** Directly in wheelhouse (Photoshop/creative workflow, EdTech, accessibility, audio engineering, homelab)
- **3:** Adjacent — could be learned in weeks (general CV/ML tooling, web apps, hardware prototyping)
- **1:** Distant — would need months of study (deep biomed, semiconductors, pharma)
- **0:** Skip (regulated industries, specialized domains Jefe doesn't touch)

Only 3+ makes the report.

---

## Domains to Filter Into

Jefe's realistic opportunity zones:

1. **Educational technology / learning analytics** — UDL, accessibility tooling, assessment automation, cognitive tracking
2. **Accessibility tech** — CVC-OEI background, WCAG tooling, alt-text automation, captioning workflows
3. **Creative workflow tools** — Photoshop plugin space, DAW/audio tools, video editing automation
4. **Audio engineering** — Midas M32 automation, virtual soundcheck, measurement tools
5. **Homelab / secops automation** — self-hosted infrastructure, LLM-in-the-loop tooling, memory systems
6. **Camera/video hardware** — LANC remotes, BMPC workflows, on-set tooling

Deliberately excluded: biomed, pharma, semiconductors, materials science, finance.

---

## MVP Scope (Week 1)

- [ ] SQLite schema (papers, patents, clusters, opportunities)
- [ ] OpenAlex ingestion script (filter to domain keywords)
- [ ] PatentsView ingestion script (same filter)
- [ ] Basic deduplication
- [ ] Citation velocity computation
- [ ] Claude-powered cluster labeling
- [ ] Simple rank-and-report script → weekly markdown digest
- [ ] Manual review: does the output look useful?

## Phase 2 (Week 2+)

- [ ] Multi-source ingestion (PubMed, arXiv, CrossRef)
- [ ] Better clustering (embeddings + HDBSCAN)
- [ ] Patent coverage density scoring
- [ ] Skill-match rubric automation via Claude
- [ ] Lens.org integration for international patents
- [ ] Historical trend tracking (is this cluster heating up or cooling down?)

## Phase 3 (Nice to Have)

- [ ] Simple web dashboard (Flask + HTMX) for browsing opportunities
- [ ] Saved searches / watchlists
- [ ] Email digest
- [ ] Export report as polymathery.com blog draft

---

## Success Criteria

The project is successful if ANY of these happen:

1. **Reading list value:** It surfaces 5+ papers per week that Jefe actually reads and learns from
2. **Content value:** It generates at least one polymathery/wilsonidt post about the pipeline itself or about an opportunity it surfaced
3. **Opportunity value:** It finds one genuine cross-domain gap that warrants a free attorney consult (bonus if that consult leads anywhere)
4. **Tooling value:** The ingestion + clustering code is reusable for other research projects (Jazz OER literature review, MULTI 211 pedagogy research, etc.)

Even hitting only #1 and #4 makes it worth building. Anything beyond that is upside.

---

## Open Questions

1. **Rate limits** — OpenAlex is generous, PatentsView less so. May need caching aggressive enough to run overnight.
2. **Domain keyword list** — needs to be curated carefully. Too broad = noise; too narrow = miss adjacencies. Probably iterate weekly.
3. **Clustering algorithm** — HDBSCAN vs k-means vs LLM-based grouping. Start simple, measure.
4. **Patent coverage scoring** — what's the right threshold for "thin coverage"? Needs empirical tuning once data is flowing.
5. **Ethical consideration** — if the pipeline surfaces something valuable, is the right move to build it, write about it, or license it? Probably depends on the specific opportunity. Decide case-by-case.

---

## Relationship to Other Projects

- **Content pipeline:** The pipeline itself, and anything it surfaces, is potential polymathery/wilsonidt content
- **LibreTexts OER on Personal AI Workflows:** This tool IS a personal AI workflow — good case study material
- **Cognitive tracking (acta-cogitationis):** Could be interesting to cross-reference what papers Jefe actually reads against what the pipeline recommends
