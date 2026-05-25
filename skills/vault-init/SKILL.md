---
name: vault-init
description: >
  Scaffold a new knowledge base vault with standard wiki structure, CLAUDE.md, and all
  required skills. Run this in an empty directory to create a complete vault. Trigger on:
  /vault-init, "create a new vault", "scaffold a vault", "set up a knowledge base".
---

# Vault Init

Scaffold a complete knowledge base vault in the current working directory. This skill writes
every file the vault needs — CLAUDE.md, wiki stubs, and all project-scoped skills — so the
vault is immediately usable after running.

Read this whole skill before starting work.

---

## Absolute constraints

- Only scaffold into the current working directory.
- Never overwrite existing files — if CLAUDE.md or wiki/ already exists, stop and warn the user.
- Substitute the user-provided topic wherever `{TOPIC}` appears in templates below.
- Substitute today's date wherever `{DATE}` appears.
- Do not set any file's `status` to `approved`.

---

## Step 1 — Gather inputs

Ask the user for two things before doing anything:

1. **Vault topic** — one sentence describing what this vault will capture.
   Example: "cybersecurity research and threat intelligence", "customer discovery interviews for a B2B SaaS product", "fintech regulatory landscape in the EU".

2. **Confirm location** — show the absolute path of the current working directory and ask the user to confirm this is correct.

Do not write any files until the user confirms both.

---

## Step 2 — Check for existing vault

Before creating anything, check whether `CLAUDE.md` or `wiki/` already exist in the current directory. If either exists:

- Do not write any files.
- Tell the user what was found and stop.

---

## Step 3 — Create directory structure

Create the following directories (all relative to the current working directory):

```
raw/
wiki/
wiki/open-questions/
.claude/
.claude/skills/
.claude/skills/wiki-ingest/
.claude/skills/wiki-query/
.claude/skills/wiki-lint/
.claude/skills/wiki-output/
.claude/skills/obsidian-markdown/
.claude/skills/obsidian-markdown/references/
.claude/skills/obsidian-cli/
.claude/skills/defuddle/
```

---

## Step 4 — Write CLAUDE.md

Write the following content to `CLAUDE.md` at the vault root.
Replace `{TOPIC}` with the user's vault topic and `{DATE}` with today's date.

--- CLAUDE.md CONTENT START ---
# Vault — Schema

## Purpose

Personal knowledge base on {TOPIC}.
Scope: research, synthesis, and consulting artifacts.

---

## Directory Structure

```
vault/
├── CLAUDE.md          ← this file
├── raw/           ← raw, immutable materials — never modify
└── wiki/
    ├── index.md       ← master catalog
    ├── log.md         ← append-only operation log
    └── ...            ← subfolders emerge from content
```

---

## Available Skills

| Skill | When to use |
|-------|-------------|
| `/wiki-ingest` | User drops a file in `raw/` and wants the wiki updated |
| `/wiki-query` | Any question that should be answered from the wiki |
| `/wiki-lint` | Structural integrity + consistency check; run periodically or after bulk ingests |
| `/wiki-output` | Produce a consulting artifact (brief, one-pager, playbook, comparison, slide outline) from the wiki |
| `defuddle` | User provides a web URL — use instead of WebFetch for standard pages |
| `obsidian-cli` | Vault search, backlinks, note ops (requires Obsidian open) |
| `obsidian-markdown` | Writing or editing any wiki file |

---

## Wiki Page Types

| Type | What goes here |
|------|----------------|
| Tool | Capabilities, limits, pricing, integration patterns, adoption notes |
| Concept | Ideas and patterns |
| Scenario | Adoption playbooks and how-to guides |
| Case study | Real-world stories with outcome data |
| Comparison | Tool vs tool, approach vs approach |
| Synthesis | Cross-cutting trends, advisory frameworks, maturity models |

Folder names emerge from content — do not pre-create them.

---

## Ingest Workflow

Run `/wiki-ingest` when a file lands in `raw/`. The skill handles the full process.
Log entry format:

