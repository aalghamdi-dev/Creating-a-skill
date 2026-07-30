# Testing: scientific-study-retriever skill

This document records a manual test of the `scientific-study-retriever` Claude Skill (v1.1) run in claude.ai.

## Test setup

- **Skill version tested:** 1.1
- **Environment:** claude.ai, skill invoked via `/scientific-study-retriever`
- **Test type:** end-to-end, single query, broad/open-ended research question (stress test for the skill's clarification and synthesis behavior rather than a narrow single-study lookup)

## Test case: broad interdisciplinary trend question

**Prompt:**
> How much our knowledge is evolving about the Nanotechnology in the last decade?

### Step 1 — Clarification behavior

Nanotechnology spans multiple fast-moving subfields (medicine, energy/materials, electronics, environmental applications) that move at different speeds. Per the skill's **Clarification Guidelines**, this counts as genuine domain ambiguity that would change the search results, so the skill correctly paused and asked which area to focus on before searching, rather than guessing or asking multiple questions at once:

> "Nanotechnology is a huge field — medicine, energy/materials, electronics, environmental applications all move at different speeds. Want me to focus on one of these, or give you a general overview across the field?"

The user selected **"General overview (all areas)."**

**Result:** ✅ Pass — clarified only where it mattered, asked a single question, offered a sensible default option ("general overview"), and proceeded immediately after the answer.

### Step 2 — Synthesis output

Because the request was an open-ended trend question rather than "find me candidate studies," the skill correctly interpreted this as calling for an **Evidence Summary** (per SKILL.md's synthesis output format) rather than a selection table + study cards. It pulled from multiple bibliometric analyses and named a mix of PubMed/PMC, Springer, and ACS Publications sources, producing a synthesized narrative covering:

1. **Publication volume growth** — exponential growth across nano subfields since ~2017–2019, China dominant in output volume, citing per-year growth rates from named bibliometric studies
2. **Nanomedicine clinical translation** — mRNA lipid-nanoparticle COVID vaccines as the decade's visible success, contrasted with a stated 50–80 total globally approved nanomedicines by 2025 against thousands of published candidates
3. **Nanocrystals/quantum dots** — matured from lab technique to the 2023 Nobel Prize in Chemistry, now extending into quantum computing applications
4. **Materials/energy** — EV battery nanotechnology R&D correlated with national investment (r=0.734), China ~50% of output
5. **Forward-looking note** — a 2025 *Nano Letters* anniversary roadmap on AI-guided materials discovery as the field's likely next phase

The response closed by offering to go deeper into any one thread with full study cards — correctly holding back full study-card detail (title/authors/DOI/etc. per the extraction schema) for a broad synthesis question, and offering it as a next step rather than dumping it unprompted.

**Result:** ✅ Pass — appropriate output format for the question type, multiple independent sources triangulated, offered a logical next step instead of overloading a broad question with maximal detail.

## Fact-checking the output

To confirm the skill wasn't fabricating or misreporting numbers, two of its more specific, checkable claims were independently re-verified against primary sources after the test:

| Claim in skill output | Verification | Result |
|---|---|---|
| "Only an estimated 50–80 nanomedicines had achieved global regulatory approval by 2025" | Matches a 2025 PMC review, *"Bridging the Gap: The Role of Advanced Formulation Strategies in the Clinical Translation of Nanoparticle-Based Drug Delivery Systems"*, which states the same 50–80 figure verbatim | ✅ Confirmed |
| "China producing over 70% of the literature" in nanoparticle cancer therapeutics, ~15 papers/year growth accelerating after 2017 | Matches a 2026 bibliometric study in *Discover Nano* on nanotechnology in nasopharyngeal carcinoma theranostics: 72.59% of publications from China, growth rate of 14.85 papers/year (R²=0.913), inflection after 2017 | ✅ Confirmed, with one caveat below |

**Caveat found:** the underlying 72.59%/14.85-papers-per-year statistic comes from a bibliometric study of one specific nano-oncology niche (nasopharyngeal carcinoma theranostics), not "nanoparticle-based cancer therapeutics" as a whole. The skill's phrasing generalizes a narrow-niche statistic to a broader category. The number itself is accurately reported and directionally consistent with other cancer-nanotech bibliometric studies checked (which show China leading at 50–73% depending on subfield), but the framing slightly overstates how broadly that specific figure applies. Not a fabrication, but a scope-generalization worth being careful about when citing niche bibliometric papers as evidence for a broader field-wide trend.

## Overall assessment

| Category | Result |
|---|---|
| Clarification behavior (asks only when it changes results) | ✅ Pass |
| Correct output format for question type (synthesis vs. study cards) | ✅ Pass |
| Multi-source triangulation | ✅ Pass |
| Source diversity (PubMed/PMC, Springer, ACS Publications) | ✅ Pass |
| Factual accuracy of cited statistics (spot-checked) | ✅ Pass, with one scope-generalization caveat |
| Appropriate restraint (didn't over-deliver detail for a broad question) | ✅ Pass |

**Conclusion:** The skill performed as designed on an intentionally broad, ambiguous, interdisciplinary query — it asked exactly one clarifying question where the domain genuinely mattered, then produced a well-sourced, appropriately-scoped synthesis rather than either refusing the broad question or dumping an overwhelming amount of unstructured detail. The one issue found (a niche statistic generalized to a broader category) is a minor precision issue rather than a factual error, and is worth flagging as something to watch for when the skill synthesizes across bibliometric studies with narrow scopes.

## Suggested follow-up tests

- A narrow, single-study lookup by DOI/PMID to confirm the deep-retrieval study-card path and field extraction
- A query with 5+ plausible candidates to confirm the selection-table + user-choice step actually triggers
- A query touching a retracted or corrected paper to confirm the Retraction Watch/Crossref integrity check fires
- A query in a non-biomedical domain (e.g. CS/arXiv or economics/SSRN) to confirm domain-specific routing works outside the biomedical default

reference: *https://claude.ai/share/a5692b1b-903f-4c4e-bc0e-9cc2cb8b5d4e*
