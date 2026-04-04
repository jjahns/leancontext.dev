---
title: "The Lean Context Standard"
subtitle: "A Structural Specification for Human-Agent Repositories"
date: 2026-04-04
layout: "single"
---

## What This Is

This is the implementation spec for the Lean Context Thesis. The thesis is the philosophy — why curated, modular, pull-based context makes humans and agents smarter together. This document is the structure — how you organize a repository so that philosophy is enforceable, not aspirational.

This spec is agent-agnostic and model-agnostic. It does not care whether you use Claude, GPT, Gemini, or a local model. It does not care whether your agent runs through Claude Code, Cursor, Aider, OpenCode, or a custom harness. It cares about the cultural contract between the human and the agent, and the repository structure that encodes it.

## The Cultural Contract

The Lean Context standard is a two-sided agreement.

### Human Commitments

- Keeping the constitution under budget and load-bearing.
- Maintaining the manifest as a live routing table.
- Writing invisible knowledge into supply documents when the agent surfaces gaps.
- Not documenting what the agent can infer from the codebase.
- Running compaction periodically — demoting, pruning, validating.
- Ending sessions when the task is complete. One task, one session, clean workbench. The session boundary is the primary compaction mechanism.
- Trusting the agent to pull what it needs.

### Agent Commitments

- Reading the constitution before any work begins.
- Consulting the manifest to identify relevant supply documents.
- Pulling supply documents when the task matches a route.
- Stopping when operating without sufficient grounding — the andon rule. Surfacing what it thinks it is missing rather than guessing.
- When a gap is surfaced, helping the human fill it — articulating what is needed, collaborating on the supply document, confirming the result would have been sufficient for the task.
- Never treating the constitution as optional or the manifest as decoration.

This is not a technical integration. It is a working agreement. The repository structure makes the agreement enforceable by giving each commitment a specific location, a loading strategy, and a failure mode the human can diagnose.

## The Spine Pattern

The Lean Context standard is implemented through the spine pattern — a consistent structural layout that runs through every repository and connects the three tiers into a single navigable system.

The spine is the connective tissue of the repository. The constitution sits at the root. The manifest routes along it. Supply documents branch off it. Every repository that follows the standard has the same structural skeleton, regardless of what the project does or which agent tool is used.

### Repository Layout

```
<repo-root>/
├── CONSTITUTION.md          ← always loaded, hard stops only
├── MANIFEST.md              ← always visible, routing table
├── .context/                ← supply documents, pulled on demand
│   ├── <topic>.md
│   ├── <topic>.md
│   └── ...
└── <your code>
```

The names are deliberate. `CONSTITUTION.md` and `MANIFEST.md` are uppercase because they are governance documents, not implementation details. They sit at the repository root because they apply to all work in the repository. The `.context/` directory is dotfile-prefixed because its contents are infrastructure — available to the agent, invisible in casual directory listings, and not part of the application.

### Adapting to Tool Conventions

Some agent tools expect specific file names or locations. Claude Code loads `CLAUDE.md` automatically. Cursor reads `.cursorrules`. Other tools have their own conventions. The standard accommodates this: use whatever filename your tool requires for the constitution, and symlink or include from the canonical location if needed. The principle — not the filename — is what matters. Adapt the names. Keep the spine.

### The Spine in a Monorepo

The spine pattern extends through nested structures. Each service or domain can have its own `.context/` directory with its own supply documents. The root constitution and manifest apply globally. Service-level constitutions supplement — never contradict — the root.

```
<repo-root>/
├── CONSTITUTION.md              ← global hard stops
├── MANIFEST.md                  ← global routing
├── .context/                    ← shared supply
│   └── infrastructure.md
├── services/
│   ├── auth/
│   │   ├── CONSTITUTION.md      ← auth-specific (supplements root)
│   │   ├── .context/
│   │   │   └── token-flow.md
│   │   └── src/
│   └── billing/
│       ├── .context/
│       │   └── payment-rules.md
│       └── src/
```

The spine repeats at each level. Every directory that an agent might operate in can have its own constitution and supply. The root spine sets the global contract. Service spines add domain-specific context.

### The Spine Across Repositories

In a multi-repo environment, the spine pattern provides consistency across projects. Every repository follows the same structure. An agent moving between repositories does not need to learn a new organizational scheme. The spine is the same everywhere. Only the content changes.

## The Constitution

### Purpose

Rules that, if missed once, cause damage that survives past the session. Always loaded — every session, every task, no exceptions.

### What Belongs Here

- Hard stops: "Never run destructive commands without confirmation."
- Security boundaries: "Never expose credentials in code or logs."
- Data integrity rules: "Never write directly to production databases."
- Non-negotiable procedures: "Always run the test suite before committing."
- The andon instruction: "When you lack sufficient context to proceed with confidence, stop and surface what you think you are missing. Do not guess."

### What Does Not Belong Here

- Style guides — the agent can infer these from the codebase, or they belong in linters.
- Architectural overviews — these are supply documents.
- Workflow preferences — these are supply documents.
- Lessons learned — these belong in the supply, not the constitution. The constitution is not a journal.
- Anything the agent could figure out by reading the code.

### The Irreversibility Test

For every line in the constitution, ask: if the agent misses this once, is the damage irreversible? "Irreversible" means damage that survives past the session — corrupted production data, a security boundary crossed, a destructive command executed without confirmation. If the mistake can be caught in code review or rolled back from version control, it is supply material, not a constitutional rule.

### Budget

30 to 50 lines for most projects. If the constitution exceeds 100 lines, it contains reference material masquerading as rules. Run compaction.

