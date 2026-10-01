# Spec review checklist

You are reviewing a feature spec with fresh eyes. You have only the spec file and this checklist. Do not edit the spec. You may read the repo to spot-check facts.

Return findings as a list: `[section] problem → suggested fix`. Put mechanical failures first, then judgment issues. If a section is fine, don't mention it.

## Part A: Mechanical checks (each one is pass or fail)

1. A `status:` line is present, and its value is one of `draft | ready | building | shipped`.
2. The header has `repo:`, `created:`, and `base_sha:`.
3. All nine numbered sections are present, in order, and none is empty or still holds only template comments. ("None." is acceptable for Open questions.)
4. Every fact (F#) has a Receipt, a Checked line (SHA + date), and a Confidence line with one of the four levels.
5. At least one fact is marked load-bearing, and every load-bearing fact is RAN IT with a command and output, or is listed in Open questions.
6. Every goal (G#) has a `You see:` line.
7. Every goal (G#) appears in the Proof table.
8. Every invariant (I#) has an `Enforced by:` line.
9. Every invariant (I#) appears in the Proof table.
10. The Blast radius section has both a Confirmed risks and a Cleared risks subsection.
11. Every confirmed risk (R#) appears in the Proof table.
12. Every Proof row says how to run it.

## Part B: Judgment checks

1. **Goals are checkable.** Could a stranger decide pass or fail from the goal and its You-see line alone? Flag vague goals ("improve", "better", "clean up").
2. **Non-goals are missing.** What would an eager builder do that isn't ruled out? (Feature flags, refactoring neighbours, touching the other client, extra settings, migrating old data, adding analytics.)
3. **Facts are weak.** Claims stated as certain but only INFERRED; receipts that don't actually support the claim; facts about other systems with no receipt. Spot-check two or three against the repo.
4. **Proof is real.** Does each check actually prove its goal, invariant, or risk, or just something nearby?
5. **The blast radius search was thorough.** Did it consider shared data shapes, other consumers (apps, services, jobs, analytics), feature flags, and code several hops downstream? Or is it just a caller list? Name specific places that were likely missed.
6. **HOW is leaking in.** Flag migration numbers, file lists to create, new function names, step-by-step plans, and code to write. These go stale. Suggest rewriting them as outcomes or constraints.
7. **The root cause is stated.** Does Starting point explain the cause, or only the symptom?
8. **Stop-and-ask is concrete.** Are risky decisions (schema, public API, deleting data, spend) routed back to the user?
9. **Hidden guesses.** Anything answered confidently that should be an Open question.

End with a one-line verdict: READY, READY WITH FIXES, or NOT READY.
