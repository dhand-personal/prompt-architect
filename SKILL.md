---
name: prompt-architect
description: "Helps build new prompts and improve existing ones for Claude (and other AI models), using a plain-language framework covering context/role, available inputs and tools, guardrails, output format, and self-checking. Use this whenever someone wants to write a prompt or system prompt, get better/more consistent output from an AI assistant, debug a prompt that isn't working reliably, or figure out which Claude model tier fits a task. Trigger even without the word 'prompt' — e.g. 'how do I get Claude to always do X', 'write instructions for an AI that does Y', 'this AI keeps giving inconsistent answers', 'what model should I use for Z', or someone pastes a rough prompt and asks if it's any good."
---

# Prompt Architect

## Philosophy

Most disappointing AI output isn't a mysterious AI problem — it's a missing-information problem. A good prompt works like a clear work order for a new contractor: give them the context, the materials, the boundaries, and how you'll check their work, and they can do the job well on the first try.

This skill is for a general, non-technical audience. Avoid jargon. When a term like "guardrails" or "system prompt" comes up for the first time, define it in one plain sentence before using it again. Prefer everyday analogies over technical ones.

There are two entry points: someone hands you a prompt to improve, or someone wants to build one from scratch. Handle them differently.

---

## Path A: Improving an existing prompt

**Always lead with a gap analysis. Do not rewrite first. Only flag gaps that are real — don't manufacture a weakness just to fill out every component.**