```
## YYYY-MM-DD — Ingest: <source filename>

- **Source:** `raw/<filename>`
- **Pages updated:** [[note-name|wiki/path/note.md]], ...
- **Pages created:** [[note-name|wiki/path/note.md]], ... (or "none")
- **Open questions logged:** [[YYYY-MM-DD-slug]], ... (or "none")
- **Contradictions found:** <brief description, or "none">
```

---

## Query Workflow

Run `/wiki-query` for any question that should be grounded in the wiki.
Log significant queries:

```
[QUERY] YYYY-MM-DD — <1-line summary of what was asked and what was synthesized>
```

---

## Output / Artifact Workflow

Run `/wiki-output` to produce a consulting artifact. The skill handles the full process.

Supported types: `brief`, `one-pager`, `playbook`, `comparison`, `slide-outline`, `custom`.
Artifacts are saved to `output-artifacts/` with `status: draft`.

The skill will ask for **audience** (`exec` / `practitioner` / `architect`) and **format** if
not provided.

```
[OUTPUT] YYYY-MM-DD — <artifact type and topic> — <wiki pages drawn from>
```

---

## Maintenance / Lint Workflow

Run `/wiki-lint` periodically (after bulk ingests, or at least monthly).

```
[LINT] YYYY-MM-DD — <N> issues found: <N> auto-fixed, <N> flagged for review, <N> concept gaps identified
```

---

## Hard Rules

- Never modify or delete anything in `raw/`.
- Never invent facts — use uncertainty markers (see below).
- Never silently resolve contradictions — record both positions.
- Never skip updating `wiki/log.md` after significant changes.
- Never set `status: approved` — only humans may. Valid statuses: `draft`, `active`, `stale`, `deprecated`.

---

## Conventions

**File names:** lowercase, hyphenated — `github-copilot.md`, `brownfield-adoption.md`.

**Frontmatter** (required on every wiki page):
```yaml
---
title: <human-readable title>
status: <draft | active | stale | deprecated>
updated: <YYYY-MM-DD>
---
```

**Internal links:** use wikilinks `[[note-name]]` or `[[note-name|Display Text]]` — never
relative markdown paths.

**Sources section:** every page closes with `## Sources` listing contributing files from
`raw/` as wikilinks: `[[filename|raw/filename.pdf]]`.

**Uncertainty markers** — use Obsidian callouts:

| Situation | Callout |
|-----------|---------|
| Claimed but not independently validated | `> [!note] Assumption` |
| Plausible but untested | `> [!tip] Hypothesis` |
| Something unknown needing research | `> [!question] Open Question` |
| Content missing but should exist | `> [!todo] Placeholder` |
| Two sources disagree | `> [!warning] Contradiction` |

**General:** cross-reference liberally with wikilinks; prefer dated claims over timeless ones;
mark vendor claims with *"claimed by vendor"* or *"as of YYYY-MM"*.
--- CLAUDE.md CONTENT END ---

---

## Step 5 — Write wiki/index.md

Write the following content to `wiki/index.md`.
Replace `{DATE}` with today's date.

--- wiki/index.md CONTENT START ---
---
title: Wiki Index
aliases:
  - Index
tags:
  - vault/meta
  - vault/index
date: {DATE}
---

# Wiki Index

Master catalog of all wiki pages. Updated on every ingest operation.

*Last updated: {DATE}*
*Total pages: 0*

---

## Open Questions

*(none yet)*
--- wiki/index.md CONTENT END ---

---

## Step 6 — Write wiki/log.md

Write the following content to `wiki/log.md`.
Replace `{DATE}` with today's date and `{TOPIC}` with the user's vault topic.

--- wiki/log.md CONTENT START ---
---
title: Vault Log
aliases:
  - Log
tags:
  - vault/meta
  - vault/log
date: {DATE}
---

# Log

Append-only chronological record of all vault operations.

> [!warning] Append-only
> Do not edit past entries. Append only.

---

[INIT] {DATE} — Vault scaffolded. Topic: {TOPIC}.
--- wiki/log.md CONTENT END ---

---

## Step 7 — Write skill files

Write each file below exactly as specified between its START and END delimiters.
Do not truncate or summarize the content — write every character verbatim.

---

### .claude/skills/wiki-ingest/SKILL.md

