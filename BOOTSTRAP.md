# Lean Context Standard — Bootstrap

> **You are adopting the Lean Context Standard in this repository.**
> Reference: https://leancontext.dev
>
> This is additive. Do not delete existing files, code, or functionality.
> You are adding structure alongside what already exists.
> Execute every step below in order.

---

## Step 1: Understand the project

Read the repository root. Identify:

- Project name and purpose
- Primary language and framework
- Build system and test runner
- Deployment method (if apparent)
- Any existing agent instruction files: `CLAUDE.md`, `AGENTS.md`, `.cursorrules`, `CODING_GUIDELINES.md`, `.github/copilot-instructions.md`, or similar

If existing instruction files are found, read them. Their content will be distributed across the new structure in later steps. **Do not delete or overwrite them yet.**

---

## Step 2: Create the supply directory

```bash
mkdir -p .context
```

If `.context/` already exists, leave its contents in place.

---

## Step 3: Create `CONSTITUTION.md`

Create `CONSTITUTION.md` in the repository root.

**This file is always loaded by the agent. It must be short.**

- Target: 30–50 lines. Hard ceiling: 100.
- Every line must pass one test: *if the agent misses this once, is the damage irreversible?*
- No reference material. No style guides. No workflow descriptions. Those belong in `.context/`.
- If a mistake can be caught in code review or rolled back from version control, it does not belong here.

Use this structure:

```markdown
# Constitution

## Identity
[Project name. One sentence: what this is and why it matters.]

## Hard Stops
[Only rules where violation causes irreversible damage.]
- Never [destructive action] without explicit confirmation.
- Never commit secrets, keys, or credentials to this repository.
- Never [action that crosses a security or data boundary].

## Non-Negotiable Procedures
[Only procedures that prevent catastrophic failure.]
- [Procedure protecting production state.]
- [Procedure protecting data integrity.]

## Agent Behavior
- Read MANIFEST.md before starting any task.
- Pull .context/ documents when the task matches a manifest route.
- Stop and surface uncertainty rather than guessing.
  State what you think you are missing.
```

Populate hard stops and procedures by inferring from:
1. Existing instruction files (extract only the irreversible-damage rules).
2. CI/CD configuration, deployment scripts, and protected branch settings.
3. The nature of the project (database? production API? infrastructure?).

Everything from existing instruction files that does **not** qualify as a hard stop goes into supply documents in Step 5.

**Demotion rule**: The constitution will bloat. This is certain. Revisit it regularly. For every line, ask: if the agent misses this once, is the damage irreversible? If the answer is no, move it to a `.context/` supply document. The lesson is preserved. The workbench stays clean.

---

## Step 4: Create `MANIFEST.md`

Create `MANIFEST.md` in the repository root.

**This is a routing table. It maps task patterns to supply documents.**

- Target: 10–15 lines. It is an index, not a document.
- The manifest never contains context itself — only pointers.

Use this structure:

```markdown
# Manifest

| Task Pattern | Context Source |
|---|---|
| [pattern relevant to this project] | `.context/[name].md` |
```

Generate 3–8 routes based on what you discovered in Step 1. Only create routes for areas this project actually has. Common patterns:

- Authentication or authorization changes → `.context/auth.md`
- Database or schema changes → `.context/schema.md`
- Deployment or release procedures → `.context/deploy.md`
- API design or contract changes → `.context/api.md`
- Testing conventions → `.context/testing.md`
- Frontend or styling conventions → `.context/frontend.md`
- Infrastructure or DevOps → `.context/infrastructure.md`
- Security policies → `.context/security.md`

---

## Step 5: Create supply documents

For each route in the manifest, create the corresponding `.context/[name].md` file.

**Only write down invisible knowledge** — why decisions were made, what was tried and rejected, what constraints the code does not encode. If the agent can discover it by reading the code, it does not belong here.

### Decisions as a first-class pattern

Create `.context/decisions/` for architectural decision records. The thesis identifies invisible knowledge as "why a decision was made, what was tried and rejected" — that deserves its own home, not just a heading buried inside topic documents.

```bash
mkdir -p .context/decisions
```

Each decision record can be as short as it needs to be:

```markdown
# [Decision Title]

**Status**: Accepted | Superseded | Deprecated
**Date**: YYYY-MM-DD

## Decision
[What was decided.]

## Why
[Why this was chosen. What was considered and rejected.]

## Constraints
[What external factors forced or influenced this decision.]
```

Add a manifest route for decisions:
```
| Architectural or design decision questions | `.context/decisions/` |
```

### Topic-based supply documents

Each supply document follows this structure:

```markdown
# [Topic]

## Context
[Why this document exists. What the agent cannot discover by reading the code.]

## Constraints
[Constraints not encoded in the code. External requirements, compliance, compatibility.]

## Procedures
[Step-by-step instructions when applicable.]
```

Populate supply documents from:
1. Content from existing instruction files that did not qualify for the constitution.
2. Invisible knowledge the human provides: decisions, trade-offs, rejected approaches, constraints not encoded in the code.
3. For decisions you cannot infer, leave a placeholder:
   `<!-- FILL IN: Why was this approach chosen? What was rejected? -->`

Do **not** document what the agent can discover by reading the code — file layout, naming conventions, dependency structure, build commands. That is inferable knowledge. Writing it down is redundant at best and harmful at worst.

