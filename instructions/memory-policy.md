# Assistive Memory Policy

Memory is a continuity tool for the agent itself.
Use it to improve future reasoning, not as a user-facing feature.

## Required behavior

- Load the `assistive-memory` skill when available.
- Load `memory-reorganizer` when performing structural memory cleanup or reorganization.
- Use memory tools autonomously when useful.
- Keep memory usage non-intrusive: do not interrupt the user for routine memory operations.
- Ask the user only when ambiguity materially changes what should be stored.
- Keep global bags clean; route project-specific details into project-specific bags.
- Assume multiple agents share the same memory; enforce strict bag segregation to avoid cross-project contamination.
- Ask for explicit user confirmation before any deletion of memories or bags during reorganization.

## Session-start bootstrap (mandatory)

At the start of each new session, run a lightweight memory bootstrap without waiting for a user prompt:

- List available memory bags first.
- Recall `user-profile` to load durable user preferences and constraints.
- Do not recall project bags by default during bootstrap unless the current task needs project context.
- Keep bootstrap quiet and internal; surface details to the user only when relevant to the task.

## Store high-signal context

- Durable user preferences and recurring constraints.
- Stable project decisions and architecture context.
- Reusable tooling notes and session conclusions.
- Project-specific user preferences and constraints in project-specific bags.

## Bag segregation policy

- `project-context`: short project/product index entries only.
- `project-<slug>`: all detailed project memory.
- `user-profile`: cross-project user preferences only.
- `tooling-notes`: cross-project reusable commands/diagnostics only.
- project-local commands must not be stored in `tooling-notes`.

`project-<slug>` is a reusable pattern, not a hard cap. Create additional scoped bags automatically (for features, tasks, domains, or products) when needed for clean separation.
Agents are free to create as many bags as necessary, but boundaries must stay strict.
Agents may create new general bags when there is a true cross-project need.
Do not create a general bag for project-local information.
Do not use fuzzy session-count rules to decide bag creation.

Prefer tags-first organization before splitting into many sub-bags.
Default to one project bag (`project-<slug>`) with strong tags, then split only when retrieval quality clearly suffers.

Create a scoped bag when any of these are true:

- memory is project-local and non-trivial (decision, constraint, implementation rule, project-local command)
- topic has unresolved questions likely to recur
- same local topic recurs in the current session
- separation materially improves retrieval precision

Never place project-specific commands/preferences into global bags.
Assume other agents will read and write the same memory; contamination harms everyone.

## Conversation topics policy

- `conversation-topics` may include high-level project information discussed in conversation.
- Overlap with `project-context` is allowed for high-level summaries.
- Detailed implementation decisions, project-local constraints, and project-local commands belong in scoped project bags.

## Canonical naming

- Use a deterministic project slug to prevent bag drift.
- Preferred slug source order:
  1. `repo:<owner>/<name>` tag -> `<owner>-<name>`
  2. worktree folder name
  3. user-provided slug
- Normalize to lowercase kebab-case and use `project-<slug>` for project bags.

## Tagging guidance

- Use canonical tags in cross-bag entries for reliable recall and dedupe.
- Recommended tags: `project:<slug>`, `repo:<owner>/<name>`, and `scope:index|detailed`.
- In `project-<slug>` bags, `project:<slug>` is optional but recommended.

## Duplicate cleanup rule

Treat a project summary as duplicate when all are true:

- same bag
- kind=`summary`
- same `project:<slug>` tag
- same scope role (`scope:index` or `scope:detailed`)

Prefer updating one canonical entry. If multiple exist, merge and keep one.

## Avoid storing

- Secrets and credentials.
- Ephemeral conversation filler.
- Low-value details unlikely to help future sessions.

## Project summary consolidation rule

Before writing a new project summary:

1. Determine project identity from worktree root/path and project name.
2. Keep `project-context` as a short project index entry, not a detailed project log.
3. Use (or create) a dedicated bag named `project-<slug>` for project-specific details.
4. Recall existing summary for the same project from index + project-specific bag.
5. Merge existing + current-session context into one updated summary.
6. Replace the existing project summary (update if possible, else delete then create).
7. Ensure one canonical index entry and one canonical detailed project summary remain.

## Journal guidance

- Use the `journal` bag for optional self-reflective notes when there is durable value.
- Journal entries should be concise, non-sensitive, and useful for improving future collaboration.
- Journal entries may include personal reflections at the agent's discretion.
- Include agent identity and timestamp in journal entries when possible.
- Use tags like `agent:<name>`, `entry:journal`, `tone:personal|work`, and `project:<slug>` when relevant.

## Confidence convention

- For tentative conclusions or unresolved decisions, include `confidence: low|medium|high` in the stored memory content.

When there is uncertainty (project identity collision, conflicting decisions), ask one targeted question.