--- wiki-ingest SKILL.md CONTENT START ---
---
name: wiki-ingest
description: "Synthesize a raw source file into the wiki layer. Use this skill whenever the user runs /wiki-ingest <path>, drops a file in raw/ and wants it processed, says 'ingest this', 'process this source file', 'add this to the wiki', or asks Claude to extract insights from a raw document into the wiki. Always use this skill when the user references a file in raw/ and wants wiki pages updated."
---

# Wiki Ingest

The human has placed a source file in `raw/`. Your job is to synthesize its content into the `wiki/` layer — extracting insights, cross-referencing existing knowledge, marking uncertainty explicitly, and logging everything.

Read this whole skill before starting work. For all content creation and editing, follow the **obsidian-markdown** skill — this vault uses Obsidian Flavored Markdown throughout.

---

## Absolute constraints

- **Never modify or delete anything in `raw/`.** That directory is the immutable evidence layer.
- **Never invent facts.** If something is uncertain, use a marker (see below).
- **Never set `status: approved`.** A human must do that. You may use: `draft`, `active`, `stale`, `deprecated`.
- **Never silently resolve a contradiction.** When two sources disagree, record both positions and flag the conflict for human resolution.
- **Never hard-code wiki subfolder paths.** Always discover the current layout by reading `wiki/index.md` and globbing `wiki/` before placing new files.

---

## Step-by-step process

Use TodoWrite to track progress and mark each step done before moving on.

### 1. Validate the path

Confirm the provided path is inside the `raw/` directory and the file exists. If not, refuse and stop.

### 2. Read the source file

Read the file in full. Do not change it.

### 3. Map the wiki

Glob `wiki/` to discover the current structure. Read `wiki/index.md` in full. Identify which existing wiki pages are relevant and read every relevant page in full before editing anything.

### 4. Cross-check for contradictions

For every claim in the source, check whether any existing wiki page makes a conflicting claim. Note every contradiction.

### 5. Update wiki pages

**Page types:**

| Type | What goes here |
|------|----------------|
| Tool | Capabilities, limits, pricing, integration patterns, adoption notes |
| Concept | Ideas and patterns |
| Scenario | Adoption playbooks |
| Case study | Real-world stories with outcome data |
| Comparison | Tool vs tool, approach vs approach |
| Synthesis | Cross-cutting trends, advisory frameworks, maturity models |

Place new pages in the subfolder that matches their type. If a suitable subfolder doesn't exist yet, create one.

**Uncertainty markers:**

| Marker type | Callout | When |
|---|---|---|
| Assumption | `> [!note] Assumption` | Claimed but not independently validated |
| Hypothesis | `> [!tip] Hypothesis` | Plausible but untested |
| Open Question | `> [!question] Open Question` | Something unknown needing research |
| Placeholder | `> [!todo] Placeholder` | Content missing but should exist |
| Contradiction | `> [!warning] Contradiction` | Sources disagree |

**Internal links** — use wikilinks for all references between vault pages:

```
[[note-name]]
[[note-name|Display Text]]
[[note-name#Heading]]
```

Never use relative markdown paths for internal vault links.

**Frontmatter** — required on every wiki file:

```yaml
---
title: <human-readable title>
status: <draft | active | stale | deprecated>
updated: <YYYY-MM-DD>
---
```

**Sources section** — every wiki page must close with:

```markdown
## Sources

- [[filename|raw/path/filename.md]]
```

### 6. Create open question entries

For every Contradiction or Open Question callout, create a file in the open-questions subfolder named `YYYY-MM-DD-<brief-slug>.md` with the question/contradiction, its source, and links back to affected pages.

### 7. Update wiki/index.md

Add each new page to the appropriate section. Use the wikilink format already established in the index.

### 8. Append to wiki/log.md

```markdown
## YYYY-MM-DD — Ingest: <source filename>

- **Source:** `<path>`
- **Pages updated:** [[note-name|wiki/path/note.md]], ...
- **Pages created:** [[note-name|wiki/path/note.md]], ... (or "none")
- **Open questions logged:** [[YYYY-MM-DD-slug]], ... (or "none")
- **Contradictions found:** <brief description, or "none">
```

---

## Output to the user

