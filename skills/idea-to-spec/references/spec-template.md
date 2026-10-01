# Spec template

Copy this structure verbatim. Keep the headings exactly as written (reviewers and future tools look for them). Guidance in `<!-- -->` comments is deleted once a section is filled.

```markdown
# <Feature title>

status: draft
repo: <repo name>
created: YYYY-MM-DD
base_sha: <full SHA of HEAD when the interview started>

## 1. Starting point
<!-- The WHY. State the root cause, not the symptom. Paste raw evidence verbatim: log lines,
     error output, support emails, screenshot paths. Who is affected and how often. -->

## 2. Goals
<!-- Outcomes someone else could check. Number them G1, G2...
     Each goal has a "You see:" line with the exact observable result. -->

- **G1:** A note deleted from web or mobile goes to trash and can be restored within 30 days.
  - You see: `GET /trash` lists the note with `deleted_at`; after restore, `GET /notes/:id` returns 200.

## 3. Non-goals
<!-- Things an eager builder WOULD do if not told. One line each, with a short reason. -->

## 4. Facts
<!-- Number them F1, F2... Mark load-bearing facts and include their proof (command + output).
     Levels: RAN IT | POINTED AT THE LINE | INFERRED | UNKNOWN (see confidence-levels.md). -->

- **F1 (load-bearing):** <claim>
  - Receipt: <file:line | command + output | log line>
  - Checked: <sha> on YYYY-MM-DD
  - Confidence: RAN IT
  - Proof: `<command>` → `<output>`

## 5. Blast radius
### Confirmed risks
<!-- Number them R1, R2... -->
- **R1:** <what breaks>
  - How it breaks: ...
  - Where: <file:line>
  - Likelihood / impact: <low|med|high> / <low|med|high>
  - How to check: ...

### Cleared risks
<!-- What was checked and why it's fine. Shows the search was thorough. -->
- <area checked>: <what was searched / run> → <why it's fine>

## 6. Constraints
### Invariants
<!-- Number them I1, I2... Cite the existing source rather than copying it. Mark NEW or CHANGED. -->
- **I1:** <invariant> (source: file:line)
  - Enforced by: <test name | type | DB constraint | NOTHING (Proof check added)>

### Preserve / Change / Avoid / Risk
<!-- From the history scout. Why the code is shaped this way, so the builder doesn't "fix" deliberate choices. -->
- Preserve: ...
- Change: ...
- Avoid: ...
- Risk: ...

### Backwards compatibility
<!-- Old clients, existing users and data, stored formats. -->

### Stopping rules
<!-- e.g. max 3 attempts per failing check; max $20/day API spend. -->

### Stop and ask
<!-- Bring back to the user: ... / Decide alone: ... -->

## 7. Proof
<!-- One check per goal, invariant, and confirmed risk. Reference the IDs.
     Type: unit | integration | e2e (agentic flow). Include how to run it. -->

| Covers | Check | Type | How to run |
|---|---|---|---|
| G1 | Delete on mobile, restore on web, note reappears | e2e | ... |

If a check fails more often than the stopping rules allow, stop and ask the user. Do not keep retrying.

## 8. Alternatives rejected
<!-- Approach → why it lost. -->

## 9. Open questions
<!-- Everything unresolved or unverifiable. Write "None." only if that is true. -->
```

## What does NOT belong in a spec

Migration numbers, lists of files to create or edit, new function or class names, branch names, step-by-step implementation plans, and line-by-line code. These go stale before a builder picks the spec up. Cite existing code as evidence (Facts, Invariants); don't prescribe new code.
