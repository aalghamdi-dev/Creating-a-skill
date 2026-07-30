---
name: scientific-study-retriever
description: "Master skill for retrieving scientific studies with full details from major trusted sources. Use when the user asks to find, search, retrieve, look up, or compare scientific studies, research papers, clinical trials, systematic reviews, meta-analyses, academic literature, evidence, or DOI lookups. Covers PubMed, Cochrane, ClinicalTrials.gov, Semantic Scholar, arXiv, medRxiv, bioRxiv, Europe PMC, OpenAlex, Crossref, Retraction Watch, DOAJ, and more. Presents candidate studies for the user to choose from before deep retrieval."
license: MIT
metadata:
  author: Albaraa
  version: '1.1'
---

# Scientific Study Retriever

## When to Use This Skill

Use this skill when the user wants to:

- Find or search for scientific studies, research papers, or academic literature
- Retrieve details about specific studies (abstracts, methods, outcomes, funding)
- Compare evidence across studies on a topic
- Look up studies by DOI, PMID, title, or author
- Find clinical trials, systematic reviews, or meta-analyses
- Conduct evidence searches for clinical, policy, or research questions
- Get preprints or working papers from preprint servers
- Check whether a study has been retracted or corrected

## When NOT to Use This Skill

- Answering general knowledge questions (use web search directly)
- Finding news articles or blog posts (use a plain web search)
- Answering questions answerable with 1-2 quick searches
- Job searches or product reviews

## Quick-Start Workflow

1. **Parse the request**: Identify the domain (biomedical, physics, CS, social science, etc.), the question, and any constraints (date range, study type, sample size, etc.).
2. **Formulate a search strategy**: Build a PICO/PECO or keyword query. See `references/search-workflows.md` for query-building guidance.
3. **Search broadly first**: Run a general web search across academic sources (Semantic Scholar, OpenAlex, Google Scholar results, etc. often surface in ordinary web search results).
4. **Search domain-specific databases**: Run targeted searches or API calls on the databases most relevant to the user's domain. See `references/source-map.md` for which sources to use and how.
5. **Deduplicate**: Remove duplicates by DOI, PMID, arXiv ID, or title similarity.
6. **Rank candidates**: Sort by relevance, evidence level, recency, source quality, sample size, and directness to the question.
7. **Present a selection table**: Show the user a concise comparison table of top candidates.
8. **Let the user choose**: If 5+ candidates, ask the user which studies to retrieve in depth (use an interactive question tool if available, otherwise ask directly in text). If fewer than 5, retrieve details directly.
9. **Deep retrieval**: For selected studies, fetch full details from primary source pages. See `references/study-details-checklist.md` for the extraction schema.
10. **Check integrity status**: For any study you're about to cite, check retraction/correction status (see Quality & Citation Rules below).
11. **Present results**: Output structured study cards with all details and inline citations to primary source URLs.

## Source Priority by Domain

### Biomedical / Clinical
- **PubMed** — primary biomedical literature database (NIH/NLM)
- **Europe PMC** — open access full-text biomedical articles
- **Cochrane Library** — systematic reviews and meta-analyses (gold standard for evidence)
- **ClinicalTrials.gov** — registered clinical trials
- **WHO ICTRP** — international clinical trial registry
- **PROSPERO** — international registry of systematic review protocols (check if a review is already underway/registered)

### Broad Scholarly Metadata
- **Semantic Scholar** — AI-powered academic search across all fields
- **OpenAlex** — open catalog of global research output
- **Crossref** — DOI registry, metadata for published works
- **CORE** — largest aggregator of open-access full text; useful when a paywalled record needs a free copy

### Preprints (clearly label as non-peer-reviewed)
- **medRxiv** — health sciences preprints
- **bioRxiv** — biology preprints
- **arXiv** — physics, math, CS, statistics preprints
- **SSRN** — social science and humanities preprints
- **Research Square** — multidisciplinary preprints

### Guidelines & Reviews
- **Cochrane** — systematic reviews
- **NICE / WHO / CDC / NIH** — clinical practice guidelines

### Research Integrity Checks
- **Retraction Watch Database** (retractiondatabase.org, now Crossref-maintained) — check retraction/correction status
- **Crossref API retraction filter** — programmatic retraction lookup by DOI
- **DOAJ (Directory of Open Access Journals)** — verify a journal is a legitimate, vetted open-access venue before treating a paper from it as credible; useful for flagging likely predatory journals

### Paywalled / Full Text
- Check **PubMed Central (PMC)** for free full-text versions
- Check **CORE** or **Unpaywall** for a free legal copy
- Use publisher pages for abstracts
- Never imply access to paid full text unless actually available
- Note open-access status explicitly

### Google Scholar
- No public API; only reachable via a general web search or a browser/computer-use tool if one is available in your environment
- Use as a fallback when other sources yield insufficient results
- Do not present it as an API-based source

