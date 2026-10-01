---
name: idea-to-spec
description: Turn a rough feature idea into a build-ready spec (WHAT and WHY, never HOW) through a one-question-at-a-time interview while background scouts investigate the codebase. Use whenever the user says "spec this out", "write a spec", "let's plan a feature", "I want to build X", "idea to spec", describes a feature they're thinking about, or wants to scope work before building, even if they don't say "spec".
argument-hint: "[rough idea: what and why]"
---

# Idea to spec

Turn a rough idea into a spec a builder agent can pick up later, possibly weeks later, against a codebase that has moved on.

## Principles (apply to every phase)

1. **What and why, not how.** If a detail would go stale within days (migration numbers, file lists, function names to create, slugs), it does not belong in the spec. The builder works out the how at build time.
2. **The user never writes the spec.** The interview produces it. The user only answers questions.
3. **Never accept an unverified fact.** Every fact has a receipt, or it is marked INFERRED or UNKNOWN. This includes the user's own claims about the code.
4. **Never block on the user for anything a scout can find out.** Ask only about product decisions and preferences that no code read or command can settle.
5. **A list of callers is not a blast radius check.** Look for shared data, other consumers, and downstream effects.

## Before you start

- If the session is not on the strongest model, tell the user once: "For best spec quality, run `/model fable` (or `/model opus`) first." Then continue; don't wait.
- Record the setup facts yourself (cheap, no scout needed): repo name (`basename $(git rev-parse --show-toplevel)`), base SHA (`git rev-parse HEAD`), today's date, and the spec path `~/specs/<repo>/YYYY-MM-DD-<feature-slug>.md`.
- Create the spec file right away with `status: draft` and the empty sections from `references/spec-template.md`. Fill it in as you go, so nothing is lost if the session dies.

## Scouts

Scouts are subagents that read code and run commands so you don't have to. Dispatch them with the Agent tool:

- `subagent_type: "general-purpose"`, `model: "sonnet"`, `run_in_background: true`
- Prompt: the shared preamble plus the matching brief from `references/scout-prompts.md`, the idea, the base SHA, and the specific question. Paste the brief text in. Scouts can't see this skill's files unless you give them the absolute path (`${CLAUDE_SKILL_DIR}/references/...`).
- Every scout returns concise findings with `file:line` pointers and a confidence level (see `references/confidence-levels.md`), never raw file dumps.
- Scouts may run read-only commands, tests, and throwaway scripts in a temp dir. They never edit the repo.

Keep scouts running for the whole session. You are notified when each one finishes: fold its findings into the next question and into the spec's Facts section.

## Phase 1: Kickoff

The user gives the high-level what and why (from the arguments or their message). In the **same turn**, launch three scouts in parallel (a single message with three Agent calls):

- **Terrain scout:** relevant code, existing patterns, entry points, data shapes.
- **History scout:** git log, blame, and commit messages that explain WHY the existing code is shaped the way it is.
- **Test scout:** existing tests, coverage, and the exact commands to run them.

Then ask the first interview question immediately. Do not wait for the scouts.

## Phase 2: Relentless interview

- Ask **one question at a time**. Each question carries a recommended answer with one or two lines of reasoning, so the user can reply "yes" or correct it. Format:

  > **Q3: Should restored notes keep their original folder?**
  > Recommended: yes. The terrain scout found that folder_id survives soft-delete (`notes/model.ts:88`), so restoring into the original folder is free.

- Fold scout findings into questions as they arrive ("There's already a soft-delete pattern in X. Reuse it?").
- When an answer opens a new area, dispatch a new scout for it.
- Treat the user's statements about the code as hypotheses. "I think we already cache that" means: dispatch a scout to check, then keep interviewing.
- If a quick command or test could answer a question, have a scout run it instead of asking.
- Keep a running list of alternatives the user rejects, and why (for Alternatives rejected).
- Stop only when no meaningful ambiguity remains about what is being built, for whom, what done looks like, and what must not change. If you are unsure whether to stop, ask one more question.

