# Example: Chaining Two Skills into One Workflow

## The idea

A single skill usually does one job well. Chaining two skills means the output of one becomes the input to the next, so Claude completes a multi-stage task in one request instead of the user manually copying results between separate asks.

**Example chain:** `scientific-study-retriever` → `docx`

1. `scientific-study-retriever` finds and structures the evidence (search, dedupe, rank, extract study details)
2. `docx` takes that structured output and turns it into a formatted, shareable Word document (headings, citations, a references section)

This is a natural pairing: research skills produce well-structured text, and document skills are good at *presenting* well-structured text — but neither does the other's job.

## Example scenario

**User prompt:**
> "Find me the best recent evidence on intermittent fasting and insulin sensitivity, and put it in a Word doc I can send to my nutritionist."

### How the chain is supposed to work

```
User prompt
   │
   ▼
scientific-study-retriever
   │  - parses domain (biomedical) and PICO elements
   │  - searches PubMed, Europe PMC, Cochrane
   │  - dedupes, ranks, checks retraction status
   │  - produces: selection table + study cards
   ▼
docx
   │  - takes the study cards as structured input
   │  - builds a formatted report: title page, summary,
   │    per-study sections, references list
   │  - exports scientific-study-retriever.docx
   ▼
File delivered to user
```

The handoff point is the study cards: `scientific-study-retriever` already outputs them in a consistent Markdown-like schema (title, authors, DOI, study type, findings, etc.), which is exactly the kind of structured content the `docx` skill is designed to convert into headings, tables, and a references section rather than a single wall of text.

## Hypothetical test walkthrough

*(Written as if run, for illustration — not an actual execution log.)*

**Step 1 — retrieval.** `scientific-study-retriever` returns 4 candidate studies: two RCTs, one systematic review, one cohort study, all from the last 5 years, all peer-reviewed and unretracted. Since there are fewer than 5 candidates, it skips the "let the user choose" step and retrieves all four in depth automatically, per its own workflow rule.

**Step 2 — handoff.** The four study cards are passed as the source content for the `docx` skill, along with the framing note "literature summary for a nutritionist, not a technical audience."

**Step 3 — document generation.** `docx` produces a 3-page Word document:
- Title page: "Intermittent Fasting and Insulin Sensitivity: Evidence Summary"
- One-paragraph summary of overall findings
- Four sections, one per study, each with a plain-language findings summary and the technical details in a collapsed/secondary position
- A references section with DOI links

**Step 4 — delivery.** The `.docx` file is presented to the user with a one-line summary, per the "no long post-ambles" rule for file delivery.

### Hypothetical result

The chain works end to end, but two rough edges show up:

1. The systematic review's "Population" field (aggregated across 12 included trials, worded densely) gets pasted into the doc almost verbatim, producing a paragraph a non-specialist nutritionist would find hard to parse.
2. The doc doesn't distinguish RCT evidence from cohort evidence visually — both look equally authoritative on the page, even though the skill's own evidence hierarchy (from `study-details-checklist.md`) ranks them differently.

## Peer review

*(A second reviewer — human or a fresh Claude session — looks at the hypothetical output above.)*

**Reviewer feedback:**

> "The chain technically works, but it's just relaying `scientific-study-retriever`'s internal, researcher-facing fields straight into the document. Two issues:
>
> 1. **Audience mismatch.** The user asked for something to send to a nutritionist — that's a semi-technical but not academic audience. The systematic review's population description is written for someone doing evidence synthesis, not for a quick read. The `docx` skill isn't rewriting it in a way that would help.
> 2. **Lost evidence-hierarchy signal.** `scientific-study-retriever` already computes a study-type ranking. That's valuable context — 'this is a systematic review, weight it more heavily than the single cohort study' — but it gets flattened once everything is dropped into the document as visually equal sections. The reader has no way to tell which finding is stronger.
>
> Neither of these is a bug in either skill individually. They're both doing exactly what they're supposed to do in isolation. The problem is the **handoff** — nobody is telling `docx` to reformat for the stated audience or to preserve the ranking signal from the other skill."

## Improvements made in response

Two changes to the workflow, not to either skill individually:

1. **Explicit audience instruction at the handoff.** When passing study cards to `docx`, include the user's stated audience ("nutritionist, not a researcher") as part of the brief, and ask for the technical fields (sample sizes, CIs, p-values) to be kept but visually secondary — e.g., in a smaller-font "study details" block under a plain-language summary sentence, rather than as the lead content.

2. **Carry the evidence hierarchy across the handoff.** Have `scientific-study-retriever`'s study-type ranking (systematic review > RCT > cohort > case-control, from its own evidence hierarchy) become a visible label in the document — a short badge or heading tag per section ("Systematic Review — strongest evidence available here") — so the ranking work the first skill already did isn't thrown away by the second.

### Revised chain

```
User prompt
   │
   ▼
scientific-study-retriever
   │  - same as before, plus: attaches an explicit
   │    evidence-strength label to each study card
   ▼
docx  (now given an explicit audience + formatting brief)
   │  - plain-language summary line per study, technical
   │    fields kept but visually secondary
   │  - evidence-strength label rendered as a heading tag
   ▼
File delivered to user
```

## Takeaway

The interesting failures in a chained-skill workflow usually aren't inside either skill — they're in the **handoff**: information one skill computed (like an evidence ranking) or context the user gave (like an audience) that doesn't automatically survive into the next skill's input unless it's explicitly carried forward. Peer review is most useful for catching exactly that kind of gap, since it's invisible when looking at either skill's `SKILL.md` in isolation.