## The Manifest

### Purpose

A routing table that maps task patterns to supply documents. It tells the agent what context is available and when to load it — without holding any of that context itself.

### Example

```
# Manifest

- Networking tasks → .context/network-standards.md
- Authentication or identity → .context/auth-architecture.md
- Deployment or release → .context/deploy-procedures.md
- Ceph or storage operations → .context/ceph-operations.md, .context/storage-decisions.md
- Database schema changes → .context/database-conventions.md
- CI/CD pipeline changes → .context/ci-pipeline.md
- New service creation → .context/service-template.md
```

### Budget

10 to 15 lines. If the manifest exceeds 25 entries, the supply is either too granular or the project needs sub-manifests per domain.

## The Supply

### Purpose

Supply documents contain the invisible knowledge that enables depth. They are pulled on demand — loaded when the task requires them, never pushed in bulk.

### Format and Naming

Each supply document covers one topic. Under 200 lines. Named by retrieval key — the topic the agent is looking for — not by author, date, or ticket number.

```
Good:  network-standards.md, auth-architecture.md, ceph-operations.md
Bad:   jay-notes-march.md, HDI-805-decisions.md, meeting-2026-03-15.md
```

### Staleness

Every supply document should carry a `last-reviewed` date. Documents untouched beyond 90 days should be flagged for review. Stale supply documents are worse than missing ones — they give the agent confidence in outdated information.

## The Knowledge Boundary

### Inferable Knowledge — Do Not Document

The agent can discover this from the codebase. File layouts, code conventions, dependency relationships, API patterns. If the agent can learn it by reading the code, it does not earn a place in the system.

One exception: if rediscovery is expensive, the system — not the human — can generate a lightweight summary and make it available for pull. The human does not write or maintain inferable knowledge. Automation can.

### Invisible Knowledge — Document It

The agent cannot discover this from the codebase. Why a decision was made. What was tried and rejected. External constraints the code does not encode. Consequences invisible until too late.

If the agent could work on this codebase for a year and never discover it, it is invisible knowledge. Write it down.

### The Human's Job

Stop documenting what the agent can see for itself. Spend that time writing down the things it never could.

## The Andon Rule

The most important behavioral instruction in the constitution.

### The Instruction

```
When you lack sufficient context to proceed with confidence, stop.
Do not guess. Do not fill gaps with assumptions. State what you
believe you are missing and ask. The act of stopping is the system
working, not failing.
```

### When the Agent Stops

Name the specific uncertainty. State what it would assume if forced to continue. Wait.

### When the Human Responds

Point to a missed supply doc, or work with the agent to write a new one. After the gap is filled, add the manifest route. The session ends. The next session starts with the gap filled.

Every andon stop that produces a supply document is the system getting smarter.

### Alert Fatigue

The early stages will feel slower. The agent is stopping to ask questions it previously would have hallucinated past. As the supply matures, the agent stops less — not because it is guessing more, but because the system has fewer gaps.

## Session Discipline

The most important compaction mechanism is not a process — it is a habit. When the task is done, the session ends.

The Lean Context standard makes runtime compaction unnecessary by eliminating the conditions that require it. A well-structured session starts with a clean workbench. The agent does the work. The work finishes. The session ends.

### Two Clean Endings

1. The problem is solved. The session produced a code change. It ends.
2. A gap was discovered and filled. The agent pulled the andon cord. The human and agent wrote a supply document. The manifest route was added. The session produced a context change. It ends.

Every session leaves the repository better than it found it. Either the code improved or the context system improved. Nothing lingers.

## Context Compaction

Two completely different operations. Do not confuse them.

### Runtime Compaction

What agent harnesses do mid-session when the context window fills up. Reactive. Lossy. A symptom of a session that has lived too long.

### Lean Compaction

What the human does to the repository structure between sessions. Proactive. Intentional. Keeps sessions starting clean.

If you do lean compaction well, the agent should rarely or never need runtime compaction. Runtime compaction is the agent's safety net. Lean compaction is the human's discipline. The standard is built around the discipline, not the safety net.

### Constitution Compaction

Review every line. Is the damage irreversible? If not, demote to supply. Watch for: lessons masquerading as hard stops, style preferences after incidents, rules added in fear.

### Manifest Compaction

Do referenced documents still exist? Are trigger conditions still accurate? Are there orphan supply docs without routes?

### Supply Compaction

Still accurate? Still pulled? Can it merge with a related doc? Is any of it now inferable from refactored code?

### Cadence

Constitution compaction: every time you add a rule. Manifest and supply: monthly.

## Getting Started

### New Project

1. Create `CONSTITUTION.md` at the repo root. Hard stops plus andon instruction. Under 50 lines.
2. Create `MANIFEST.md`. Empty or one to two routes.
3. Create `.context/` directory. Empty. Supply documents accumulate as the agent surfaces gaps.
4. Work. The system builds itself through use.

The spine is now in place. Every subsequent repository follows the same structure.

### Existing Project

1. Create the three-tier spine structure.
2. Triage the existing instruction file: irreversible damage → constitution. Reference material → supply with manifest route. Agent could figure it out → delete it.
3. Run for a week. The agent's andon stops tell you what is missing.

## Summary

The spine is the same everywhere. Only the content changes.

The constitution prevents damage. The manifest enables discovery. The supply provides depth. The andon rule tells the agent when to stop — and the agent helps fill the gap it found. Session discipline keeps the workbench clean. Lean compaction keeps the system sharp between sessions so the agent never needs to compress within one.

The goal is not a well-instructed agent. The goal is a well-structured system that makes the agent — and the human — smarter every session.