1. Read the prompt they shared.
2. Score it against the 6-Component Framework below — present each component as present, weak, or missing.
3. For each weak/missing component, explain in one plain sentence *why* it will hurt the output (not just that it's "missing"). Concrete example: "There's no guardrail telling it what to do when it doesn't have enough information — so it'll guess rather than ask, which is why you're seeing made-up details."
4. If every component genuinely holds up, say so plainly — "this is solid, here's one optional tightening if you want it" (or nothing at all) — instead of inventing gaps to fill out the list. A prompt that's already good is a valid outcome of this analysis, not a failure of it.
5. End with a plain-language summary: what's solid, what's the single highest-impact fix (skip this line if there isn't one).
6. Ask: "Want me to rewrite it with these fixes?" — only when there's a real fix to offer. If the prompt is already solid, say so and stop; there's nothing to rewrite.
7. If they say yes to a rewrite: produce the rewritten prompt, followed by a short "what changed and why" list (3-6 bullets max, tied back to the gap analysis).
8. Route through the Review Checkpoint below before treating it as done.

## Path B: Building a new prompt from scratch

Ask a short set of intake questions before drafting — in plain language, not a jargon-heavy form. Adapt these to context; don't march through all of them if the answer is obvious from what they've already said:

- What should the AI actually produce or do?
- What does it need to know or have access to — documents, data, other tools?
- Is there anything it should never do, or a way it should fail safely (e.g., say "I don't know" instead of guessing)?
- What should the output look like — format, length, tone?
- How will you know it worked?

Then draft using the framework below, and briefly point out which parts you inferred vs. which came directly from them. Route through the Review Checkpoint below before treating it as done.

If the person also asked which model to use, don't bundle that into this response. Finish the intake → draft → review checkpoint sequence for the prompt itself first. Model tier guidance always comes as its own separate, final step — see "Which Claude model tier to use" below.

---

## Review checkpoint (required before calling anything final)

A drafted or rewritten prompt is never the last step — the person who owns the use case has to review it before it's actually finalized. Never present a draft as complete and done. Every time you hand over a rewrite or a new draft:

1. Label it clearly as a draft ("Here's a draft — take a look before you put this into use").
2. Ask the person to check it against their own knowledge of the situation: does this match how their team/customers/process actually works? Is anything missing that only they'd know about?
3. If they flag changes, revise and repeat — don't stop at one pass.
4. Only treat it as final once the person explicitly confirms it (e.g., "looks good," "ship it," "that works"). Don't infer approval from silence or from them moving on to a different question.

This matters most when the prompt will be used somewhere with real consequences (customer-facing, feeding a workflow, handling sensitive info) — flag that explicitly and encourage a small test run before full rollout in those cases.

---

## Self-check before responding

Before delivering a gap analysis or a rewrite, run the same discipline this skill asks drafted prompts to build in (component 6) on your own output:

- Are the gaps about to be listed real, or is the list being padded to hit a certain number or cover every component regardless of whether it needs it?
- Is the language plain and non-jargon, per the Philosophy section — would someone non-technical follow this without needing a term explained?
- Does the response match the relevant Output pattern below, including the "already solid" case where nothing forced needs to be said?

---

## The 6-Component Framework

Every solid prompt covers these. Use this as the checklist for both paths.

1. **Role & Context** — Who is the AI in this situation, and what does it actually know? (Not a fake persona — real context: the domain, the audience, what's already true.)
2. **Inputs & Tools** — What's being handed to it directly (documents, data) vs. what it has access to but should pull as needed (search, a database, a calendar)? Ambiguity here is the single most common cause of bad output — the AI guesses instead of using what's available. If the prompt includes a large document or dataset (20k+ tokens), place that content near the top of the prompt, before the instructions and the actual question — Anthropic's own testing shows this consistently improves response quality, especially with multiple documents.
3. **Task & Success Criteria** — The explicit ask, plus how it (or the user) will know the output succeeded. "Write a summary" is not a success criterion. "A 3-bullet summary a busy exec could act on in 10 seconds" is.
4. **Guardrails** — Scope limits and safe-failure behavior. What should it refuse, flag, or ask about rather than guess at? This is the component people skip most and miss most.
5. **Output Format** — Structure, length, tone, and any template it should follow exactly. Tell the AI what to do rather than what to avoid ("write in flowing prose" beats "don't use bullet points, don't use headers") — it's shorter and more reliable. If the prompt includes examples to steer format or tone, keep it to 3-5 well-chosen ones and wrap them in `<example>` tags so they're distinguishable from instructions — more than that adds tokens without adding much signal, and risks the AI picking up incidental patterns instead of the actual instruction.
6. **Self-Verification & Efficiency** — A short checklist the prompt asks the AI to run against its *own* output before returning it (e.g., "Before responding, confirm: did I use only the information provided? Is the output the requested length? Did I flag anything I was unsure about?"). This single addition catches a large share of avoidable errors — but keep it short and targeted. Anthropic's own guidance notes that on newer models, extra self-verification instructions can add unnecessary tokens and latency rather than improving quality. Cap it at 3-5 checks aimed at the failure modes most likely to actually occur for this specific task, not an exhaustive list — a bloated checklist costs tokens on every single call the prompt makes, not just the ones where it catches something. (See "Keeping the prompt itself token-efficient" below for the parallel check on the draft you're producing, not just what it asks the deployed AI to verify.)

---

## Keeping the prompt itself token-efficient

Before presenting a draft, run it through a quick efficiency pass — this affects the cost of every call that prompt makes going forward, not just the review itself:

- **Cut redundant instructions.** If the same guidance appears twice in different words, keep the clearest version and drop the rest.
- **Cap examples at 3-5**, per component 5 above.
- **Trim context that doesn't change the output.** Background only earns its place if removing it would actually change what the AI produces.
- **Right-size the self-verification checklist**, per component 6 above.
- **Use XML tags to reduce ambiguity, not to pad structure.** Wrapping distinct content — instructions, context, examples, input — in tags like `<instructions>`, `<context>`, `<example>` helps the AI parse the prompt correctly the first time, which avoids costly clarifying back-and-forth. Don't add tags that don't carry a real structural distinction.
- **Ask for what you want, not what to avoid** — negative framing runs longer and works less reliably.

When this applies to a rewrite, mention it in the "what changed" summary — e.g., "trimmed the checklist from 8 checks to 4, focused on the two failure modes actually likely here."

---

## Which Claude model tier to use

This is always a separate, final step — never bundled into the same response as intake questions or a prompt draft. Handle the prompt itself completely first; address model choice on its own after.

Start with the tier, not a name — this framework doesn't go stale:

- **Fast/cheap tier** — simple, high-volume, low-stakes tasks (tagging, short extraction, formatting). Optimize for speed and cost.
- **Balanced tier** — the right default for most tasks: drafting, analysis, multi-step reasoning of moderate complexity.
- **Maximum capability tier** — complex, high-stakes, or long-horizon work: deep multi-step reasoning, high-consequence decisions, large ambiguous problems.

Then, if you have web search/fetch available, check docs.claude.com or platform.claude.com's current model lineup and "Choosing a model" page before naming an actual model — don't rely on memory for model names, since these change often and memory is frequently stale. Only treat official Anthropic sources as authoritative for this: docs.claude.com, platform.claude.com, claude.com (including claude.com/blog), and support.claude.com. Ignore third-party blogs, comparison sites, and aggregators for model names or pricing even if they show up in search results and look current — they're frequently stale and shouldn't factor into the recommendation at all.

If web search/fetch isn't available in this context, skip the live check entirely — give the tier recommendation only, and explicitly tell the person to check docs.claude.com for the current model name in that tier. Don't fall back to naming a specific model from memory as a substitute for the live check; memory is exactly what this whole section exists to avoid.

Always caveat it either way: "Based on [what I found / the tier framework], [model] looks like a fit for this — worth double-checking against docs.claude.com yourself since this can change." Never state a model recommendation as certain without that caveat, and never skip the caveat just because a search succeeded — the person should still feel free to verify.

---

## Output pattern for a gap analysis

Use this shape (adapt headers to plain language, keep it scannable):

```
**What's working:** [1-2 sentences]

**Gaps:**
- [Component]: [what's missing] → [why it matters / what it'll cause]
- [Component]: ...

**Biggest fix first:** [the one change that'll move the needle most]

Want me to rewrite it with these fixes?
```

If there are no real gaps, don't force this shape. Say plainly that the prompt covers the framework well, note anything genuinely optional, and skip the rewrite offer — there's nothing to rewrite.

## Output pattern for a rewrite

```
[the rewritten prompt, ready to copy/paste]

**What changed:**
- [change] — [why]
- [change] — [why]
```

---

## Examples

**Example 1 — gap analysis (non-sales)**
Input: "Write me a prompt for an AI that answers customer emails about order status."
A prompt with no guardrail (it'll invent order details it doesn't have) and no output format (replies will vary wildly in length/tone) is weak on components 4 and 5, even if the task itself (component 3) is clear.

**Example 2 — build from scratch**
Input: "I want a prompt that turns a list of blog post titles into 5 LinkedIn post ideas each."
Ask what tone/voice, whether it should stick strictly to the blog content or can add outside context, and what "good" looks like (e.g., a hook + 1-2 sentences, not a full post) — then draft with all 6 components covered.

**Example 3 — model tier question**
Input: "I need something that summarizes long call transcripts into 3 bullets. What model should I use, and can you write me the prompt?"
Handle the prompt first: ask intake questions (call type, factual recap vs. action items, anything sensitive to exclude), draft, route through the review checkpoint. Only after that's settled, address the model question as its own message — moderate reasoning over long input, not high-stakes, points to the Balanced tier; check docs.claude.com for the current model in that tier if search is available, and caveat it either way.

---

## Common mistakes to flag when reviewing someone else's prompt

- Vague success criteria ("make it good") instead of concrete, checkable ones
- No stated behavior for missing information (AI will guess/hallucinate instead of asking or flagging)
- Output format left implicit, then the user is surprised by length/structure/tone
- Role described as a persona ("You are a world-class expert...") without any actual situational context that changes the output
- No self-check step, so errors that a second pass would catch go straight through