## Tool Mapping

These map to tools available in this environment. If your environment exposes different tools (e.g. a dedicated academic-search tool, a browser/computer-use tool), prefer those where they clearly fit; otherwise fall back to the mappings below.

| Task | Tool |
|------|------|
| Broad web/academic sweep | `web_search` |
| Fetch a study page, abstract, or JSON API endpoint | `web_fetch` |
| Search databases requiring JS rendering or login walls (e.g. Google Scholar, Cochrane advanced search) | a browser/computer-use tool if available, otherwise fall back to `web_search` + `web_fetch` |
| Ask user to select between candidates | an interactive selection tool if available, otherwise ask directly in your reply |
| Programmatic API queries (PubMed E-utilities, OpenAlex, Crossref, etc.) | `bash_tool` with `curl`, or `web_fetch` directly on the API URL if outbound network access from bash is restricted in your environment |

**Note on network access**: some sandboxed environments allowlist only a handful of domains for shell-level network calls. If `curl` fails or is blocked for a given API domain, try `web_fetch` on the exact same URL instead — most of these APIs return plain JSON/XML that a page-fetch tool can read just as well as a shell command.

## Required Study Details

For each study retrieved in depth, extract these fields (see `references/study-details-checklist.md` for full schema):

- **Title**
- **Authors** (full list if ≤10, else first 3 + et al.)
- **Year / publication date**
- **Venue** (journal, conference, or preprint server)
- **Identifiers**: DOI, PMID, arXiv ID, or other
- **Source URL** (direct link to the study page)
- **Study type** (RCT, cohort, case-control, systematic review, meta-analysis, etc.)
- **Population / sample** (size, demographics, setting)
- **Intervention / exposure**
- **Comparator** (if applicable)
- **Outcomes / endpoints**
- **Main findings** (effect sizes, confidence intervals, p-values)
- **Limitations** (as stated by authors)
- **Funding source / conflicts of interest**
- **Citation count** (if available)
- **Peer-review status** (peer-reviewed, preprint, retracted)
- **Open access status**

## Output Formats

### Selection Table (presented before deep retrieval)

| # | Title | Year | Study Type | Source | Relevance |
|---|-------|------|------------|--------|-----------|
| 1 | Short title | 2024 | RCT | PubMed | High |

### Study Card (deep retrieval output)

```
## Study Title
- **Authors:** Author A, Author B, et al.
- **Published:** 2024, Journal Name
- **DOI:** 10.xxxx/xxxxx
- **Source:** [PubMed](https://...)
- **Study Type:** Randomized controlled trial
- **Population:** N=500, adults aged 18-65, outpatient setting
- **Intervention:** Drug X 10mg daily
- **Comparator:** Placebo
- **Primary Outcome:** Reduction in symptom score (Mean diff: -3.2, 95% CI: -4.1 to -2.3, p<0.001)
- **Key Findings:** Brief summary...
- **Limitations:** Single-center, short follow-up
- **Funding:** Industry-sponsored (PharmaCo Inc.)
- **Citations:** 42 (Semantic Scholar)
- **Status:** Peer-reviewed, open access, not retracted (checked Retraction Watch/Crossref)
```

### Evidence Summary (when comparing multiple studies)

When the user asks for a synthesis across multiple retrieved studies, provide a brief narrative summary highlighting:
- Consistency of findings across studies
- Strength of evidence (using the evidence hierarchy)
- Key gaps or contradictions
- Applicability to the user's question

## Quality & Citation Rules

- Cite every factual claim with an inline link to the primary source URL
- Distinguish between abstract-only and full-text access
- **Check retraction/correction status** for any study before citing it as evidence — use the Retraction Watch Database or the Crossref API retraction filter (see `references/source-map.md`). Flag retractions, expressions of concern, or corrections explicitly.
- Clearly label preprints as non-peer-reviewed
- If a journal is unfamiliar, check DOAJ (or note it's not indexed there) before treating it as a vetted venue — flag likely predatory journals rather than presenting them as equivalent to peer-reviewed sources
- Do not overstate causality — use the study's own language (association vs causation)
- If a source is unavailable or behind a paywall, state this clearly

## Clarification Guidelines

Only ask the user to clarify when it would change the search results:

- **Domain ambiguity**: e.g., "aspirin study" — biomedical vs. chemistry synthesis
- **Missing PICO elements**: if population, intervention, or outcome is unclear
- **Date range preference**: if the user might want only recent or historical studies
- **Evidence type preference**: if they want only RCTs, reviews, or all types

If the query is clear enough to produce useful results, search directly without asking.

## Reference Files

- `references/source-map.md` — Detailed guide to each source: what it covers, how to search it, and caveats
- `references/study-details-checklist.md` — Full extraction schema with evidence hierarchy
- `references/search-workflows.md` — Query building (PICO/PECO), deduplication, ranking, and fallback strategies
