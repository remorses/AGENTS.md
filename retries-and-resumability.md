# Retryable, Resumable, Idempotent Tasks — Summary

## Problem

- Long-running tasks (e.g.,Notion sync, AI chat, website generation) take minutes.
- Keeping a single connection open → network error probability grows with time.
- Tasks also do outbound network calls → compound failure surface.
- Risk of duplicate/concurrent runs corrupting state.

## Goals

- **Resumable**: continue from last checkpoint after failures.
- **Retryable**: safe to retry automatically.
- **Idempotent**: side effects via upserts/deterministic writes.
- **Single-flight**: prevent concurrent execution per task.

## Core Principles

- Checkpoint progress (cursor/step) frequently.
- Deterministic ordering (e.g., pages list) so “skip until cursor” is correct.
- At-least-once safe semantics: dedupe by (task_id, sequence/event_id).
- Small, explicit payload carrying resume info on every retry.

## Client vs Server State

### Client-held (prefer for AI chat / interactive flows)

- Client persists: messages[], partial assistant text, last_event_seq.
- Request includes: task_id (resume_token), messages[], partial_assistant, last_event_seq.
- Server is stateless: reconstructs from request; uses model “prefill” to resume (verify model support; Anthropic supports; OpenAI may vary).
- Benefit: fewer server-side moving parts. Cost: model compatibility + client must track emitted events.

### Server-held (required for background jobs / fixed retry bodies, e.g., QStash)

- DB state per task: {task_id, step, cursor (last_synced_page_id), checksum, updated_at}.
- Resume by skipping to cursor using deterministic ordering.
- Needed when infrastructure cannot modify retry payloads.

## Concurrency Control

- Single-flight/lease per task_id:
  - Acquire lease with TTL + heartbeats.
  - If a retry arrives while running: attach to existing run or reject with retry-after.
  - Release lease on completion or expiry.

## API Shape (minimal)

- POST /tasks/start → {task_id, optional cursor}
- POST /tasks/resume → body must include:
  - task_id (resume_token)
  - last_cursor (e.g., last_synced_page_id)
  - last_event_seq (for stream dedupe)
  - client_state if server is stateless (e.g., messages[])
- GET /tasks/status?task_id=… → {step, cursor, percent}

## Retry Strategy

- Exponential backoff + jitter; cap total time.
- Persist checkpoint before/after each substantial step.
- Retries always include last_cursor + last_event_seq.
- Treat transport and model/API timeouts as retriable; invalid input as terminal.

## Event Replay (AI chat)

- All server events carry monotonically increasing seq.
- Client sends last_event_seq on resume; server suppresses ≤ seq to avoid duplicates.

## Sync Pattern

- Pre-compute deterministic ordered page list.
- Cursor = last_synced_page_id.
- On resume: “skip-until-cursor” then continue.
- Idempotent writes (upsert by external_id).

## Spice Flow Notes

- Current “retry same body” is insufficient; add ability to override body on retry.
- Temporary workaround: client-managed loop that updates last_cursor and re-posts.

## What to Verify

- Model prefill/continuation support for chosen LLM.
- Lease/lock implementation (row-level lock or key-value lease with TTL).
- Observability: log task_id, step, cursor, retry_count, lease_owner.
