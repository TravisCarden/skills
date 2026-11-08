# AI Agent Skills

My personal collection of AI agent skills, shared publicly in case others find them useful. [I make no promises whatsoever.](LICENSE) You should read them before using them.

## My Skills

| Skill | Description | Resources |
|-------|-------------|-----------|
| [`advise-me`](skills/advise-me/SKILL.md) | Manually invoked advisor for any technical or business decision, from "should I try X?" to an architecture or initiative. Checks the premise, looks for ways to make the problem go away, and gives one recommendation. | [Examples](skills/advise-me/examples.md): 13 real responses |
| [`build-vs-buy`](skills/build-vs-buy/SKILL.md) | Manually invoked review for a significant decision: reuse existing code, use a platform capability, adopt a dependency or service, build custom code, or a mix. | [Examples](skills/build-vs-buy/examples.md): 12 real responses |
| [`docs-optimizer`](skills/docs-optimizer/SKILL.md) | Manually invoked review and cleanup of repository instructions for AI agents. Fixes stale facts and conflicts, removes unnecessary context, and preserves required behavior. | [Examples](skills/docs-optimizer/examples.md): 10 real responses |
| [`session-checkpoint`](skills/session-checkpoint/SKILL.md) | Manually invoked checkpoint: assess session, model, and effort changes; continue authorized work unless a user decision materially affects cost or outcome. | [Examples](skills/session-checkpoint/examples.md): 20 real responses; [Default model mapping](skills/session-checkpoint/model-mapping.md); [Overrides](#model-mapping-overrides) |

### Model mapping overrides

`session-checkpoint` includes a [default model mapping](skills/session-checkpoint/model-mapping.md). To use other models or vendors, add a short block to your existing global or project `AGENTS.md`, `CLAUDE.md`, or equivalent instructions that your agent already loads. You can also put the mapping directly in a checkpoint request. No new file or changes to the installed skill are needed.

For example, replace the values below with exact model names and vendors you can use:

```text
For session-checkpoint, replace the default model mapping with:
- Strongest: unavailable
- Strong: <vendor and exact model name for difficult diagnosis and risky decisions>
- Standard: <vendor and exact model name for bounded implementation>
- Light: <vendor and exact model name for mechanical work>
```

Use any vendors, and list alternatives when useful. The tier definitions are in [the skill](skills/session-checkpoint/SKILL.md#model-and-effort). Plain text is enough; no parser or special syntax is required.

A user mapping takes precedence over a runtime tier mapping, which takes precedence over the bundled defaults, subject to your agent's instruction rules. Each supplied mapping replaces the defaults completely, not just matching rows. Omitted or unavailable tiers stay unmapped. The skill uses a concrete model name for a change recommendation; if a material change needs an unmapped tier, it asks which available model to use. Missing mappings do not interrupt work that the current setup can reliably finish.

The mapping is a recommendation guide, not model detection, an availability check, or a promise of equal capabilities or prices. Keep your overrides current as your access and model choices change.

## My Recommendations

### Matt Pocock's AI Skills for Real Engineers

[aihero.dev/skills](https://www.aihero.dev/skills) - "A practical skill system for engineers who want to use AI without giving up their standards." I use most of these daily, in roughly this order:

| Skill | Description |
|-------|-------------|
| [`grill-with-docs`](https://www.aihero.dev/skills-grill-with-docs) | "Relentless design interrogation that stress-tests plans against your domain model and sharpens terminology inline." |
| [`handoff`](https://www.aihero.dev/skills-handoff) | "Summarize conversations into handoff documents for seamless agent-to-agent transitions." |
| [`code-review`](https://www.aihero.dev/skills-code-review) | "Two-axis review of the diff between HEAD and a fixed point the user supplies:" |
| [`research`](https://www.aihero.dev/skills-research) | "Spin up a background agent to do the research, so you keep working while it reads." |
| [`improve-codebase-architecture`](https://www.aihero.dev/skills-improve-codebase-architecture) | "Analyze codebases for architectural friction and propose module-deepening refactors as testability improvements." |
| [`retro`](https://www.aihero.dev/skills-retro) | Look back on a session and suggest repo improvements. |

### Others

| Skill | Description |
|-------|-------------|
| [`skill-creator`](https://www.skills.sh/anthropics/skills/skill-creator) | "Create, test, and iteratively improve AI agent skills with structured evaluation and benchmarking." |

### Exclusions

I specifically do *not* use these:

| Skill | Description |
|-------|-------------|
| [`caveman`](https://github.com/juliusbrussee/caveman) | `caveman` is extremely popular, but [it's claims are disputable](https://blog.jetbrains.com/ai/2026/07/speak-to-ai-agents-like-cavemen-tosave-tokens/) and I haven't personally seen much benefit--certainly not enough to justify its surprising complexity. Someone [benchmarked the whole thing](https://www.maxtaylor.me/articles/i-benchmarked-caveman-against-two-words) against the two words "be brief". |
| [`ponytail`](https://ponytail.dev/) | [Some experiments](https://www.youtube.com/watch?v=yRkD-CPcY1U) find that `ponytail` can be helpful for avoiding needless complexity. [Actual benchmarks](https://blog.jetbrains.com/ai/2026/07/ponytail-skill-claude-tested/) give a more nuanced picture. Subjectively, I don't see much benefit from it in my own work; I suspect that's because I already have mature architectures with a robust quality toolchain and thoughtful agent instructions and I know how to spot speculative complexity when I see it. It might deliver more value on less mature codebases. |
| [`simple-english`](https://www.skills.sh/aminblg/simpleenglish/simple-english) | `simple-english` is a good idea, but it's also very heavy and highly opinionated. If all you want is shorter, more precise prose, check out the project's condensed [standalone system prompt](https://github.com/AminBlg/SimpleEnglish/blob/main/prompts/system-prompt.md). There's a [~60 word-budget version](https://github.com/AminBlg/SimpleEnglish/blob/main/prompts/system-prompt.md#word-budget-version-60-tokens) at the end for tight system prompts. |
| [`superpowers`](https://www.skills.sh/obra/superpowers) | I prefer Matt Pococks skills, but it's really just a matter of preference, and if you're not already committed to an alternative, `superpowers` may work well for you. |
