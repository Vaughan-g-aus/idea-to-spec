# Confidence levels

Every fact in a spec carries exactly one level. Use the strongest level you can honestly claim. When in doubt, go one level down.

## RAN IT (strongest)
A command, script, or test was executed and its output proves the claim. Include the command and the relevant output lines.

> **F2:** Soft-deleted notes are excluded from search results.
> - Receipt: `pytest tests/search/test_soft_delete.py -k excluded` → `1 passed`
> - Checked: a1b2c3d on 2026-10-01
> - Confidence: RAN IT

Load-bearing facts must reach this level. If they can't, the spec says so in Open questions.

## POINTED AT THE LINE
The code at a specific `file:line` says it directly, but nothing was executed. Quote the line if it's short.

> **F3:** The notes API returns 404 for soft-deleted notes.
> - Receipt: `api/notes.ts:142` — `if (note.deleted_at) return res.status(404)`
> - Checked: a1b2c3d on 2026-10-01
> - Confidence: POINTED AT THE LINE

## INFERRED
A reasonable reading with no direct proof. Always state the reasoning, so a builder knows what to re-check.

> **F4:** The mobile app does not cache deleted notes offline.
> - Receipt: no offline store found under `mobile/src/`; sync code (`mobile/src/sync.ts:30-60`) only pulls live notes.
> - Checked: a1b2c3d on 2026-10-01
> - Confidence: INFERRED. The sync filter excludes deleted notes, but a second cache could exist that we didn't find.

## UNKNOWN
We looked and could not find out. State exactly what was searched, so the next person doesn't repeat it.

> **F5:** Whether the analytics pipeline reads `notes.deleted_at`.
> - Receipt: searched this repo for `deleted_at` and `notes` in `jobs/` and `etl/`: no hits. The pipeline lives in another repo we don't have access to.
> - Checked: a1b2c3d on 2026-10-01
> - Confidence: UNKNOWN. Listed in Open questions.

## Rules

- The user's statements about the code start at INFERRED until a scout checks them.
- Scout claims without a `file:line` or command output count as INFERRED.
- "I remember" and "it's usually like this" are not receipts.
- INCONCLUSIVE is not a pass: if a command's output is ambiguous, the fact stays INFERRED.
