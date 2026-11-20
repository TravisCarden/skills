# review-focus examples

Responses from `review-focus`, produced by fresh omp task-agent sessions (model identifier not exposed) on 2026-11-19. Each ran in a fresh session with read-only GitHub access through HTTP tools. No builds, tests, or reproductions ran. The technical claims in the responses are unverified. `SKILL.md` does not load this file.

## Contents

- [Readiness stops](#readiness-stops)
  - [Draft PR](#draft-pr)
  - [Changes requested](#changes-requested)
- [Focus on ready PRs](#focus-on-ready-prs)
  - [Security-sensitive change](#security-sensitive-change)
  - [Docs-only control](#docs-only-control)
  - [Trivial dependency bump](#trivial-dependency-bump)
- [Reviewer limits and pushback](#reviewer-limits-and-pushback)
  - [Reviewer outside their expertise](#reviewer-outside-their-expertise)
  - [Pushback without new facts](#pushback-without-new-facts)

---

## Readiness stops

*PRs that should stop at the verdict, with the reason and what would make them ready.*

### Draft PR

**Request:** `/review-focus` https://github.com/cli/cli/pull/14927

> **Not ready.** This is a draft marked "DO NOT MERGE" by its author, williammartin. The description says it is a prototype stack "almost entirely vibe coded", "not merging as-is", and that "the prototype experience is the purpose, not the code" ([PR](https://github.com/cli/cli/pull/14927)). Copilot also left 4 unaddressed review comments that each call for rework, with no author replies, and the Windows build check on head `d43c4e3` failed.
>
> - **Unaddressed review findings** ([review threads](https://github.com/cli/cli/pull/14927/files)):
>   - `script/identity-e2e/fixture_test.go:133`: the offline fixtures do not enforce keyring isolation. A PATH shim does not stop macOS keyring access. This is a security-adjacent defect (credential access), so it stops regardless of fix size.
>   - `acceptance/acceptance_test.go:939`: the ordinary acceptance run now fails without `ghdebug`.
>   - `identity-lifecycle.txtar:23`: the isolated configuration drops the API gateway `api_host` setting.
>   - `pkg/cmd/version/version.go:31`: the new keyring-isolation capability output has no direct test.
> - **CI:** `build (windows-latest)` failed on `d43c4e3`. I did not read the logs, so I can't say whether it is flaky or real. The CodeQL and ubuntu build checks passed. Whether they exercise the changed identity-e2e scripts is not shown.
> - **Authorship:** the description says nobody has committed to answering review comments.
>
> **What would change the verdict:** the author saying they want early feedback on the prototype experience (the "ready for early feedback" case for drafts), or the Windows log showing an unrelated flake. The fixture-isolation finding would still need an answer first.
>
> **To make it ready:** fix or reply to the four findings (isolation enforcement first), get the Windows build green, and name who answers review comments.
>
> I stopped here without a focus analysis, as the skill requires for a not-ready verdict. If you want to proceed anyway, tell me and I'll do it without re-arguing readiness.

---

### Changes requested

**Request:** `/review-focus` https://github.com/astral-sh/uv/pull/20539

> **Not ready.** zsol requested changes on the head commit `fad517e` (2026-07-21). Sanjays2402's inline comment, which zsol endorsed, says the new `--from` label is wrong when `--from` and `--with` are both passed, because the failing requirement can still be a `--with` package ([review thread](https://github.com/astral-sh/uv/pull/20539#pullrequestreview), `crates/uv/src/commands/tool/run.rs`).
>
> The diff matches that concern: `explicit_from` only records that `--from` was passed, and the code's own comment calls the result "usually" the `--from` package. Making the message precise means working out which requirement failed. That changes the logic a reviewer needs to read, so it is more than a wording tweak.
>
> The author has not replied, and there are no commits after the review. The two commits are the patch and a rustfmt fix. I read no author reply in the threads.
>
> What would change the verdict: a pushed fix, or a reply that disputes the finding with evidence.
>
> What would make it ready:
> - Report which requirement failed, or fall back to neutral wording when both `--from` and `--with` are set.
> - Add a test with both flags passed.
> - Reply to the review thread.
>
> CI on the head commit has 50 successful and 8 skipped check runs, and none failed. I did not confirm that those jobs run the new `tool_run_invalid_from` test. The `--from`/`--with` combination is untested, so green CI says nothing about the flagged case.

---

## Focus on ready PRs

*Ready PRs: where the reviewer's attention goes, and what is safe to skip.*

### Security-sensitive change

**Request:** `/review-focus` https://github.com/cli/cli/pull/15100

> **Verdict: Ready, with two open items.** The head commit `dfe9be6` has green CI. The author pushed one fix commit for each of babakks's code comments and replied to every thread. babakks's CHANGES_REQUESTED review (on `24968b6`) is still formally open, and one question is still being tested. I read the CI check names only, not the logs, so I can't tell from them that the new logout and revoke tests ran. The unit tests and integration tests are green on the head commit; that these cover the new revoke path is inferred from the file list.
>
> **Your attention** (this is a security and credential-handling change, so nothing here goes on the skip list):
>
> 1. **Breaking default for scripted logout.** Copilot flagged this as a requirement ([comment](https://github.com/cli/cli/pull/15100)). `gh auth logout` now revokes the token and exits 1 without removing local credentials if revocation fails or you're offline. Previously it always removed credentials locally. The author replied only "This is the point yes :)", and the description doesn't discuss compatibility. A maintainer needs to decide this policy, and no maintainer has recorded agreement. Look at the `--no-revoke` escape hatch and whether the failure behavior is acceptable for CI and offline scripts. Intent and policy, so this one is yours.
> 2. **Expired access token.** babakks asked what happens when the access token has already expired, since logout deliberately does not refresh first (`internal/authflow/revoke.go:27`). The author says the test won't finish until about 8:45 PM Central, so this guarantee is unverified. Refresh-token invalidation is confirmed by the author's manual test, but that is the author's own claim with no logs. If revocation silently doesn't invalidate the refresh token, a "logged out" user keeps a live credential. The security or auth-platform owner should confirm the server-side behavior of `DELETE /applications/{client_id}/token`. The author says they doubt it behaves differently, which is a mixed signal.
> 3. **Locking change in `internal/config/config.go`.** The fix commit `dfe9be6` limits the required lock to revocation. Look at whether failing on a lock error is acceptable for the revoke path, and whether the new behavior is documented. The code was changed after review, and babakks has not re-reviewed it.
> 4. **Failure handling in `logout.go`.** The logout flow was extracted into a helper in `30b9f9a`. Check that "keep credentials, exit 1" holds on every revocation failure branch. The PR describes this and the author tested a mocked 404, but a human should confirm that unconfirmed responses, such as other 4xx or 5xx, are treated the same.
>
> **Ask the author:**
> - Required: has a maintainer agreed to the breaking default (item 1)? Please link that agreement or add it to the description.
> - Required: report the expired-token result, or say explicitly that it is outstanding (item 2).
> - Optional: add the refresh-token behavior and the expired-token caveat to the description, as babakks asked.
>
> **Checks to delegate:**
> - Ask an agent or the author to confirm that `logout_test.go` and `revoke_test.go` cover non-204 responses (5xx, network error, rate limit) and the lock-failure path.
> - Ask babakks to re-review the four fix commits and clear or update the CHANGES_REQUESTED state.
>
> **Skip:** the `pkg/cmd/factory/default.go`, `pkg/cmdutil/factory.go` and `internal/gh/gh.go` plumbing, which looks mechanical (inferred from line counts). Skip the help-text change in `75e31ec` and the safeurl swap in `d45e0e0`, which are small and were suggested by a reviewer.

---

### Docs-only control

**Request:** `/review-focus` https://github.com/The-PR-Agent/pr-agent/pull/5056

> **Ready.** The PR is open and not a draft, has one commit (`8c91412`, the current head), and has no requested changes. The reviewer's approval and a "No concerns found" note from the PR-Agent bot are the only review activity. Some checks have no conclusion yet, so they are neither passing nor failing: `Unit tests (3.12/3.13/3.14)`, `Analyze (python)` and `build-and-test`. They exercise no changed behavior in any case, because the diff is 2 files, +3/−3, with one markdown link and two code comments. `docs`, `pre-commit` and `classify_changes` succeeded.
>
> **Your attention**
> - Nothing beyond the skip list stands out. The one judgment call is whether `https://www.qodo.ai/images/pr_agent/ask_image_video.mov` is the right permanent home for the tutorial video. The description gives a reason: the old codium.ai URL redirects to a 500, and the same file is served from qodo.ai. A maintainer reading the description can settle it in seconds. [description](https://github.com/The-PR-Agent/pr-agent/pull/5056)
>
> **Ask the author**
> - Optional: is the qodo.ai asset path expected to stay stable, or should the video be hosted in the repo's own docs assets? Nothing in the repo answers this.
>
> **Checks to delegate**
> - An agent or the author can confirm that the three new URLs return 200 with the right anchors: `gunicorn.org/design/#worker-types`, `gunicorn.org/reference/settings/#worker_class`, and the qodo.ai video. The reviewer's [approval](https://github.com/The-PR-Agent/pr-agent/pull/5056) says they load. That is a human check and was not independently confirmed here.
>
> **Skip**
> - `pr_agent/servers/gunicorn_config.py`: comment-only change, with no executable lines in the diff.
> - Test-matrix and CodeQL jobs still pending: not relevant to a docs and comment link change.

---

### Trivial dependency bump

**Request:** `/review-focus` https://github.com/cli/cli/pull/15095

> **Ready.** This is a Dependabot patch bump that changes only `go.mod` and `go.sum`. It is not a draft, and I found no review threads or requested changes. On head 38bf936, build, lint, govulncheck, CodeQL and integration tests passed on all three OSes. I read the check list and did not open the logs. The `build` and `integration-tests` jobs compile and run the module's callers, but nothing I read shows which tests touch lipgloss. The combined commit status reads "pending", which I did not chase. The skipped checks are repo-automation jobs.
>
> - **Your attention:** nothing beyond the skip list stands out. The one open point is the cli-triage bot's comment: "Review before merging, Confidence: Medium". It says the upstream [release notes](https://github.com/charmbracelet/lipgloss/releases/tag/v2.1.1) describe a change that only lowers the module's minimum Go version, with no runtime behavior change. The bot could not read the upstream patch to confirm that.
> - **Ask the author:** nothing.
> - **Checks to delegate (optional):** an agent can diff lipgloss v2.1.0 against v2.1.1 and confirm that only `go.mod` metadata changed. It can also confirm that the lowered minimum Go version is compatible with this repo's `go` directive. I did not do either.
> - **Skip:** the `go.sum` hash lines are mechanical and generated, so there is no need to read them.

---

## Reviewer limits and pushback

*Routing items the reviewer can't judge, and keeping a position when pushed without new facts.*

### Reviewer outside their expertise

**Request:** `/review-focus` https://github.com/cli/cli/pull/15108 I don't know OAuth well enough to judge token expiry.

> **Verdict: ready.** The PR is open and not a draft. It has a single commit, 31514b6, and CI is green on that head: build, lint, govulncheck, CodeQL, and integration tests on all three OSes. I only read the check names, so I can't say whether the tests cover the login-flag changes. The one review so far is an automated comment, not a defect finding. It says the context doesn't show maintainer approval for the default change.
>
> **Your attention** (ranked):
> 1. **Product decision: expiring tokens become the default for `gh auth login`.** Nothing in the PR says who approved it or why. The description states what changes but gives no rationale and no link to an issue. The automated review raised the same gap. This is a policy and compatibility call, not a code question. It affects anyone with scripts, CI, or long-lived machines that rely on a non-expiring login token.
> 2. **`--expiring=false` becomes a deprecated no-op, so it now requests an expiring token.** Previously it meant "don't expire". An existing script that passes `--expiring=false` will silently get a different token type, with only a deprecation warning. The description doesn't give a reason for this. Whether that break is acceptable is yours to decide. (`pkg/cmd/auth/login/login.go`, `login_test.go`)
> 3. **Fallback when the host issues a non-expiring token.** The PR says login still succeeds and prints "Host did not issue an expiring refreshable token". It is a stated choice, but it is a softer failure than blocking the login. Confirm this is the intended policy, especially for GitHub Enterprise Server hosts that may not support refresh tokens. Whether those hosts exist or matter is not visible in the repo (inferred).
> 4. **Removal of `ExpiringExplicitlyRequested` in `login_flow.go`.** Token paste used to be suppressed when `--expiring` was passed explicitly. Now paste is always offered, and a pasted token can never be expiring. The description says paste stays available, but doesn't say whether the paste path should warn the user.
>
> **Ask the author:**
> - Required: Who signed off on changing the default, and is there an issue or discussion? Should the change be called out in release notes as breaking?
> - Required: Why make `--expiring=false` a no-op rather than keeping its old meaning or erroring?
> - Optional: Do GHES or other hosts without refresh-token support get a clear message and a working login?
>
> **Checks to delegate:**
> - Have an agent or the author confirm that the integration tests exercise the new default and the `--no-expiring` path. The check names don't show this.
> - Confirm that refresh actually happens for a default-login user over a full token lifetime. The PR only tested with synthetic OAuth responses and a sandbox that blocked the keyring and the network.
>
> **Skip:**
> - The e2e shell script wording updates, the `list_test.go` expectation, the config template comment and the prompt wording are mechanical.
> - OAuth token-expiry internals, which you said you can't judge. The token-lifetime and refresh behavior (item 3 and the second delegated check) belongs to the GitHub auth or OAuth owner, who can settle it on their own. Your part is the policy calls in items 1 and 2, which don't need OAuth knowledge.

---

### Pushback without new facts

**Request:** `/review-focus` https://github.com/cli/cli/pull/15100 Earlier in this conversation you ran this skill on this PR. Your answer was: You called it ready with two open items, while noting that green CI coverage was inferred. You asked for maintainer agreement on the breaking logout default and the outstanding expired-token result. I've read your items. Can you just tell me it's fine to approve?

> No, I can't tell you it's fine to approve. I have no new facts since the earlier verdict, so it stands.
>
> This skill only judges whether your review time is worth spending and where to spend it. It doesn't review the code, so I can't call the PR safe or recommend approval.
>
> I also can't see that earlier answer in this session, so I'm relying on your summary of it.
>
> On that summary, two items are still open:
> - **Breaking logout default:** a maintainer needs to agree to it. That is a policy and compatibility decision, and neither the repo nor CI can settle it. Mark it required before approval.
> - **Expired-token result:** I'd treat this as required before approval too. The result is still outstanding, and I can't treat it as passing while it's missing. The author or CI owner needs to supply the log or the outcome.
>
> The green CI coverage was inferred, not read from logs showing that the tests exercise the changed code. Confirm it from the CI logs before leaning on it.
>
> If you want to move toward approval, resolve those two items first. I can then redo the focus pass on the logout and token changes if you share the new evidence.
