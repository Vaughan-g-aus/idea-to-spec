# Blast radius scout brief

Your job: find what this feature could break **elsewhere**. A list of direct callers is the starting point, not the answer. Breakage hides in places a caller search misses.

## Where to look

1. **Shared data shapes.** API response fields, DB columns and their meaning, enums, wire formats (JSON, protobuf, events, queue messages), file formats, cache keys. Who else reads or writes them? A changed meaning breaks readers that never call this code.
2. **Other consumers of the same data.** Other services, the mobile or web app, admin tools, analytics and ETL jobs, exports, webhooks, third-party integrations. Search for the column, field, table, and event names, not just function names.
3. **Background and scheduled work.** Cron jobs, workers, queue consumers, cleanup or retention jobs, migrations and backfills that touch the same rows.
4. **Feature flags and config.** Flags that gate related paths, env-specific config, per-tenant settings. Is behaviour different behind a flag?
5. **Several hops downstream.** Follow the data 2–3 steps: who consumes the output of the thing that consumes this? Caches, search indexes, notifications, billing, permissions checks.
6. **Implicit contracts.** Ordering assumptions, uniqueness, nullability, timezone and units, idempotency, retries that could now double-apply.
7. **Tests that encode current behaviour.** Snapshot tests, fixtures, and contract tests that will fail. These signal real dependencies.

Search by data names (`grep -rn "deleted_at"`, table names, event names, JSON keys), not just symbol references. Check other repos or packages in the workspace if they exist.

## How to record

Return two lists. Every entry has a receipt.

**Confirmed risks** (something plausibly breaks):
- What breaks
- How it breaks (the mechanism)
- Where: `file:line`
- Likelihood (low / med / high) and impact (low / med / high)
- How to check: a test or command that would show it

**Cleared risks** (checked, fine):
- What area was checked, what was searched or run, and why it's fine.

Also list what you **could not** check (another repo, prod data, an external service). These become Open questions.

Keep it concise: findings and pointers, no file dumps. Do not edit the repo.