**Size guidance**: Each supply document should be under 150 lines. A three-line decision record or a five-line principle statement is valid — the standard says "small enough that loading it is cheap, and specific enough that all of it is relevant." If a document exceeds 150 lines, split it into focused documents and update the manifest routes.

### Domain manifests

Start flat. When a domain inside `.context/` accumulates more than ~5 files, give it its own directory and `MANIFEST.md`. The root manifest routes to domains; domain manifests route to documents.

```
.context/
├── auth.md
├── deploy.md
├── testing.md
├── decisions/              ← domain with sub-routing
│   ├── MANIFEST.md         ← routes within this domain
│   ├── 001-database.md
│   ├── 002-auth-provider.md
│   └── 003-api-versioning.md
└── infrastructure/         ← another domain
    ├── MANIFEST.md
    ├── network.md
    ├── storage.md
    └── monitoring.md
```

A domain `MANIFEST.md` follows the same format as the root manifest:

```markdown
# Decisions Manifest

| Task Pattern | Context Source |
|---|---|
| Database architecture questions | `001-database.md` |
| Auth provider selection | `002-auth-provider.md` |
| API versioning approach | `003-api-versioning.md` |
```

---

## Step 6: Create tool symlinks

Symlink the constitution so each agent tool finds it automatically:

```bash
ln -sf CONSTITUTION.md CLAUDE.md
ln -sf CONSTITUTION.md AGENTS.md
ln -sf CONSTITUTION.md .cursorrules
```

If any of these files already exist as real files (not symlinks), **do not overwrite them**. Instead:
1. Read their content.
2. Migrate relevant content into the constitution or supply documents.
3. Then replace with the symlink.

---

## Step 7: Verify nothing is excluded

Check `.gitignore`. Ensure none of these are ignored:
- `CONSTITUTION.md`
- `MANIFEST.md`
- `.context/`
- `CLAUDE.md`
- `AGENTS.md`
- `.cursorrules`

If any are listed in `.gitignore`, remove those entries.

**Inverse rule**: If any `.context/` content is generated, synced from upstream, or fetched at build time (e.g., a parent constitution pulled via curl, auto-generated dependency maps), add those specific paths to `.gitignore`. Authored context is tracked. Generated context is not.

---

## Step 8: Multi-repository support

**Skip this step if this is a standalone repository.**

If this repository is part of a multi-repository system — a platform with shared services, a monorepo parent with child repos, or an organization where multiple repos share governance — apply the following pattern.

### Parent repository (the governance root)

The parent holds the authoritative constitution and a manifest that routes to both local supply documents and child-repository context.

```
platform/
├── CONSTITUTION.md          ← governance for the whole system
├── MANIFEST.md              ← routes to local and child context
├── .context/                ← platform-level supply
│   ├── architecture.md
│   └── security.md
├── services/
│   ├── service-a/           ← child repo or subdirectory
│   │   ├── CONSTITUTION.md  ← inherits + extends parent
│   │   ├── MANIFEST.md      ← local routing
│   │   └── .context/
│   └── service-b/
│       ├── CONSTITUTION.md
│       ├── MANIFEST.md
│       └── .context/
```

### Child repository rules

1. A child `CONSTITUTION.md` **must not contradict** the parent constitution. It may add constraints specific to its scope.
2. A child `MANIFEST.md` routes to its own `.context/` directory. It may also reference parent-level supply:
   ```
   | Platform architecture questions | `../../.context/architecture.md` |
   ```
3. When working in a child repo, the agent loads the child's constitution first. If the child constitution references a parent, the agent loads the parent constitution as well.
4. Hard stops flow downward. A parent hard stop applies to all children. A child hard stop applies only to that child.

### Standalone cloned repos

If child repos are cloned independently (not as subdirectories), the parent constitution should be available as a supply document:

```bash
# In the child repo's .context/
curl -sO https://raw.githubusercontent.com/[org]/[platform-repo]/main/CONSTITUTION.md \
  && mv CONSTITUTION.md .context/parent-constitution.md
```

Add a manifest route:
```
| Questions about platform-wide governance | `.context/parent-constitution.md` |
```

**Staleness warning**: This is a point-in-time snapshot. Establish a process to refresh it when the parent changes — a CI hook, an agent skill, or a periodic manual check. A stale parent constitution is worse than none — the agent will follow outdated governance with full confidence.

---

## Step 9: Summary

After completing all steps, output:

1. **Files created** — list every new file.
2. **Constitution line count** — flag if over 50 lines.
3. **Manifest routes** — count and list.
4. **Supply documents** — count and list, noting any with placeholder sections.
5. **Migrated content** — list any existing instruction files whose content was distributed into the new structure.
6. **Unresolved** — any content from old instruction files that you were unsure how to classify. Present it to the human for a decision.
7. **Multi-repo** — if Step 8 was applied, describe the parent/child relationship established.

**Do not delete any original files until the human confirms the migration is complete.**

---

## What happens next

The bootstrap is scaffolding. The system improves through use.

**Calibration**: Watch the agent work. When it succeeds without context you thought it needed, remove that context. When it fails, write a supply document to fill the gap. When it stops and asks, you have found invisible knowledge that needs to be externalized. Each cycle sharpens your model of what the agent can and cannot infer.

**Sustain**: Automate what you can — line budgets on the constitution, staleness checks on supply documents, dead-entry audits on the manifest. But the decision to promote, demote, or retire a document is human judgment. A context system that launches clean and decays over time has not solved the problem. It has deferred it.

---

*The Lean Context Standard · https://leancontext.dev · Jay Jahns · 2026*
