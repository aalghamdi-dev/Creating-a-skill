# Source Map: Major Trusted Scientific Databases

This reference provides detailed guidance on each source the skill can search, including coverage, search method, and caveats.

Search methods below name generic actions (`web_search`, `web_fetch`, `bash_tool`+`curl`). Substitute your environment's equivalent tools where names differ — the URLs and API parameters are what matter and are environment-agnostic.

## Biomedical / Clinical

### PubMed
- **Coverage**: 36M+ biomedical citations from MEDLINE, life science journals, and online books (NIH/NLM)
- **Search method**:
  - `web_search` for an initial sweep
  - `web_fetch` on `https://pubmed.ncbi.nlm.nih.gov/?term=QUERY` for direct search results
  - `bash_tool` with `curl` (or `web_fetch` if shell network access is restricted) using NCBI E-utilities API: `https://eutils.ncbi.nlm.nih.gov/entrez/eutils/esearch.fcgi?db=pubmed&term=QUERY&retmax=20&retmode=json`
- **Caveats**: Many entries are abstract-only; use PMC for full text. MeSH terms improve precision.

### Europe PMC
- **Coverage**: 40M+ biomedical publications, with open-access full text for many
- **Search method**: `web_fetch` on `https://europepmc.org/search?query=QUERY` or API: `https://www.ebi.ac.uk/europepmc/webservices/rest/search?query=QUERY&format=json`
- **Caveats**: Overlaps with PubMed but includes more European sources and full-text content

### Cochrane Library
- **Coverage**: Systematic reviews, meta-analyses, and controlled trials registers (gold standard for evidence-based medicine)
- **Search method**: `web_fetch` (or a browser tool if the search UI is JS-heavy) on `https://www.cochranelibrary.com/search?searchTerm=QUERY`
- **Caveats**: Some content is paywalled; abstracts are usually free. Results are already high-quality evidence syntheses.

### ClinicalTrials.gov
- **Coverage**: Registered clinical trials (500K+ studies worldwide)
- **Search method**: `web_fetch` on `https://clinicaltrials.gov/search?term=QUERY` or the modern API v2: `https://clinicaltrials.gov/api/v2/studies?query.term=QUERY&pageSize=20&format=json` (the legacy v1 `/api/query/` endpoint was retired in 2024 — do not use it)
- **Caveats**: Includes registered but not-yet-published trials. Results may be ongoing, completed, or terminated. Useful filters include `filter.overallStatus` (e.g. `RECRUITING`) and `query.cond` for condition-specific search.

### WHO ICTRP
- **Coverage**: International Clinical Trials Registry Platform — aggregates trial registries worldwide
- **Search method**: `web_fetch` (or a browser tool) on `https://trialsearch.who.int/`
- **Caveats**: Overlaps with ClinicalTrials.gov but includes non-US registries

### PROSPERO
- **Coverage**: International prospective register of systematic review protocols (health/social care), run by the University of York
- **Search method**: `web_fetch` on `https://www.crd.york.ac.uk/prospero/` search
- **Caveats**: Only covers protocols that were registered before the review was conducted; useful to check whether a systematic review on the topic is already underway, and to compare a completed review's reported methods against its original protocol

## Broad Scholarly Metadata

### Semantic Scholar
- **Coverage**: 200M+ papers across all fields, with AI-powered relevance and citation analysis
- **Search method**: `web_search`, or API: `https://api.semanticscholar.org/graph/v1/paper/search?query=QUERY&limit=20&fields=title,authors,year,abstract,citationCount,journal,externalIds`
- **Caveats**: Excellent for citation counts and AI relevance ranking; may miss very recent or niche papers. Unauthenticated requests share a common rate-limit pool; if you have an API key, send it as the `x-api-key` header for more consistent throughput.

### OpenAlex
- **Coverage**: 250M+ works across all disciplines (open replacement for Microsoft Academic Graph)
- **Search method**: API: `https://api.openalex.org/works?search=QUERY&per-page=20`
- **Caveats**: Very broad coverage; metadata quality varies. Good for citation tracking and institutional analysis. Add `&mailto=you@example.com` to get routed to a faster, more polite pool.

### Crossref
- **Coverage**: 150M+ DOI-registered works
- **Search method**: API: `https://api.crossref.org/works?query=QUERY&rows=20`
- **Caveats**: Metadata only (no abstracts); best for DOI resolution and bibliographic data verification. Also the canonical source for retraction data (see Research Integrity Checks below).

### CORE
- **Coverage**: World's largest aggregator of open-access research papers, harvested from repositories and journals
- **Search method**: `web_fetch` on `https://core.ac.uk/search?q=QUERY`, or API (requires free API key): `https://api.core.ac.uk/v3/search/works?q=QUERY`
- **Caveats**: Good for finding a free full-text copy of an otherwise paywalled paper; metadata quality varies by source repository.

## Preprint Servers (non-peer-reviewed — always label)

