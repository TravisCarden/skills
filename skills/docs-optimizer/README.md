# docs-optimizer

Clean up the repository instructions that agents actually load. Preserve correctness before reducing length.

## When to use it

Use this for stale commands, conflicting rules, repeated instructions, and unnecessary context in `AGENTS.md`, `CLAUDE.md`, rules, or related instruction files. It is not a general documentation editor.

The skill checks how instructions load before moving or merging them. A repeated rule is not redundant if removing it changes which tasks or paths receive it. If loading behavior is unknown, the skill should report that limit rather than rearrange rules on an assumption.

## Install

```sh
npx skills add https://github.com/TravisCarden/skills --skill docs-optimizer
```

## Use

Invoke the skill manually and name the files or scope. Ask for an audit to get findings without edits:

```text
/docs-optimizer Audit the repository instructions for stale facts, conflicts, and unnecessary context. Propose changes without editing.
```

Ask for fixes when you want supported changes applied:

```text
/docs-optimizer Optimize the repository instructions. Check disputed commands and paths, preserve required behavior, and fix supported problems.
```

Expect a proportional report of changes, unresolved decisions, and what was checked. Generated instructions should be fixed at their maintained source. Shorter text alone is not evidence of better model behavior or lower total cost.

## Read more

- [Skill instructions](SKILL.md)
- [10 real example responses](examples.md), including stale facts, policy conflicts, generated instructions, and loading boundaries

The examples ran without repository access or tools. Technical claims, including claims about instruction loading, are unverified.
