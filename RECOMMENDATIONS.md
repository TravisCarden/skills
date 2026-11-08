# Other Public Skills

These are my personal choices, not requirements for using this repository's skills.

## Matt Pocock's AI Skills for Real Engineers

[aihero.dev/skills](https://www.aihero.dev/skills) - "A practical skill system for engineers who want to use AI without giving up their standards." I use most of these daily, in roughly this order:

| Skill | Description |
|-------|-------------|
| [`grill-with-docs`](https://www.aihero.dev/skills-grill-with-docs) | "Relentless design interrogation that stress-tests plans against your domain model and sharpens terminology inline." |
| [`handoff`](https://www.aihero.dev/skills-handoff) | "Summarize conversations into handoff documents for seamless agent-to-agent transitions." |
| [`code-review`](https://www.aihero.dev/skills-code-review) | "Two-axis review of the diff between HEAD and a fixed point the user supplies:" |
| [`research`](https://www.aihero.dev/skills-research) | "Spin up a background agent to do the research, so you keep working while it reads." |
| [`improve-codebase-architecture`](https://www.aihero.dev/skills-improve-codebase-architecture) | "Analyze codebases for architectural friction and propose module-deepening refactors as testability improvements." |
| [`retro`](https://www.aihero.dev/skills-retro) | Look back on a session and suggest repo improvements. |

## Others

| Skill | Description |
|-------|-------------|
| [`skill-creator`](https://www.skills.sh/anthropics/skills/skill-creator) | "Create, test, and iteratively improve AI agent skills with structured evaluation and benchmarking." |

## Exclusions

I specifically do *not* use these:

| Skill | Description |
|-------|-------------|
| [`caveman`](https://github.com/juliusbrussee/caveman) | `caveman` is extremely popular, but [its claims are disputable](https://blog.jetbrains.com/ai/2026/07/speak-to-ai-agents-like-cavemen-tosave-tokens/) and I haven't personally seen much benefit--certainly not enough to justify its surprising complexity. Someone [benchmarked the whole thing](https://www.maxtaylor.me/articles/i-benchmarked-caveman-against-two-words) against the two words "be brief". |
| [`ponytail`](https://ponytail.dev/) | [Some experiments](https://www.youtube.com/watch?v=yRkD-CPcY1U) find that `ponytail` can be helpful for avoiding needless complexity. [Actual benchmarks](https://blog.jetbrains.com/ai/2026/07/ponytail-skill-claude-tested/) give a more nuanced picture. Subjectively, I don't see much benefit from it in my own work; I suspect that's because I already have mature architectures with a robust quality toolchain and thoughtful agent instructions and I know how to spot speculative complexity when I see it. It might deliver more value on less mature codebases. |
| [`simple-english`](https://www.skills.sh/aminblg/simpleenglish/simple-english) | `simple-english` is a good idea, but it's also very heavy and highly opinionated. If all you want is shorter, more precise prose, check out the project's condensed [standalone system prompt](https://github.com/AminBlg/SimpleEnglish/blob/main/prompts/system-prompt.md). There's a [~60 word-budget version](https://github.com/AminBlg/SimpleEnglish/blob/main/prompts/system-prompt.md#word-budget-version-60-tokens) at the end for tight system prompts. |
| [`superpowers`](https://www.skills.sh/obra/superpowers) | I prefer Matt Pocock's skills, but it's really just a matter of preference, and if you're not already committed to an alternative, `superpowers` may work well for you. |
