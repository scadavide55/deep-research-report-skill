# deep-research-report-skill

Claude skill that adds a `/deep-research-report` command. Give it a topic and it runs parallel web research across 15–30+ sources, rates each source's reliability, and writes a sourced Markdown report with comparison tables and Mermaid diagrams where useful.

## Usage

```
/deep-research-report <topic you want to research;Constraint #1 ( e.g.: Ignore results from before 2026) ; Language : [your preferred language for the report]>
```

The report is written as `deep-research-<topic-slug>-<YYYY-MM-DD>.md` and sent back to you. It is written in the language of your request.

## Install

Save `SKILL.md` as a skill named `deep-research-report` (for Claude Code, put it in `~/.claude/skills/deep-research-report/SKILL.md`).



## What the report contains

- Executive summary, background, findings per sub-question
- Comparison table (only when comparing options)
- Disagreements and uncertainties, open questions
- Numbered source list with High/Medium/Low reliability ratings
