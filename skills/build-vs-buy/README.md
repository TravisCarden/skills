# build-vs-buy

Choose the solution with the least complexity your project must own over its lifetime—not necessarily the fewest lines of code.

## When to use it

Use this before adding a dependency or service, replacing a component, or building functionality that an existing solution might handle. It compares local code, platform features, existing dependencies, new solutions, and custom code. A mix can be the best choice.

For broader questions about whether the problem needs solving at all, see [advise-me](../advise-me/README.md).

## Use

Invoke the skill manually with the requirement, constraints, and relevant project context:

```text
/build-vs-buy We need to validate signed webhook payloads. Should we use the framework's built-in support, add a library, or write the verification ourselves?
```

Small, reversible choices get a short comparison. Consequential choices get a deeper check of actual behavior, licensing, maintenance, dependencies, lock-in, and replacement cost. The skill reads project evidence and uses primary sources. Deep checks include an independent challenge when subagents are available, or a case for the runner-up otherwise.

Expect a recommendation that separates what the project owns from what it delegates, names the main trade-off, and states what would reopen the decision. Unverified facts should remain labeled.

A recommendation does not authorize adding dependencies, adopting services, or changing scope. The optional `research` skill is not required; the agent can research directly.

## Read more

- [Skill instructions](SKILL.md)
- [12 real example responses](examples.md), covering built-ins, existing dependencies, standardized problems, and costly commitments

The examples ran without tools, research, or independent review. Their technical claims are unverified; they illustrate the decision process, not completed evaluations of the named options.
