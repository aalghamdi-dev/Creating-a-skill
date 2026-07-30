# Search Workflows: Query Building, Deduplication, Ranking & Fallbacks

## PICO / PECO Query Framework

Translate the user's question into a structured search before querying databases.

### PICO (for intervention questions)
| Element | Description | Example |
|---------|-------------|---------|
| **P** — Population | Who/what is studied? | Adults with type 2 diabetes |
| **I** — Intervention | What is being tested? | Metformin 1000mg BID |
| **C** — Comparator | What is it compared to? | Placebo or sulfonylurea |
| **O** — Outcome | What is measured? | HbA1c reduction |

### PECO (for exposure questions)
| Element | Description | Example |
|---------|-------------|---------|
| **P** — Population | Who is studied? | Adults aged 30-60 |
| **E** — Exposure | What exposure is assessed? | Air pollution PM2.5 |
| **C** — Comparator | Unexposed group | Low-pollution areas |
| **O** — Outcome | What is measured? | Cardiovascular events |

### Building the Search String

1. Extract PICO/PECO elements from the user's question
2. Add synonyms and MeSH terms (for biomedical queries):
   - Example: "type 2 diabetes" OR "T2DM" OR "non-insulin-dependent diabetes" OR MeSH: "Diabetes Mellitus, Type 2"
3. Combine with Boolean operators:
   - `AND` for different PICO elements
   - `OR` for synonyms within the same element
   - Use parentheses to group: `(metformin OR glucophage) AND (type 2 diabetes OR T2DM)`
4. Add filters if the user specifies:
   - Date range: `AND ("2020"[Date - Publication] : "2024"[Date - Publication])`
   - Study type: `AND randomized controlled trial[pt]`
   - Language: `AND English[la]`

## Search Execution Strategy

### Step 1: Broad Academic Index
- Use `web_search` with the PICO search string for an initial sweep
- This surfaces Semantic Scholar, OpenAlex, publisher pages, and other indexed sources across all fields
- Aim for 10-20 results

### Step 2: Domain-Specific Databases
Based on the domain, run targeted searches on the most relevant databases (see `source-map.md`):

**Biomedical/Health:**
- PubMed E-utilities API via `bash_tool` + `curl` (or `web_fetch` on the same URL if shell network access is restricted) for structured results
- Europe PMC API for open-access full text
- Cochrane for systematic reviews
- PROSPERO to check if a systematic review is already registered on the topic

**Physics/Math/CS:**
- arXiv API for preprints
- Semantic Scholar API for peer-reviewed

**Social Sciences/Humanities:**
- SSRN via `web_fetch`
- Semantic Scholar API

**Clinical Trials:**
- ClinicalTrials.gov API v2 for registered trials
- WHO ICTRP for international coverage

### Step 3: Supplementary Searches
- `web_search` for studies mentioned in news or policy documents
- A browser/computer-use tool on Google Scholar as a fallback, if available; otherwise `web_search`
- `web_fetch` on specific journal sites if a study is known but not indexed in the above

## Deduplication

After collecting results from multiple sources:

1. **Match by DOI** — most reliable; if two results share a DOI, they are the same study
2. **Match by PMID** — for PubMed-sourced results
3. **Match by arXiv ID** — for preprint servers
4. **Title similarity** — if no identifier match, compare normalized titles (lowercase, stripped punctuation); if >90% similar and year matches, treat as duplicate
5. **Preprint vs. published** — if a preprint and a published paper share the same title/authors, keep the published version as primary and note the preprint as a prior version

## Ranking Algorithm

Sort deduplicated candidates using this scoring approach:

```
Score = (relevance × 0.30) + (evidence_level × 0.25) + (recency × 0.15)
        + (source_quality × 0.15) + (sample_size × 0.10) + (directness × 0.05)
```

Where:
- **relevance** (0-1): PICO match quality — does the study directly address the population, intervention, and outcome?
- **evidence_level** (0-1): systematic review = 1.0, RCT = 0.85, cohort = 0.65, case-control = 0.5, cross-sectional = 0.35, case report = 0.2
- **recency** (0-1): published within 2 years = 1.0, 3-5 years = 0.7, 5-10 years = 0.4, >10 years = 0.2
- **source_quality** (0-1): Cochrane = 1.0, PubMed indexed = 0.8, Semantic Scholar = 0.7, preprint = 0.4
- **sample_size** (0-1): normalized within study type (larger = higher)
- **directness** (0-1): primary outcome matches question = 1.0, secondary = 0.6, surrogate endpoint = 0.4

Present the top 8-10 candidates in the selection table.

## Fallback Strategy

If initial searches yield insufficient results:

1. **Broaden the query**: Remove the most restrictive PICO element (usually the comparator) and retry
2. **Use synonyms**: Expand with additional synonyms and MeSH terms
3. **Try different databases**: If PubMed yields little, try Europe PMC, OpenAlex, or Semantic Scholar API
4. **Relax filters**: Remove date or study-type restrictions
5. **Google Scholar**: Use a browser/computer-use tool (if available) or `web_search` as a last resort for grey literature or niche topics
6. **Citation chasing**: If a relevant systematic review is found, check its included studies for additional primary research
7. **Related articles**: Use PubMed's "Related Articles" feature (via `web_fetch` on the article page) to find similar studies
8. **CORE / Unpaywall**: If a promising result is paywalled and full text is needed for extraction, check CORE or Unpaywall for a legal open-access copy before giving up on it

## Selection Prompt Template

When presenting candidates to the user (via an interactive selection tool if available, otherwise directly in your reply):

```
I found [N] relevant studies on [topic]. Here are the top candidates:

[Selection Table]

Which studies would you like me to retrieve in full detail?

Options:
- "Study 1, 3, and 5" — retrieve those specific ones
- "All of them" — retrieve details for all candidates
- "The most relevant" — I'll pick the top 2-3 by evidence level and relevance
- (Free text) — describe what you're looking for and I'll refine
```

## Deep Retrieval Workflow

For each selected study:

1. **Identify the primary source URL** (DOI link, PubMed page, arXiv page, etc.)
2. **Fetch the study page** using `web_fetch` to extract the full details per `study-details-checklist.md`
3. **Check for open access full text** — look for PMCID, Unpaywall API, CORE, or publisher OA page
4. **Check integrity status** — query the Retraction Watch Database or Crossref's retraction filter for the DOI before treating the study as valid evidence
5. **Verify key data** — cross-check abstract text against the fetched page content
6. **Compile the study card** using the output template in SKILL.md
7. **Add inline citations** — every factual statement in the card links to the primary source URL

## Output Quality Checks

Before presenting results to the user:

- [ ] Every study has at least: title, authors, year, DOI/ID, source URL, study type, main findings
- [ ] Preprints are clearly labeled as non-peer-reviewed
- [ ] Retraction/correction status has actually been checked (Retraction Watch or Crossref), not assumed
- [ ] Retracted studies are flagged
- [ ] Unfamiliar open-access journals are checked against DOAJ before being treated as vetted venues
- [ ] Funding/conflicts are reported
- [ ] Effect sizes include confidence intervals where available
- [ ] All URLs are valid and point to the primary source (not aggregator sites)
- [ ] Citations use inline markdown links per the citation format rules