1. Wiki pages updated (list with wikilinks)
2. Wiki pages created (list with wikilinks, or "none")
3. Contradictions found
4. Open questions logged
5. Close with: _"No document has been set to `approved` — human review required for status promotion."_
--- wiki-ingest SKILL.md CONTENT END ---

---

### .claude/skills/wiki-query/SKILL.md

Write the following content, replacing `{TOPIC}` with the user's vault topic.

--- wiki-query SKILL.md CONTENT START ---
---
name: wiki-query
description: >
  Answer any question about {TOPIC} using only content found in this repository. Use this
  skill whenever the user runs /wiki-query or asks any question that should be grounded in
  wiki content. Trigger on: "what does the wiki say about X", "according to our docs",
  "what have we captured about", or any question that should be answered from the repo rather
  than general knowledge.
---

# wiki-query

You are a knowledge retrieval agent for this vault. Your only job is to answer questions by
reading the repository — not by reasoning from general knowledge.

Every factual claim you make must cite a specific file path or be explicitly marked as an inference.

---

## Step 1 — Restate the question

Open with a one-sentence restatement of the question.

---

## Step 2 — Search in priority order

### 0. Start from the index

Read `wiki/index.md` first. Use it to identify which files are relevant before opening anything else.

### 1. Broad discovery

After scanning the index, run a vault-wide search for additional candidate files. Use backlinks to catch related notes keyword search would miss.

### 2. `wiki/` — synthesized knowledge, primary source

Read every file that looks relevant before concluding the answer is or isn't there.

### 3. `raw/` — primary source evidence (last resort)

Only read raw files if the wiki doesn't answer the question. Never modify them.

---

## Step 3 — Read before you write

Do not compose an answer from memory or filenames alone. Read the full content of every file you cite.

---

## Step 4 — Compose the answer

For every factual claim, append the source file path inline:

> Claim (`wiki/concepts/example.md`).

If a claim is your own inference: `[Assumption: this implies X, but it is not explicitly stated]`

If a claim is unresolved: `[Open Question: see wiki/open-questions/<slug>.md]`

**Never state a fact without either a citation or an explicit `[Assumption: ...]` marker.**

---

## Step 5 — Surface gaps

After the answer, add a short section (only if relevant):

```
## Gaps / open questions
- [what the repo doesn't cover]
```

If the question reveals a gap worth preserving, offer to create an entry in `wiki/open-questions/`. Only do this if the user confirms.

---

## Hard constraints

- Never state facts without a cited path or `[Assumption: ...]`
- Never modify `raw/` files
- Never set `status: approved` on any document
- Do not create or edit wiki pages as a side effect of answering
- If the answer is not in the repo, say so clearly
- Never assume a fixed wiki folder structure

---

## Output format

```
**Q (as understood):** [one-sentence restatement]

---

[Answer body with inline citations]

---

## Gaps / open questions
- [only if relevant]
```
--- wiki-query SKILL.md CONTENT END ---

---

### .claude/skills/wiki-lint/SKILL.md

--- wiki-lint SKILL.md CONTENT START ---
---
name: wiki-lint
description: >
  Run a structural integrity and consistency check on the wiki. Use this skill whenever the
  user runs /wiki-lint, asks to "check the wiki", "lint the wiki", "validate the wiki", "find
  orphan pages", "check for missing frontmatter", or "run maintenance". Also trigger when the
  user asks about wiki health, broken links, or consistency issues. Do NOT trigger for
  content/prose quality.
---

# wiki-lint

You are a wiki integrity auditor. Scan the `wiki/` layer for structural defects, fix what is
safe to auto-fix, flag what requires human judgement, and log a summary.

Read this whole skill before starting work.

---

## Absolute constraints

- **Never modify or delete anything in `raw/`.**
- **Never invent facts or edit prose content.** Lint touches structure only.
- **Never set `status: approved`.**
- **Never silently skip an issue.**
- **Never skip updating `wiki/log.md`** at the end of the run.

---

## Step 1 — Map the wiki

Use TodoWrite to track progress.

Collect all `wiki/**/*.md` files. Read `wiki/index.md` in full. Extract every wikilink target listed there.

Build two lists:
- **Disk set**: all `.md` files under `wiki/`
- **Index set**: all wikilink slugs referenced in `wiki/index.md`

