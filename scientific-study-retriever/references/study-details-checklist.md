# Study Details Checklist & Evidence Hierarchy

## Full Extraction Schema

For each study retrieved in depth, attempt to extract all of the following fields. If a field is not available, mark it as "Not reported" rather than omitting it.

### Bibliographic Information
| Field | Description | Example |
|-------|-------------|---------|
| Title | Full title of the study | "Effect of Drug X on..." |
| Authors | Full author list (first 3 + et al. if >10) | Smith J, Doe A, Lee B, et al. |
| Year | Publication year | 2024 |
| Publication date | Full date if available | 2024-03-15 |
| Venue | Journal, conference, or preprint server | New England Journal of Medicine |
| Volume/Issue/Pages | Bibliographic details | 390(11):1024-1035 |
| DOI | Digital Object Identifier | 10.1056/NEJMoa2400123 |
| PMID | PubMed ID (if biomedical) | 38451234 |
| PMCID | PubMed Central ID (if open access full text) | PMC10987654 |
| arXiv ID | arXiv identifier (if preprint) | 2401.12345 |
| Source URL | Direct link to the study page | https://doi.org/10.1056/NEJMoa2400123 |

### Study Design
| Field | Description | Example |
|-------|-------------|---------|
| Study type | RCT, cohort, case-control, cross-sectional, systematic review, meta-analysis, case report, etc. | Double-blind randomized controlled trial |
| Evidence level | See evidence hierarchy below | Level I (RCT) |
| Registration | Trial registration number if applicable | NCT04567890 |

### Population & Setting
| Field | Description | Example |
|-------|-------------|---------|
| Sample size | Total N | N=1,250 |
| Participant demographics | Age, sex, ethnicity if reported | Adults 18-65, 52% female |
| Setting | Clinical, community, lab, etc. | Multicenter, 12 hospitals |
| Inclusion criteria | Who was eligible | Adults with confirmed diagnosis |
| Exclusion criteria | Who was excluded | Pregnancy, prior treatment |
| Follow-up duration | For longitudinal studies | 12 months |

### Intervention & Comparator
| Field | Description | Example |
|-------|-------------|---------|
| Intervention | What was tested | Drug X 10mg oral daily |
| Comparator | Control group | Placebo or Drug Y 5mg |
| Duration of intervention | How long the treatment lasted | 24 weeks |

### Outcomes
| Field | Description | Example |
|-------|-------------|---------|
| Primary outcome | Main endpoint | Change in HbA1c |
| Primary result | Effect size, CI, p-value | Mean diff: -0.8%, 95% CI: -1.1 to -0.5, p<0.001 |
| Secondary outcomes | Additional endpoints | Weight change, adverse events |
| Secondary results | Effect sizes and significance | Weight: -2.1 kg, p=0.03 |
| Adverse events | Safety profile | 12% vs 8% (intervention vs control) |

### Quality & Validity
| Field | Description | Example |
|-------|-------------|---------|
| Key limitations | As stated by authors | Single-center, short follow-up |
| Risk of bias | If assessed | Low risk (Cochrane tool) |
| Funding source | Sponsor | PharmaCo Inc. |
| Conflicts of interest | Author disclosures | 3 authors report consulting fees |
| Citation count | Number of citations (if available) | 42 (Semantic Scholar) |

### Access & Status
| Field | Description | Example |
|-------|-------------|---------|
| Peer-review status | Peer-reviewed, preprint, retracted | Peer-reviewed |
| Open access status | Open, closed, hybrid | Open access (CC BY) |
| Retraction status | Checked against Retraction Watch Database / Crossref retraction filter; note if retracted or has an expression of concern | Not retracted (checked Retraction Watch, 2026-07-30) |
| Journal legitimacy | For unfamiliar open-access venues, note DOAJ listing status | Listed in DOAJ |
| Full text available | Yes/no and where | Yes — PMC |

---

## Evidence Hierarchy

Use this hierarchy to classify and rank studies:

### Level I — Strong Evidence
1. **Systematic reviews / meta-analyses** of RCTs (Cochrane reviews are gold standard)
2. **Randomized controlled trials (RCTs)** — especially large, multi-center, double-blind
3. **Meta-analyses** of cohort studies

### Level II — Moderate Evidence
4. **Cohort studies** (prospective)
5. **Case-control studies** (well-designed)
6. **Systematic reviews** of observational studies

### Level III — Limited Evidence
7. **Cross-sectional studies**
8. **Case series / case reports**
9. **Expert opinion / consensus statements**

### Special Categories
- **Systematic reviews** always rank above individual studies of the same type
- **Preprints** should be ranked below their peer-reviewed equivalents but noted for recency
- **Clinical practice guidelines** synthesize evidence but are not primary studies
- **Retracted studies** should be flagged and excluded from evidence synthesis

### Ranking Priority for Presentation

When sorting candidates for the selection table, weight in this order:
1. **Relevance** to the user's question (PICO match)
2. **Evidence level** (higher is better)
3. **Recency** (newer is better, unless user asks for historical)
4. **Source quality** (Cochrane > PubMed indexed > preprint)
5. **Sample size** (larger is better, within same study type)
6. **Directness** (primary outcome matching the question > secondary outcome)
