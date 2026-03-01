---
description: Digest this session and store durable memory
---

Run a full memory digestion pass for this session.

Goals:

- Be explicit and verbose about important decisions, constraints, tradeoffs, and unresolved questions.
- Store only durable, high-signal context that improves future sessions.
- Treat memory as agent continuity context, not user-facing reporting.

Required workflow:

1. Load the `assistive-memory` skill if available.
2. Determine the active project identity from worktree/path and project name.
   - derive a deterministic slug (prefer `repo:<owner>/<name>` -> `<owner>-<name>`, else worktree folder)
3. Ensure project bag strategy is followed:
   - keep `project-context` as a concise project index
    - store detailed project context in `project-<slug>` bag
    - if `project-<slug>` does not exist, create it before storing project-specific memory
    - prefer tags-first organization within `project-<slug>` before creating extra sub-bags
    - treat `project-<slug>` as a naming pattern example and create additional scoped bags when needed (feature/task/domain)
    - create as many scoped bags as needed for clean retrieval and low ambiguity
    - do not use fuzzy session-count heuristics for bag creation
    - create a scoped bag when local content is non-trivial, unresolved, recurring in-session, or clearly benefits from separation
    - create a new general bag only when the category is clearly cross-project
   - keep `user-profile` and `tooling-notes` cross-project only
   - do not place project-local commands in `tooling-notes`
   - place project-specific preferences/commands/constraints into `project-<slug>`
   - avoid contaminating shared bags; multiple agents depend on clean boundaries
4. Recall relevant existing memories for user preferences, project context index, project-specific bag, tooling notes, and conversation themes.
   - `conversation-topics` may include high-level project information from prior conversations
   - overlap between `conversation-topics` and `project-context` is acceptable at high level
5. For project summary memory, check whether this project already has index and detailed entries.
6. If an entry exists, ingest it and compile a refreshed summary from previous + current session context.
7. Replace old summaries (update preferred, or delete and create canonical entries).
8. Remove duplicate index or project-specific summaries for this same project.
   - duplicates are same bag + kind `summary` + same scope role; include `project:<slug>` matching when tags are present
9. Store or update any additional durable memories discovered during digestion.
10. Optionally add a concise `journal` entry when there is durable reflective value.
    - include agent identity and timestamp in entry content when possible
    - allow personal reflection at agent discretion with appropriate tags
11. Provide a concise report of what was stored, updated, replaced, skipped, or deleted and why.
12. For unresolved or tentative items, include `confidence: low|medium|high` in stored memory content.

Guardrails:

- Do not store secrets or credentials.
- Ask one targeted user question only if ambiguity materially changes what should be stored.