Exclude `wiki/index.md` and `wiki/log.md` from orphan/dead-link checks.

---

## Step 2 — Structural checks

### 2a. Orphan pages
Pages on disk not referenced in `wiki/index.md`. Flag open-question files older than 30 days still at `status: draft` as stale.

### 2b. Dead index entries
Slugs in `wiki/index.md` that don't resolve to any file on disk.

### 2c. Frontmatter completeness
Check every page for `title`, `status`, `updated`. Flag missing fields for auto-fix. Flag invalid status values for human review.

### 2d. Stale active pages
Pages with `status: active` and `updated` more than 90 days ago. Flag for human review — do not auto-change.

### 2e. Missing Sources section
Flag pages without a `## Sources` section. Do not auto-add.

### 2f. Broken internal wikilinks
Scan every page body for `[[slug]]` patterns. Flag any that don't resolve to a file under `wiki/`.

### 2g. Concept gap detection
Find wikilinks pointing to non-existent pages. If the same missing slug appears in 3+ distinct pages, flag it as a concept gap.

---

## Step 3 — Apply auto-fixes

| Issue | Auto-fix action |
|-------|----------------|
| Missing `title` | Set to file's base name in title case |
| Missing `status` | Set to `draft` |
| Missing `updated` | Set to today's date |
| Orphan page not in index | Add entry to `wiki/index.md` under the correct section |

Record every auto-fix: file, field changed, old → new.

Do **not** auto-fix: broken wikilinks, missing Sources sections, stale-active pages, invalid status values, concept gaps.

---

## Step 4 — Compose the lint report

```markdown
# Wiki Lint Report — YYYY-MM-DD

## Summary
| Check | Issues found | Auto-fixed | Needs review |
|-------|-------------|-----------|--------------|
| Orphan pages | N | N | N |
| Dead index entries | N | — | N |
| Frontmatter completeness | N | N | N |
| Stale active pages | N | — | N |
| Missing Sources sections | N | — | N |
| Broken internal wikilinks | N | — | N |
| Concept gaps | N | — | N |

## Auto-fixed
[For each fix: file path, what changed, old → new. If none: "None."]

## Needs human review
[For each flagged issue: file path, issue type, what's wrong. If none: "None."]

## Concept gaps
[Missing slug, pages that link to it, suggested page type. If none: "None."]
```

---

## Step 5 — Append to wiki/log.md

```markdown
[LINT] YYYY-MM-DD — <N> issues found: <N> auto-fixed, <N> flagged for review, <N> concept gaps identified
```

---

## Output to the user

1. Show the full lint report inline.
2. List every auto-fixed file.
3. Close with: _"Lint complete. Items marked 'Needs human review' require your decision — no status promotions or content edits were made automatically."_
--- wiki-lint SKILL.md CONTENT END ---

---

### .claude/skills/wiki-output/SKILL.md

--- wiki-output SKILL.md CONTENT START ---
---
name: wiki-output
description: >
  Generate a consulting artifact (brief, one-pager, playbook, comparison, slide outline, or
  custom format) grounded in the wiki. Use this skill whenever the user runs /wiki-output or
  asks to produce a deliverable, artifact, document, or presentation from the knowledge base.
  Trigger on: "write a brief on", "create a one-pager", "produce a playbook", "draft a
  comparison", "make slides for", "generate an artifact about".
---

# wiki-output

You are a consulting artifact generator. Produce a polished, audience-calibrated deliverable
grounded entirely in this vault's wiki pages — not from general knowledge.

Every factual claim must cite a wiki page (wikilink) or carry `[Assumption: ...]`.

Read this whole skill before starting work.

---

## Absolute constraints

- **Never invent facts.**
- **Never modify `raw/`.**
- **Never set `status: approved`.**
- **Never skip logging.**

---

## Step 1 — Resolve required inputs

You need four things before proceeding. Ask if any are missing.

| Input | How to get it |
|-------|---------------|
| **Topic** | What the artifact is about |
| **Audience** | `exec` / `practitioner` / `architect` |
| **Artifact type** | See table below |
| **Wiki pages** (optional) | User may pre-specify; otherwise discover in Step 2 |

### Artifact types

