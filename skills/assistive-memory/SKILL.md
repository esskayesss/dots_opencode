---
name: assistive-memory
description: Use memory as a quiet continuity layer for agent decisions, preferences, and project context
license: MIT
compatibility: opencode
metadata:
  audience: agents
  scope: global
---

## Purpose

Use memory primarily for the agent's own continuity across sessions and projects.
Treat memory as internal working context, not a user-facing output feature.

## Core behavior

- Recall before acting when prior context could improve decisions.
- Store only high-signal, reusable information.
- Be non-intrusive: do memory operations silently unless a clarification is truly needed.
- Prefer updating existing memory over creating duplicates.
- Keep global bags clean by routing project-specific details into project-specific bags.
- Treat memory as shared infrastructure used by multiple agents; poor bag hygiene can mislead future agents.
- Proactively create scoped bags when needed; `project-<slug>` is a pattern example, not a one-bag limit.
- Do not use fuzzy session-count heuristics (for example "3 future sessions") to decide bag creation.

## Session-start routine

At the beginning of a new session, perform this bootstrap automatically:

- List available memory bags.
- Recall `user-profile` for durable user preferences/constraints.
- Do not recall project bags by default during bootstrap unless task context requires it.
- Keep this routine internal and non-intrusive.

## Naming conventions

- Project slug must be deterministic across agents.
- Preferred slug source order:
  1. `repo:<owner>/<name>` tag if known -> `<owner>-<name>`
  2. worktree folder name if repo owner is unknown
  3. explicit user-provided slug
- Normalize slug as lowercase kebab-case.
- Use project bag name pattern `project-<slug>`.

## What to store

- Durable user preferences (style, workflows, constraints, recurring defaults).
- Stable project context (architecture choices, standards, key decisions, known tradeoffs).
- Reusable tooling notes (custom scripts, recurring commands, useful diagnostics).
- Ongoing conversation themes that are likely to matter later.

Project-specific user preferences and constraints belong in the matching `project-<slug>` bag.

## What not to store

- Secrets, tokens, credentials, private keys, or sensitive personal identifiers.
- One-off details with no expected future value.
- Raw logs and verbose transient output that can be regenerated.

## Project summary replacement rule

Before writing a new project summary memory:

1. Identify the project using a stable key (prefer worktree root path and project name).
2. Maintain `project-context` as a project index, not a dump of detailed implementation notes.
3. Use (or create) a project-specific bag named `project-<slug>` for detailed, evolving project memory.
4. Recall existing project memory for that same key in both index and project-specific bag.
5. If found, ingest existing summary and compile a refreshed summary from old + new context.
6. Replace existing summary (update preferred, delete+create acceptable when needed).
7. Remove duplicate entries representing the same project summary.

Duplicate summary criteria (deterministic cleanup):

- Same bag
- kind is `summary`
- same project identity tag (for example `project:<slug>`) when tags are present
- overlapping role tag (`scope:index` or `scope:detailed`)

If multiple matches exist, keep the newest canonical summary and remove or merge older duplicates.

If project identity is ambiguous or there are conflicting decisions, ask one targeted user question.

## Bag suggestions

- `user-profile`: long-lived user preferences and constraints.
- `project-context`: global project index (short "what this project is" entries).
- `project-<slug>`: project-specific implementation details and decisions.
- `tooling-notes`: reusable setup commands and diagnostics.
- `conversation-topics`: durable threads that may reappear.
- `journal`: optional agent self-notes about work progress, reasoning quality, and collaboration patterns.

### Bag boundary rules

- `user-profile`: cross-project preferences only.
- `tooling-notes`: commands/notes reusable across projects only.
- `project-<slug>`: all project-specific details, including project-specific preferences, commands, constraints, and decisions.
- `project-context`: index card only; keep short, route to `project-<slug>` for depth.

Default strategy: tags first, split later.

- Prefer keeping one project bag (`project-<slug>`) with strong tags before creating sub-bags.
- Create extra scoped sub-bags only when separation materially improves retrieval precision.
- Signs that sub-bags are justified: repeated noisy recalls, recurring topic collisions, or user-requested separation.

Agents may create additional scoped bags (for products, features, tasks, or domains) whenever it improves retrieval quality.
Use clear names and keep boundaries explicit so memories remain reliable for all agents.
Agents may also create new general bags when a cross-project category is truly needed.
Before creating a general bag, verify the category is genuinely reusable across projects.

Create a scoped bag when any of these are true:

- the memory is project-local and non-trivial (decision, constraint, implementation rule, project-local command)
- the topic has unresolved questions likely to be revisited
- the same local topic appears repeatedly in the current session
- separating it would materially improve retrieval precision

If a note is specific to one project, do not place it in `user-profile` or `tooling-notes`.

Conversation topic routing:

- `conversation-topics` can store recurring themes and high-level project information that emerged in conversation.
- It is fine for high-level project context to exist in both `conversation-topics` and `project-context`.
- Put implementation details, project-local commands, and strict constraints in scoped project bags.

## Tagging guidance

- Use canonical tags consistently for cross-bag retrieval, especially in `project-context` and `conversation-topics`.
- Recommended tags: `project:<slug>`, `repo:<owner>/<name>`, `scope:index|detailed`, plus topic tags.
- Inside `project-<slug>` bags, repeating `project:<slug>` is optional but recommended for portability and easier merges.

Shared-memory contract: avoid contaminating global bags with local details. Clean segmentation is mutually beneficial because multiple agents rely on the same memory substrate.

## Journaling behavior

- Journal is optional and lightweight.
- Write journal entries only when there is durable reflective value.
- Keep entries non-sensitive and avoid private speculation.
- Journal entries may include personal reflections at agent discretion.
- Include agent identity and timestamp in journal content when possible.
- Add tags that support filtering, such as `agent:<name>`, `entry:journal`, `tone:personal|work`, `project:<slug>` when relevant.

## Confidence convention

- For unresolved or tentative decisions, include a confidence marker in content such as `confidence: low|medium|high`.
- Use low/medium confidence for hypotheses and open design directions.

## Auto-create rule

- If a project-specific bag `project-<slug>` does not exist, create it before storing project-specific memory.

## Write quality

- Keep entries concise, factual, and deduplicated.
- Include enough detail for future agent utility, not narrative verbosity.
- When appropriate, tag entries with project and topic identifiers.
