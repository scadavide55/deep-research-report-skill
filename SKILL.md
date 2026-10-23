---
name: deep-research-report
description: Run a very deep, multi-source web research pass on a topic and write the findings as a full Markdown report file. Use whenever the user invokes /deep-research-report followed by a topic, or asks for a "deep research report", a researched .md write-up, a thorough investigation saved to a file, or a multi-source briefing on any subject. Make sure to use it even when the user just says "research X in depth and give me a file".
---

# Deep Research Report

Turn a topic into a thoroughly researched, sourced Markdown report. Depth is the point of this skill: the user chose "very deep" over a quick answer, so spend the effort to read many sources, cross-check them, and say how much each deserves to be trusted.

## Input

The topic is everything after `/deep-research-report`. If no topic is given, ask one short question for it and stop.

## Workflow

### 1. Scope the question

Before searching, decide, without asking the user to approve:

- The core question in one sentence.
- 5 to 8 sub-questions that together answer it: background, current state, key players or options, evidence and numbers, disagreements, risks, and open questions. Drop any that do not apply.
- Whether the topic is time-sensitive. If so, anchor queries with the current year and prefer recent sources.
- Whether the topic involves comparing options (tools, products, approaches). If so, plan a comparison table. Whether it involves a process, architecture, or timeline. If so, plan a diagram.

### 2. Research in parallel with subagents

Split the sub-questions across subagents (the `Agent` tool, `general-purpose` type), one per sub-question or small group of related ones, and launch them all in the same turn so they run concurrently. Subagents start with no context, so each prompt must include:

- The overall topic and why it is being researched, and the specific sub-question(s) it owns.
- The instruction to use `WebSearch` (extended mode for hard or time-sensitive facts, standard otherwise) with varied phrasings, then `WebFetch` on the most authoritative results.
- The required return format: findings as short paraphrased bullets, each with its source URL, the source's publication date if known, and a reliability rating (see below) with a few words of justification. Conflicts between sources must be reported, not resolved silently.
- A reminder not to reproduce long source text.

Aim for 15 to 30 or more distinct sources across all subagents. While they run, do not duplicate their work; wait for the results.

When the subagents return, review the findings for gaps and contradictions. If a key claim rests on a single weak source or two sources disagree, run targeted follow-up searches yourself (or one more subagent) to resolve it. If it stays unresolved, report both positions.

If the `Agent` tool is not available, do the same research inline: separate searches per sub-question in the same turn, then fetch the best sources.

### 3. Rate source reliability

Give every source in the final list a rating, so the reader can weigh the evidence:

- **High**: primary or authoritative (official documentation, peer-reviewed papers, government or standards bodies, original data, first-hand announcements).
- **Medium**: reputable secondary reporting or expert analysis that cites its basis, or primary sources with an obvious interest (vendor claims about their own product).
- **Low**: forums, anonymous posts, SEO or affiliate content, undated pages, aggregators. Use these as leads only; do not rest a key claim on one.

Add a few words of justification, such as "official docs" or "vendor marketing, no independent data".

### 4. Write the report

Write one `.md` file with this structure unless the topic clearly needs another:

```markdown
# <Title>

*Researched on <YYYY-MM-DD>. Scope: <one line describing the question and what was excluded>.*

## Executive summary
Three to six sentences. The answer first, then the most important caveat.

## Background
Only the context needed to follow the findings.

## Findings
One subsection per sub-question: the finding, the evidence with inline source links, and the confidence the evidence supports.

## Comparison
A table, included only when options are being compared (columns = criteria, rows = options). Cite sources for non-obvious cells.

## Disagreements and uncertainties
Where sources conflict, where data is thin, and what would change the conclusion.

## Open questions
What the research could not settle.

## Sources
A numbered list: [Title](URL), organization, date if known, **Reliability: High/Medium/Low** and why.
```

Writing rules:

- Paraphrase everything. At most one direct quote per source, under 15 words, and only when the exact wording matters.
- Attach a source link to every specific claim, number, or date. If a claim cannot be attributed, leave it out.
- State confidence where it varies: "multiple independent high-reliability sources agree" differs from "a single vendor claims".
- Use tables for genuine comparisons and prose for explanation. Do not force a table where there is nothing to compare.
- Use Mermaid diagrams (fenced ```mermaid blocks) only where a process, architecture, dependency, or timeline is clearer drawn than described. Keep each diagram small and accurate to the cited sources; do not invent structure the sources do not support.
- No filler or restating conclusions. Depth means more evidence and better cross-checking, not more words. A very deep report typically runs 3,000 to 6,000 words.
- Write in the language of the user's request: an Italian request gets an Italian report, an English request an English one.

### 5. Save and deliver

- File name: `deep-research-<topic-slug>-<YYYY-MM-DD>.md` (slug: lowercase, hyphens, at most 6 words).
- Save it to the working directory with `Write`, then send it with `SendUserFile` (`status: normal`, short caption naming the topic). Do not write it to the user's computer unless they ask for that in the request.
- Final reply: 2 to 4 sentences with the headline finding, the number of sources, and the main uncertainty. Do not repeat the report in chat.

## Quality bar

Before saving, check:

- Every sub-question has a finding or an explicit "could not be answered".
- Every specific number, date, and named claim has a source link, and every source has a reliability rating.
- No key claim depends only on a Low-reliability source.
- Contradictions are reported, not silently resolved.
- Any table or diagram matches what the sources actually say.
- No long runs of text were copied from any source.