### medRxiv
- **Coverage**: Health sciences preprints
- **Search method**: `web_fetch` on `https://www.medrxiv.org/search/QUERY` or API: `https://api.medrxiv.org/details/medrxiv/QUERY`
- **Caveats**: Not peer-reviewed, not clinical guidance. Clearly label as preprint.

### bioRxiv
- **Coverage**: Biology preprints
- **Search method**: `web_fetch` on `https://www.biorxiv.org/search/QUERY` or API: `https://api.biorxiv.org/details/biorxiv/QUERY`
- **Caveats**: Same as medRxiv — not peer-reviewed

### arXiv
- **Coverage**: Physics, mathematics, CS, statistics, quantitative biology, quantitative finance, electrical engineering
- **Search method**: API: `http://export.arxiv.org/api/query?search_query=all:QUERY&max_results=20`
- **Caveats**: Most CS and physics papers appear here first. Not peer-reviewed.

### SSRN
- **Coverage**: Social science, humanities, law, economics preprints and working papers
- **Search method**: `web_fetch` (or a browser tool) on `https://papers.ssrn.com/sol3/results.cfm?txtKey_Words=QUERY`
- **Caveats**: Mix of peer-reviewed and working papers; check each paper's status

### Research Square
- **Coverage**: Multidisciplinary preprints (Springer Nature)
- **Search method**: `web_fetch` on `https://www.researchsquare.com/search?query=QUERY`
- **Caveats**: Often preprints of papers later published in Springer Nature journals

## Guidelines & Reviews

### NICE (National Institute for Health and Care Excellence)
- **Coverage**: UK clinical guidelines and evidence reviews
- **Search method**: `web_fetch` on `https://www.nice.org.uk/search?q=QUERY`
- **Caveats**: UK-focused but internationally referenced

### WHO Guidelines
- **Coverage**: Global health guidelines and recommendations
- **Search method**: `web_fetch` on `https://www.who.int/publications?query=QUERY`
- **Caveats**: Some guidelines are PDF-only

### CDC / NIH
- **Coverage**: US public health and clinical guidance
- **Search method**: `web_search` or `web_fetch` on respective sites
- **Caveats**: Mix of guidelines, factsheets, and research summaries

## Research Integrity Checks

Use these whenever a study is about to be cited as evidence, not just when something looks suspicious — retractions are not always obvious from the abstract page alone.

### Retraction Watch Database
- **Coverage**: 60,000+ retractions, corrections, and expressions of concern, maintained by Crossref (acquired from the Center for Scientific Integrity in 2023) and updated on business days
- **Search method**: `web_fetch` on `https://retractiondatabase.org/` (requires at least one search field filled — it no longer allows a blank browse-all query); also check the site's "Hijacked Journal Checker" for journal-spoofing scams
- **Caveats**: Best-effort, community-augmented dataset — treat as strong evidence of a retraction if found, but absence from the database is not proof a paper wasn't retracted

### Crossref retraction filter (programmatic check)
- **Coverage**: Same underlying retraction data as above, queryable by DOI
- **Search method**: API: `https://api.crossref.org/works?filter=update-type:retraction,doi:DOI` or browse all retractions with `https://api.crossref.org/v1/works?filter=update-type:retraction`
- **Caveats**: Fastest way to check a single known DOI programmatically

### DOAJ (Directory of Open Access Journals)
- **Coverage**: Curated index of journals vetted against open-access and editorial-integrity criteria
- **Search method**: `web_fetch` on `https://doaj.org/search/journals?source=%7B%22query%22%3A%7B%22query_string%22%3A%7B%22query%22%3A%22QUERY%22%7D%7D%7D` or simpler: search `https://doaj.org/search/articles` for the article title
- **Caveats**: A journal *not* being listed doesn't automatically mean it's predatory (many legitimate journals aren't indexed), but it's a useful signal to combine with other checks before treating an unfamiliar open-access venue as equivalent to an established peer-reviewed journal

## Google Scholar
- **Coverage**: Broadest academic search engine (includes publisher sites, repositories, etc.)
- **Search method**: `web_search`, or a browser/computer-use tool if available — there is no public API
- **Caveats**: Inconsistent metadata; includes non-peer-reviewed sources; may rate-limit automated sessions. Use as a fallback, not a primary source.

## Full Text Access Routes

### PubMed Central (PMC)
- Free full-text archive of biomedical/life sciences journal literature
- Check for `PMCID` in PubMed records; access via `https://www.ncbi.nlm.nih.gov/pmc/articles/PMCID/`

### Publisher Open Access
- Many journals offer open access (e.g., PLOS, BMC, Frontiers, MDPI)
- Use `web_fetch` on the publisher page to check access status

### Unpaywall (via API)
- Checks for free legal full-text versions
- API: `https://api.unpaywall.org/v2/DOI?email=example@example.com` (a real contact email is required by Unpaywall's usage policy)
- Returns OA status and best OA location URL

### CORE (via API)
- Aggregates full text harvested from institutional and subject repositories worldwide
- API: `https://api.core.ac.uk/v3/search/works?q=DOI:DOI` (requires a free API key)
- Good second-line check when Unpaywall doesn't find a copy
