# review-focus

Decide whether a pull request is ready for your review, then find the few places where only a human's context or judgment can help.

## When to use it

Use this before spending review time on a pull request, including agent-written work or a reviewer guide you are preparing. It does not review the code.

It first says whether the PR is ready for a human: failing CI, draft status, unaddressed requested changes, or ongoing rework mean it is not, and it stops. A draft whose author asks for early feedback counts as ready for that feedback. If a check it depends on exists but can't be read, the verdict is unclear and it says how to get the result. If ready, it points you at what an agent cannot verify: intent, context, unexplained error suppression, author doubt, and policy choices. It deprioritizes mechanical changes and well-tested code, and lists unsettled technical questions as checks to delegate. It reads existing PR data and results, using available tools or skills, but does not run checks unless you ask.

If your CI needs a special tool or skill to read, such as a Jenkins skill, say so in the repository's `AGENTS.md` or in your request. To skip the readiness gate, say so when you invoke it or after the verdict; it will not argue readiness again.

## Install

```sh
npx skills add https://github.com/TravisCarden/skills --skill review-focus
```

## Use

Provide the PR and what you know or own:

```text
/review-focus https://github.com/example/repo/pull/123 I know Git and bash well, but not this codebase. The author is a trusted lead.
```

Expect a readiness verdict first. If ready, you get **Your attention**: what to read or decide, with the location, what to look for, and why it needs you. Then **Ask the author**, any **Checks to delegate**, and a short **Skip** list. It does not recommend approval or call the PR safe, because it has not reviewed the code.

## Read more

- [Skill instructions](SKILL.md)
- [Example responses](examples.md) (7 responses)
