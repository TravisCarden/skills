# Other Public Skills

These are my personal choices, not requirements for using this repository's skills.

## Matt Pocock's AI Skills for Real Engineers

[aihero.dev/skills](https://www.aihero.dev/skills) - "A practical skill system for engineers who want to use AI without giving up their standards." I use most of these daily, in roughly this order:

| Skill | Description |
|-------|-------------|
| [`grill-with-docs`](https://www.aihero.dev/skills-grill-with-docs) | Get interviewed about a plan, and record the decisions. |
| [`handoff`](https://www.aihero.dev/skills-handoff) | Write up a long session so another agent can continue it. |
| [`code-review`](https://www.aihero.dev/skills-code-review) | Review a diff against your standards and against the spec. |
| [`research`](https://www.aihero.dev/skills-research) | Get a cited answer, read from primary sources. |
| [`improve-codebase-architecture`](https://www.aihero.dev/skills-improve-codebase-architecture) | Find the modules worth refactoring, as a visual report. |
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
| [`ponytail`](https://ponytail.dev/) | `ponytail` can help when an agent has room to overbuild. An [independent benchmark of v4.8.4](https://blog.jetbrains.com/ai/2026/07/ponytail-skill-claude-tested/) found real savings, though it doesn't tell us how the current version performs. Subjectively, I don't see much benefit in my own work; I suspect my existing instructions and practices already cover much of the same ground. That doesn't make it redundant with `build-vs-buy`: [they address different questions and can work together](skills/build-vs-buy/README.md#compared-with-ponytail). |
| [`simple-english`](https://www.skills.sh/aminblg/simpleenglish/simple-english) | `simple-english` is a good idea, but it's also very heavy and highly opinionated. If all you want is shorter, more precise prose, check out the project's condensed [standalone system prompt](https://github.com/AminBlg/SimpleEnglish/blob/main/prompts/system-prompt.md). There's a [~60 word-budget version](https://github.com/AminBlg/SimpleEnglish/blob/main/prompts/system-prompt.md#word-budget-version-60-tokens) at the end for tight system prompts. |
| [`superpowers`](https://www.skills.sh/obra/superpowers) | I prefer Matt Pocock's skills, but it's really just a matter of preference, and if you're not already committed to an alternative, `superpowers` may work well for you. |
