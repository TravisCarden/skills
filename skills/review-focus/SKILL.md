---
name: review-focus
description: "Manually invoked pull-request attention guide. Use before spending human review time on a pull request. Says first whether the PR is ready for a human, then points the reviewer at what only a human can settle: intent, context, and judgment calls. Reads the PR, its discussion, CI results, and diff; does not run checks or review the code."
disable-model-invocation: true
user-invocable: true
---

# Review Focus

Decide whether a human's review time is worth spending on this pull request now, and if so, where. This skill does not review the code or verify claims.

## Limits

Read what already exists: PR description, comments, review threads, commits, CI status and results, and the diff. Fetching existing results through any available tool or skill is fine. Do not run builds, tests, scripts, or reproductions, trigger new CI runs, or spawn subagents unless the user explicitly asks. Do not push, post, or change anything.

If a relevant result exists but you cannot read it, such as a status check whose details need access you lack, do not treat it as passing. Name the source and how the user could get it to you.

## Ready?

Judge from signals in the PR. Not ready when:

- CI or tests fail in a way that implies rework, not a flaky run. Judge from the existing logs and history; do not rerun to find out.
- It is a draft, marked WIP, or the author says it is unfinished.
- Requested changes are unaddressed, or an agent or human review found a defect whose fix will change what the reviewer needs to read. A small fix that doesn't, such as a typo or a missing null check, is a one-line concern, not a stop. A safety, security, data-integrity, or compliance defect stops regardless of fix size.
- The author is evidently still changing the code: an announced rework, or code changes pushed after a review with no reply to it. Commits that touch only docs, formatting, or typos are not churn.

CI that passed on an older commit than the head does not count for the head. If a signal the verdict depends on is missing or unreadable, the verdict is unclear: say which signal and how to get it.

If the author asks for early feedback on a draft, it is ready for that feedback; focus on the approach.

Stop only on clear evidence. For weak or mixed signals, call it ready and note the concern in one line. A review finding the author disputed without evidence is a mixed signal; name it in the verdict line.

Passing CI counts only if it exercises the changed code. If the CI logs don't show that it does, say so in the verdict line.

If not ready, give the reason with its evidence, the fact that would change the verdict, and what would make it ready, then stop. If the user asks to proceed, before or after the verdict, do the focus analysis without arguing readiness again.

## Focus

Scan every changed line and the discussion for the signals below, not only the parts the author points you to; do not trace logic or try to confirm behavior. For each candidate, ask whether it is a question of intent or context, or one that code and tooling could answer.

- Intent or context: it belongs to the human.
- Code or tooling, and CI or an existing review already answered it, or it is mechanical: skip it.
- Code or tooling, and nothing has answered it: list it as a check for an agent or the author to run. Do not run it yourself.

Point the reviewer at what only a person can settle:

- Behavior that discards or hides failure (suppressed errors, broad catches, silent fallbacks) with no stated reason.
- Author doubt: "not sure", "should probably", "TODO", "hack", or an open question in the description or threads.
- Choices of behavior or policy with no recorded rationale.
- Consumers, commitments, or compatibility the repo cannot show.
- Claims in the description with no matching change or test.
- Changes whose intent the description does not explain.

If the description or threads give a reason for a choice, it is not an item, even if you would choose differently. A reason contradicted by the diff is an item.

Deprioritize, and say so briefly, where it saves reading:

- Mechanical changes: renames, moves, formatting, generated files.
- Tests that extend existing tests with no code change.
- Changed behavior exercised by tests in the diff that passed on the head commit.
- Anything a linter or type checker enforces.

Do not ask what the reviewer can judge. When settling an item takes specialized knowledge, name the owner who has it, so the reviewer can reassign it; never ask the reviewer to accept the author's word on it. If the reviewer says they cannot judge something, skip it and send it to that owner. Never put code touching safety, security, data integrity, or compliance on the skip list.

## Output

First line: the readiness verdict, ready or not ready or unclear, with the reason.

If ready, then:

- **Your attention**: ranked items, each with the location or question, what to look for, and why it needs a human. If nothing needs them, say that nothing beyond the skip list stands out. You have not reviewed the code, so do not call the PR safe or recommend approval.
- **Ask the author**: questions only the author or another owner can answer, each marked required or optional before approval.
- **Checks to delegate**: unsettled questions an agent or the author could resolve, only if any exist.
- **Skip**: only where it saves real reading.

Link each item to evidence. Mark anything you inferred rather than read as inferred. Keep trivial PRs to a few lines.