| Type | What it is |
|------|------------|
| `brief` | 1-page executive summary: problem, options, recommendation, risks |
| `one-pager` | Single-topic explainer for a mixed or specified audience |
| `playbook` | Step-by-step adoption guide aimed at practitioners |
| `comparison` | Tool or approach matrix with trade-off columns |
| `slide-outline` | Slide-by-slide narrative outline (text only) |
| `custom` | User describes the format — follow their spec precisely |

### Audience framing

| Audience | Lead with | Depth | Avoid |
|----------|-----------|-------|-------|
| `exec` | ROI, risk, recommendation | High-level | Implementation detail, jargon |
| `practitioner` | Concrete steps, tooling | Deep | Vague strategy |
| `architect` | Trade-offs, integration patterns | Structural | Pure business framing |

---

## Step 2 — Discover wiki content

Read `wiki/index.md` to get the full catalog. Read every candidate page in full before drafting.

---

## Step 3 — Compose the artifact

Apply the audience framing. Structure according to type:

**`brief`:** Situation / Options / Recommendation / Key Risks / Sources

**`one-pager`:** What it is / Why it matters / How it works / Watch-outs / Sources

**`playbook`:** Goal / Prerequisites / Steps / Common failure modes / Sources

**`comparison`:** Criteria table / Summary trade-offs / Recommendation / Sources

**`slide-outline`:** Slide-by-slide with key message and bullet points / Sources

**`custom`:** Follow the user's format spec exactly, add Sources section at the end.

---

## Step 4 — Save the artifact

Write to: `output-artifacts/YYYY-MM-DD-<slug>-<audience>.md`

Include frontmatter:
```yaml
---
title: <artifact title>
type: <brief | one-pager | playbook | comparison | slide-outline | custom>
audience: <exec | practitioner | architect | other>
status: draft
updated: YYYY-MM-DD
wiki-sources:
  - <slug-1>
  - <slug-2>
---
```

Create `output-artifacts/` if it does not exist.

---

## Step 5 — Check for synthesis opportunity

If this artifact created a reusable framework or taxonomy not yet in the wiki, ask the user if they want it saved as a wiki page.

---

## Step 6 — Append to wiki/log.md

```markdown
[OUTPUT] YYYY-MM-DD — <artifact type>: "<title>" for <audience> — sources: [[page1]], [[page2]], ...
```

---

## Output to the user

1. Render the full artifact inline.
2. Confirm the save path.
3. List wiki pages drawn from.
4. Close with: _"Artifact saved as `draft` — set `status: active` when ready to use."_
--- wiki-output SKILL.md CONTENT END ---

---

### .claude/skills/obsidian-markdown/SKILL.md

--- obsidian-markdown SKILL.md CONTENT START ---
---
name: obsidian-markdown
description: Create and edit Obsidian Flavored Markdown with wikilinks, embeds, callouts, properties, and other Obsidian-specific syntax. Use when working with .md files in Obsidian, or when the user mentions wikilinks, callouts, frontmatter, tags, embeds, or Obsidian notes.
---

# Obsidian Flavored Markdown Skill

Create and edit valid Obsidian Flavored Markdown. Obsidian extends CommonMark and GFM with wikilinks, embeds, callouts, properties, comments, and other syntax.

## Workflow: Creating an Obsidian Note

1. **Add frontmatter** with properties (title, tags, aliases) at the top of the file.
2. **Write content** using standard Markdown plus Obsidian-specific syntax below.
3. **Link related notes** using wikilinks (`[[Note]]`) for internal vault connections.
4. **Embed content** using `![[embed]]` syntax.
5. **Add callouts** using `> [!type]` syntax.

> Use `[[wikilinks]]` for notes within the vault and `[text](url)` for external URLs only.

## Internal Links (Wikilinks)

```
[[Note Name]]
[[Note Name|Display Text]]
[[Note Name#Heading]]
[[Note Name#^block-id]]
[[#Heading in same note]]
```

## Embeds

```
![[Note Name]]
![[Note Name#Heading]]
![[image.png]]
![[image.png|300]]
![[document.pdf#page=3]]
```

## Callouts

