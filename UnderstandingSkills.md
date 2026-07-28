# Three Claude Skills, Compared

A quick guide to what `pdf`, `prompt-engineering`, and `webapp-testing` each do, when Claude reaches for them, and the problem they solve.

---

## 1. `pdf` — PDF Processing

**Problem it solves:** PDFs are hard to work with programmatically — text, tables, images, and forms are locked inside a format not meant for editing. Without this skill, Claude would have to improvise fragile, one-off code every time.

**What it does:** Gives Claude a tested toolkit (pypdf, pdfplumber, reportlab, OCR tools) for reading, creating, and modifying PDFs — extracting text/tables, merging or splitting files, rotating pages, adding watermarks, filling forms, encrypting files, and OCR'ing scanned documents into searchable text.

**When it's triggered:** Any time a `.pdf` file is mentioned or needs to be produced — e.g. "combine these three PDFs," "pull the table out of this scanned invoice," "fill out this form," or "turn this report into a PDF."

**Type:** A *capability* skill — teaches Claude reliable techniques for a well-defined file format.

---

## 2. `prompt-engineering` — Prompting Best Practices

**Problem it solves:** People (and Claude itself, when building AI-powered features) often write vague or poorly structured prompts, leading to inconsistent or low-quality model output. This skill closes that gap.

**What it does:** Encodes Anthropic's own best practices for writing effective prompts — being specific and detailed, using positive/negative examples, encouraging step-by-step reasoning, using clear structure (like XML tags), and specifying format or length constraints.

**When it's triggered:** When a user wants help *writing or improving a prompt* for Claude or another LLM — e.g. "how do I get better output from this prompt," "help me design a system prompt for my chatbot," or when Claude is building an AI-powered artifact that itself calls the Claude API.

**Type:** A *knowledge* skill — it's a reference of techniques, not a tool integration.

---

## 3. `webapp-testing` — Automated Web App Testing

**Problem it solves:** After Claude builds or edits a web app, there's no built-in way to verify it actually works — clicks succeed, forms submit, pages render correctly. Without this, bugs slip through untested.

**What it does:** Lets Claude interact with a running web app the way a user would — clicking through flows, filling in forms, checking that the right things happen — to catch bugs before handing the app back.

**When it's triggered:** After Claude builds or modifies a web app / UI and needs to confirm it behaves correctly, or when a user says "test this," "make sure the login flow works," or "check this app for bugs."

**Type:** An *action* skill — it drives real interaction with software, not just static analysis.

---

## The Pattern

| Skill | Solves | Triggered by |
|---|---|---|
| `pdf` | Working with a rigid file format | Any PDF task |
| `prompt-engineering` | Weak/inconsistent prompts | Prompt-writing help |
| `webapp-testing` | Unverified web app behavior | Post-build QA |

Together they show the three flavors of skills: **file-format capability**, **domain knowledge**, and **hands-on action** — each activated only when the task calls for it, keeping Claude efficient the rest of the time.