## Phase 3: Alignment check

Play back a concise summary: the problem and its root cause, the goals, the non-goals, the key decisions, and what the scouts found that shaped them. Ask "Is this right?" **Do not continue until the user explicitly confirms.** Corrections send you back to Phase 2 for the affected area.

## Phase 4: Define done

- **Goals:** outcomes someone else could check. Bad: "improve the trash." Good: "A note deleted from web or mobile goes to trash and can be restored within 30 days."
- Every goal gets a **You see:** line with the exact observable result (a log line, a screen state, an API response).
- **Non-goals:** things an eager builder would otherwise do on its own: add feature flags, touch the other app, refactor nearby code, add extras. A good non-goal is something the agent WOULD have done if not told.

## Phase 5: Facts and blast radius

Facts from stale memory are the #1 source of bugs in specs, so this phase is strict.

- Every fact has a **receipt** (`file:line`, command plus output, or log line), the **SHA** it was checked against, the **date**, and a **confidence level**: RAN IT / POINTED AT THE LINE / INFERRED / UNKNOWN. See `references/confidence-levels.md`.
- Identify the one or two **load-bearing facts** the feature's safety depends on. Have a scout prove each one by running real code (a small script or a test), and record the command and output. A citation is not enough for these.
- Dispatch the **blast radius scout** with `references/blast-radius.md`. It records confirmed risks (how it breaks, `file:line`, likelihood, impact, how to check) and cleared risks (what was checked and why it's fine).
- Confirmed risks feed into Constraints and Proof.

## Phase 6: Constraints (what must stay true)

- **Invariants:** cite existing ones rather than copying them, and flag any that are new or changed. State what enforces each one (a test, a type, a DB constraint). If nothing enforces one, say so and add a Proof check for it.
- **Preserve / Change / Avoid / Risk:** built from the history scout's findings, so the builder doesn't "fix" something that was deliberate.
- **Backwards compatibility:** old clients and existing users and data (e.g. add the new endpoint; don't delete the old one).
- **Stopping rules:** retry limits and spend caps (e.g. max 3 attempts per failing check, max $20/day API spend). Ask the user if unknown.
- **Stop and ask:** which decisions the builder must bring back to the user (e.g. schema changes) and which it decides alone (e.g. naming).

## Phase 7: Proof

Every goal, every invariant, and every confirmed blast-radius risk gets a check: a unit test, an integration test, or an agentic end-to-end flow (e.g. "delete on mobile, restore on web"). Each check says what it proves and how to run it, using the test scout's commands. Include the rule: if a check fails repeatedly during the build (more than the stopping-rule limit), the builder stops and asks.

## Phase 8: Wrap-up

- **Alternatives rejected:** approaches discussed in the interview and why they lost, so a future builder doesn't revisit dead ends.
- **Open questions:** everything unresolved or unverifiable, listed explicitly. Never guess quietly or omit.

## Phase 9: Self-review

1. Re-read the spec yourself against the **mechanical checks** in `references/review-checklist.md` and fix every failure.
2. Dispatch a **fresh-context reviewer**: `subagent_type: "general-purpose"`, `model: "opus"`, foreground. Give it ONLY the spec path and the checklist path (`${CLAUDE_SKILL_DIR}/references/review-checklist.md`), never your interview context. It must not edit the spec.
3. Sort each finding: **fix it** (edit the spec), **move it to Open questions** (needs the user or can't be verified), or **dismiss it** (note why in one line to the user).
4. Set `status: ready` and save.
5. Tell the user: the spec path, a five-line summary (problem, goals, the riskiest fact and its proof, open-question count), and anything you dismissed from the review.

## Status lifecycle

`draft` (interview in progress) → `ready` (reviewed, buildable) → `building` (a builder picked it up) → `shipped`. The `~/specs/` folder doubles as a cross-project backlog: `grep -l "status: ready" ~/specs/*/*.md`.
