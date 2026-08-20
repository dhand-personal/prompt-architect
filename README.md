# Prompt Architect

A Claude Skill that helps you build new prompts or improve existing ones — for a
non-technical audience, using a plain-language framework covering context, inputs
and tools, guardrails, output format, and self-verification.

It leads with a gap analysis on existing prompts (not an immediate rewrite), asks
intake questions before drafting new ones, checks Anthropic's official docs before
recommending a model, and audits its own drafts for token bloat before handing them
over.

## Installation

### Claude.ai, Claude Desktop, and Cowork

1. Download this repository as a zip: **Code → Download ZIP** on this page (or
   `git clone` it).
2. In Claude, go to **Settings → Capabilities → Skills → Upload skill**.
3. Select the downloaded zip. `SKILL.md` is at the root of this repo, so no
   repackaging is needed.
4. Enable the skill.

This upload is per-account. If you're on a Team or Enterprise plan, you can share
it with your organization from the same settings screen instead of everyone
uploading individually.

### Claude Code

```bash
git clone https://github.com/dhand-personal/prompt-architect.git ~/.claude/skills/prompt-architect
```

Or, for just this repo/project:

```bash
git clone https://github.com/dhand-personal/prompt-architect.git .claude/skills/prompt-architect
```

Restart your session after installing.

## Usage

Ask Claude to review or build a prompt, directly:

```
Can you look at this prompt and tell me if it's any good?
[paste your prompt]
```

```
I want a prompt for an AI that [does X]. Can you help me build it?
```

```
What Claude model should I use for [task]?
```

The skill triggers automatically on requests like these — no special syntax needed.

## How it works

Every prompt is checked against six components: Role & Context, Inputs & Tools,
Task & Success Criteria, Guardrails, Output Format, and Self-Verification &
Efficiency. See [SKILL.md](./SKILL.md) for the full framework and behavior rules.

Test cases covering the main behaviors — gap analysis, new-build intake, model-tier
sequencing, and the token-efficiency check — are in [evals/evals.json](./evals/evals.json).

## Version history

- **1.0.0** — Initial release. 6-component framework, gap-analysis-first review
  path, build-from-scratch intake path, review checkpoint before treating any
  draft as final, live official-docs check for model-tier recommendations, and a
  token-efficiency pass on both drafted prompts and the skill's own self-verification
  checklists.

## License

MIT