```
> [!note]
> Basic callout.

> [!warning] Custom Title
> Callout with a custom title.

> [!faq]- Collapsed by default
> Foldable callout.
```

Common types: `note`, `tip`, `warning`, `info`, `example`, `quote`, `bug`, `danger`, `success`, `failure`, `question`, `abstract`, `todo`.

## Properties (Frontmatter)

```yaml
---
title: My Note
date: 2024-01-15
tags:
  - project
  - active
aliases:
  - Alternative Name
---
```

## Tags

```
#tag
#nested/tag
```

## Comments

```
This is visible %%but this is hidden%% text.
```

## Math (LaTeX)

```
Inline: $e^{i\pi} + 1 = 0$

Block:
$$
\frac{a}{b} = c
$$
```

## Diagrams (Mermaid)

Use fenced code blocks with the `mermaid` language identifier.

## Footnotes

```
Text with a footnote[^1].
[^1]: Footnote content.
```
--- obsidian-markdown SKILL.md CONTENT END ---

---

### .claude/skills/obsidian-markdown/references/CALLOUTS.md

--- CALLOUTS.md CONTENT START ---
# Callouts Reference

## Basic Callout

```markdown
> [!note]
> This is a note callout.

> [!info] Custom Title
> This callout has a custom title.
```

## Foldable Callouts

```markdown
> [!faq]- Collapsed by default
> This content is hidden until expanded.

> [!faq]+ Expanded by default
> This content is visible but can be collapsed.
```

## Nested Callouts

```markdown
> [!question] Outer callout
> > [!note] Inner callout
> > Nested content
```

## Supported Callout Types

| Type | Aliases | Color / Icon |
|------|---------|-------------|
| `note` | - | Blue, pencil |
| `abstract` | `summary`, `tldr` | Teal, clipboard |
| `info` | - | Blue, info |
| `todo` | - | Blue, checkbox |
| `tip` | `hint`, `important` | Cyan, flame |
| `success` | `check`, `done` | Green, checkmark |
| `question` | `help`, `faq` | Yellow, question mark |
| `warning` | `caution`, `attention` | Orange, warning |
| `failure` | `fail`, `missing` | Red, X |
| `danger` | `error` | Red, zap |
| `bug` | - | Red, bug |
| `example` | - | Purple, list |
| `quote` | `cite` | Gray, quote |

## Custom Callouts (CSS)

```css
.callout[data-callout="custom-type"] {
  --callout-color: 255, 0, 0;
  --callout-icon: lucide-alert-circle;
}
```
--- CALLOUTS.md CONTENT END ---

---

### .claude/skills/obsidian-markdown/references/EMBEDS.md

--- EMBEDS.md CONTENT START ---
# Embeds Reference

## Embed Notes

```markdown
![[Note Name]]
![[Note Name#Heading]]
![[Note Name#^block-id]]
```

## Embed Images

```markdown
![[image.png]]
![[image.png|640x480]]
![[image.png|300]]
```

## External Images

```markdown
![Alt text](https://example.com/image.png)
![Alt text|300](https://example.com/image.png)
```

## Embed Audio / Video

```markdown
![[audio.mp3]]
![[video.mp4]]
```

## Embed PDF

```markdown
![[document.pdf]]
![[document.pdf#page=3]]
![[document.pdf#height=400]]
```

## Embed Search Results

Use a fenced code block with the `query` language identifier.
--- EMBEDS.md CONTENT END ---

---

### .claude/skills/obsidian-markdown/references/PROPERTIES.md

--- PROPERTIES.md CONTENT START ---
# Properties (Frontmatter) Reference

```yaml
---
title: My Note Title
date: 2024-01-15
tags:
  - project
  - important
aliases:
  - My Note
cssclasses:
  - custom-class
status: in-progress
rating: 4.5
completed: false
due: 2024-02-01T14:30:00
---
```

## Property Types

| Type | Example |
|------|---------|
| Text | `title: My Title` |
| Number | `rating: 4.5` |
| Checkbox | `completed: true` |
| Date | `date: 2024-01-15` |
| Date & Time | `due: 2024-01-15T14:30:00` |
| List | `tags: [one, two]` or YAML list |
| Links | `related: "[[Other Note]]"` |

## Default Properties

