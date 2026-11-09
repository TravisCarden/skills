# AI Agent Skills

My personal collection of agent skills, shared publicly in case others find them useful. [I make no promises whatsoever.](LICENSE) Read a skill's instructions before using it.

## My Skills

| Skill | Use it to |
|---|---|
| [`advise-me`](skills/advise-me/README.md) | Check the premise of a technical or business decision and get one recommendation. |
| [`build-vs-buy`](skills/build-vs-buy/README.md) | Choose between reuse, built-ins, dependencies, services, and custom code. |
| [`docs-optimizer`](skills/docs-optimizer/README.md) | Fix stale, conflicting, or unnecessary instructions that agents load. |
| [`session-checkpoint`](skills/session-checkpoint/README.md) | Decide whether a session, model, or effort change is worthwhile before work or at task completion. |

All skills support manual invocation. `session-checkpoint` also supports model invocation for material transition decisions. Each skill page explains when to use it and links to its instructions and real example responses.

## Install

Install all the skills:

```sh
npx skills add https://github.com/TravisCarden/skills
```

To install one skill, use the command on its page.

## Other Public Skills

See [my recommendations and exclusions](RECOMMENDATIONS.md) for skills from other authors.
