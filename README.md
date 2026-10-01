# idea-to-spec

A Claude Code skill that turns a rough feature idea into a build-ready spec: a one-question-at-a-time interview while background scout subagents investigate the codebase. Specs capture the WHAT and WHY, never step-by-step HOW, and every fact carries a receipt and a confidence level.

Specs are saved outside the repo at `~/specs/<repo>/YYYY-MM-DD-<slug>.md`, with `status: draft | ready | building | shipped`.

## Install

```bash
git clone https://github.com/Vaughan-g-aus/idea-to-spec.git ~/idea-to-spec
mkdir -p "${CLAUDE_CONFIG_DIR:-$HOME/.claude}/skills"
ln -s ~/idea-to-spec/skills/idea-to-spec "${CLAUDE_CONFIG_DIR:-$HOME/.claude}/skills/idea-to-spec"
```

Repeat the `ln` for each profile. Run `git pull` in `~/idea-to-spec` to update every profile at once.

## Use

```
/model fable
/idea-to-spec Let users restore deleted notes for 30 days, because support gets ~20 "I deleted it by accident" emails a week
```

Or say "spec this out", "I want to build X", and so on.

## Models

- Interviewer and spec writer: whatever the session runs. Use `/model fable` (or opus). A skill's `model:` frontmatter only lasts one turn, so it isn't set.
- Scouts: `sonnet`, in the background, set per Agent dispatch.
- Fresh-context reviewer: `opus`.

## Credits

The reviewer-dispatch and verdict patterns are adapted from [cursor/plugins pstack](https://github.com/cursor/plugins/tree/main/pstack) (MIT, Lauren Tan).