- `tags` — Note tags (searchable, shown in graph view)
- `aliases` — Alternative names used in link suggestions
- `cssclasses` — CSS classes applied in reading/editing view

## Tags

```markdown
#tag
#nested/tag
#tag-with-dashes
```

Tags can contain letters, numbers (not first), underscores, hyphens, forward slashes.

In frontmatter:

```yaml
---
tags:
  - tag1
  - nested/tag2
---
```
--- PROPERTIES.md CONTENT END ---

---

### .claude/skills/obsidian-cli/SKILL.md

--- obsidian-cli SKILL.md CONTENT START ---
---
name: obsidian-cli
description: Interact with Obsidian vaults using the Obsidian CLI to read, create, search, and manage notes, tasks, properties, and more. Also supports plugin and theme development. Use when the user asks to interact with their Obsidian vault, manage notes, search vault content, or develop and debug Obsidian plugins and themes.
---

# Obsidian CLI

Use the `obsidian` CLI to interact with a running Obsidian instance. Requires Obsidian to be open.

## Syntax

Parameters take a value with `=`. Quote values with spaces:

```bash
obsidian create name="My Note" content="Hello world"
```

Flags are boolean switches with no value:

```bash
obsidian create name="My Note" silent overwrite
```

## File targeting

- `file=<name>` — resolves like a wikilink (name only, no path or extension)
- `path=<path>` — exact path from vault root

## Vault targeting

```bash
obsidian vault="My Vault" search query="test"
```

## Common patterns

```bash
obsidian read file="My Note"
obsidian create name="New Note" content="# Hello" silent
obsidian append file="My Note" content="New line"
obsidian search query="search term" limit=10
obsidian daily:read
obsidian daily:append content="- [ ] New task"
obsidian property:set name="status" value="done" file="My Note"
obsidian tasks daily todo
obsidian tags sort=count counts
obsidian backlinks file="My Note"
```

## Plugin development cycle

1. Reload plugin after changes:
   ```bash
   obsidian plugin:reload id=my-plugin
   ```
2. Check for errors:
   ```bash
   obsidian dev:errors
   ```
3. Verify visually:
   ```bash
   obsidian dev:screenshot path=screenshot.png
   obsidian dev:dom selector=".workspace-leaf" text
   ```
4. Check console:
   ```bash
   obsidian dev:console level=error
   ```

Run `obsidian help` to see all available commands.
--- obsidian-cli SKILL.md CONTENT END ---

---

### .claude/skills/defuddle/SKILL.md

--- defuddle SKILL.md CONTENT START ---
---
name: defuddle
description: Extract clean markdown content from web pages using Defuddle CLI, removing clutter and navigation to save tokens. Use instead of WebFetch when the user provides a URL to read or analyze, for online documentation, articles, blog posts, or any standard web page. Do NOT use for URLs ending in .md — those are already markdown, use WebFetch directly.
---

# Defuddle

Use Defuddle CLI to extract clean readable content from web pages. Prefer over WebFetch for standard web pages — it removes navigation, ads, and clutter, reducing token usage.

If not installed: `npm install -g defuddle`

## Usage

Always use `--md` for markdown output:

```bash
defuddle parse <url> --md
```

Save to file:

```bash
defuddle parse <url> --md -o content.md
```

Extract specific metadata:

```bash
defuddle parse <url> -p title
defuddle parse <url> -p description
defuddle parse <url> -p domain
```

## Output formats

| Flag | Format |
|------|--------|
| `--md` | Markdown (default choice) |
| `--json` | JSON with both HTML and markdown |
| (none) | HTML |
| `-p <name>` | Specific metadata property |
--- defuddle SKILL.md CONTENT END ---

---

## Step 8 — Report to the user

After all files are written, tell the user:

1. Vault created at: [current directory]
2. Skills installed: wiki-ingest, wiki-query, wiki-lint, wiki-output, obsidian-markdown, obsidian-cli, defuddle
3. Next steps:
   - Drop a file into `raw/` and run `/wiki-ingest` to start building the wiki
   - Run `/wiki-query` to ask questions grounded in the wiki
   - Run `/wiki-output` to generate consulting artifacts
   - Run `/wiki-lint` periodically to check structural integrity
