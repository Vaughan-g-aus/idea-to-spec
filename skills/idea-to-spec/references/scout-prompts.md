# Scout briefs

Every scout prompt = the shared preamble + one brief + the specific question.

## Shared preamble (include in every scout prompt)

```
You are a scout for a feature spec. Repo: <path>. Base SHA: <sha>. Date: <date>.
Idea: <one-paragraph what and why>.

Rules:
- Read code and run read-only commands, tests, or throwaway scripts (in a temp dir). Never edit the repo.
- Return CONCISE findings, under ~40 lines. No file dumps.
- Every finding: the claim, a receipt (file:line, or command + key output lines), and a confidence level:
  RAN IT | POINTED AT THE LINE | INFERRED (say why) | UNKNOWN (say what you searched).
- End with "Surprises" (anything that should change the plan) and "Couldn't check".
```

## Terrain scout
Map the code relevant to this idea: entry points (routes, screens, CLI commands, jobs), the modules and data shapes involved (types, DB tables and columns, API payloads), and existing patterns this feature should reuse (e.g. an existing soft-delete, pagination, or permissions helper). Point at the best example of each pattern.

## History scout
Explain WHY the relevant code is shaped the way it is. Use `git log --follow`, `git blame` on key lines, commit messages, and linked PR or issue numbers. Look for reverted attempts, deliberate workarounds, and "don't do X" comments. Return findings as Preserve / Change / Avoid / Risk candidates, each with the commit SHA as a receipt.

## Test scout
Find the existing tests around this area, how they're organised, and the exact commands to run them (unit, integration, e2e). Run the relevant subset once and report pass/fail with timing. Note coverage gaps: behaviour in this area that no test exercises.

## Blast radius scout
Paste the contents of `${CLAUDE_SKILL_DIR}/references/blast-radius.md` as the brief.

## Hypothesis scout (ad hoc)
The user believes: "<claim>". Verify or refute it against the code. Prefer running something (RAN IT) over reading. Report the verdict as CONFIRMED, REFUTED, or INCONCLUSIVE, with receipts.

## Load-bearing proof scout (ad hoc)
Prove this fact by executing real code: "<fact>". Write the smallest script or test that demonstrates it, run it, and return the command, the output, and the script contents (if short). Don't commit anything.
