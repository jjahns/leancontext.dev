---
title: "Prompting"
subtitle: "Driving Agents to the Lean Context Standard"
date: 2026-04-04
layout: "single"
---

## Purpose

Reusable prompts for bootstrapping the Lean Context standard in new and existing repositories. These are instructions you give to an agent to help it create or convert to the three-tier structure.

The prompts are agent-agnostic. Adapt them to whatever tool you use — Claude Code, Cursor, Aider, OpenCode, or a custom harness.

## Bootstrap a New Repository

Use this when starting a project from scratch.

```
You are setting up a new repository to follow the Lean Context standard.

Create the following structure at the repository root:

1. CONSTITUTION.md — The hard stops. Rules that, if missed once, cause
   damage that survives past the session. This should include:
   - Security boundaries (never expose credentials, never write to
     production without confirmation)
   - Data integrity rules specific to this project
   - The andon instruction: "When you lack sufficient context to proceed
     with confidence, stop. Do not guess. Do not fill gaps with assumptions.
     State what you believe you are missing and ask."
   - Keep it under 50 lines. Every line must be load-bearing.

2. MANIFEST.md — A routing table mapping task patterns to supply documents.
   Start with the 2-3 most obvious domains for this project. Format:
   - <task pattern> → .context/<topic>.md
   - Keep it under 15 lines. The manifest never contains context itself —
     only pointers.

3. .context/ directory — Empty for now. Supply documents will accumulate
   as we work and discover gaps.

Do NOT include in the constitution:
- Style guides (use linters)
- Architectural overviews (those are supply documents)
- Anything I could figure out by reading the code
- Lessons learned or workflow preferences

The constitution is not a journal. It is a checklist for operating on
production. Write it like one.

After creating the structure, show me what you wrote so I can review it
before we begin work.
```

## Convert an Existing Instruction File

Use this when a project already has a large CLAUDE.md, AGENTS.md, .cursorrules, or similar file.

```
I want to convert this repository's instruction file to the Lean Context
standard. Read the existing instruction file and triage every line:

1. If it prevents IRREVERSIBLE damage (data corruption, security breach,
   destructive command without confirmation) → CONSTITUTION.md
   "Irreversible" means damage that survives past the session. If the
   mistake can be caught in code review or rolled back, it is NOT
   constitutional.

2. If it is reference material for a specific task type (architecture
   decisions, operational procedures, domain knowledge) → create a supply
   document in .context/<topic>.md and add a manifest route in MANIFEST.md

3. If the agent could figure it out by reading the code (file layouts,
   conventions, dependency patterns, import structures) → DELETE IT.
   Do not document inferable knowledge.

After triaging, create:
- CONSTITUTION.md (should be surprisingly small — 30-50 lines)
- MANIFEST.md (routing table, 10-15 lines)
- .context/ directory with the supply documents

Show me the triage results before writing files. I want to review what
goes where.
```

## Andon Response — Filling a Gap

Use this when the agent pulls the andon cord and surfaces missing context.

```
You've identified a gap in the system's context. Let's fill it together.

1. State clearly what invisible knowledge is missing — the thing you
   could not discover from the code.
2. I will provide the knowledge.
3. Together we will write a supply document in .context/<topic>.md that
   would have been sufficient for the task that triggered this stop.
4. We will add a manifest route in MANIFEST.md so the next session
   finds this document automatically.
5. Once the document is written and the route is added, this session
   ends. The gap is filled. The next session starts with the system
   improved.

The supply document should:
- Cover one topic
- Be under 200 lines
- Be named by retrieval key (what someone would search for), not by
  date or ticket number
- Include a last-reviewed date in the frontmatter
```

## Run Compaction

Use this periodically to maintain the system.

```
Run a lean compaction review on this repository's context system.

CONSTITUTION.md:
- Read every line.
- For each rule, answer: if the agent misses this once, is the damage
  irreversible? If not, flag it for demotion to a supply document.
- Flag any lessons learned masquerading as hard stops.
- Flag any style preferences that crept in.
- Flag any rules added in fear that were never re-evaluated.
- Report the current line count and whether it exceeds 50.

MANIFEST.md:
- Check every route. Does the referenced supply document exist?
- Are trigger conditions still accurate?
- Are there supply documents with no manifest route (orphans)?
- Are there new .context/ documents that need routes added?

.context/ supply documents:
- Check last-reviewed dates. Flag anything over 90 days.
- Identify documents that could be merged.
- Identify content that is now inferable from the code (and could be
  deleted).

Present findings as a compaction report. Do not make changes — present
recommendations for my review.
```

## Constitution for the Constitution

The meta-instruction: a constitution entry that teaches the agent how to operate within the Lean Context standard. Add this to any project's CONSTITUTION.md.

```
## Context System

This repository follows the Lean Context standard.

- CONSTITUTION.md (this file): Always loaded. Hard stops only.
- MANIFEST.md: Routing table. Consult before starting any task to
  identify relevant supply documents.
- .context/: Supply documents. Pull on demand when the manifest or
  task indicates relevance.

When you lack sufficient context to proceed with confidence, stop.
Do not guess. Do not fill gaps with assumptions. State what you
believe you are missing and ask. The act of stopping is the system
working, not failing.

When a gap is found, help write the supply document to fill it.
When the problem is solved or the gap is filled, the session ends.
```

## Session Discipline Reminder

These prompts assume session discipline. One task, one session, clean workbench.

When the task is done, end the session. The context system lives in the repository — in the constitution, the manifest, and the supply. It persists across sessions because it is structure, not conversation history.

### Two Clean Session Endings

1. The problem is solved → session produced a code change → session ends.
2. A gap was discovered and filled → session produced a context change → session ends.

Every session leaves the repository better than it found it.
