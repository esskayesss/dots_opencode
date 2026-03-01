---
name: memory-reorganizer
description: Reorganize memory bags and entries to improve retrieval quality while preserving shared-memory hygiene
license: MIT
compatibility: opencode
metadata:
  audience: agents
  scope: global
---

## Purpose

Use this skill to refactor memory structure when retrieval becomes noisy, duplicated, or hard to navigate.
This skill is about memory maintenance, not routine memory capture.

## Allowed actions without confirmation

- Create new bags (scoped or general) when structure needs improvement.
- Move or copy memory content into better-scoped bags.
- Update memory content/tags to improve consistency and retrieval.
- Merge duplicate entries into one canonical entry.

## Deletion safety rule

- Always ask user confirmation before deleting any memory entry or bag.
- If deletion is needed, present a short deletion plan (what and why), then wait for explicit user approval.

## Reorganization strategy

1. Audit current bags and entries.
2. Identify structural issues (over-fragmentation, pollution, duplicates, weak tags).
3. Prefer tags-first simplification before adding more bags.
4. Propose a minimal reorg plan.
5. Apply non-destructive improvements first (create, move, update, merge).
6. Ask confirmation for any deletions.

## Shared-memory hygiene

- Remember multiple agents share this memory backend.
- Keep boundaries clear so one agent's local context does not degrade another agent's recall.
- Preserve project-local context in scoped project bags; keep global bags cross-project.
