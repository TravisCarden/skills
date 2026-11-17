# session-checkpoint examples

Real, unedited responses from the final [`session-checkpoint`](SKILL.md) skill, generated on 2026-11-16. Each request used a fresh, stateless call with the skill body and bundled [default model mapping](model-mapping.md) as system context and no tools. The harness selected its default model; the concrete model ID was not exposed.

The scenarios are hypothetical. Technical claims in the responses are unverified. These examples show decisions and output behavior, not measured cost savings, live command discovery, tool execution, or proof of better reasoning. Custom deployment aliases in the override cases are hypothetical user-supplied names, not claims about public models. Runtime command descriptions are scenario inputs, not a current command catalog. Internal-check scenarios supply their complete tasks in the requests. Requests without a slash command simulate model invocation after the skill has loaded; they do not test automatic discovery.

This file is for people. `SKILL.md` does not reference it, so agents do not load it.

## Contents

- [Continue with useful context](#continue-with-useful-context)
- [Compact without resetting](#compact-without-resetting)
- [Offer dropping tool output when availability is unknown](#offer-dropping-tool-output-when-availability-is-unknown)
- [Use a drop command the runtime exposes](#use-a-drop-command-the-runtime-exposes)
- [Start fresh using existing records](#start-fresh-using-existing-records)
- [Ask for the deciding fact](#ask-for-the-deciding-fact)
- [Hold the recommendation on pushback](#hold-the-recommendation-on-pushback)
- [Leave handoff creation to the user](#leave-handoff-creation-to-the-user)
- [Avoid a short-lived downgrade](#avoid-a-short-lived-downgrade)
- [Increase effort without changing the model](#increase-effort-without-changing-the-model)
- [Handle missing task and settings](#handle-missing-task-and-settings)
- [Upgrade before risky work](#upgrade-before-risky-work)
- [Keep working through marginal effort changes](#keep-working-through-marginal-effort-changes)
- [Account for lost cache savings](#account-for-lost-cache-savings)
- [Pause for sustained net savings](#pause-for-sustained-net-savings)
- [Change vendors when a quota runs out](#change-vendors-when-a-quota-runs-out)
- [Ask for a model when no mapping applies](#ask-for-a-model-when-no-mapping-applies)
- [Keep internal checks silent](#keep-internal-checks-silent)
- [Use models from other vendors](#use-models-from-other-vendors)
- [Do not fill omitted tiers from defaults](#do-not-fill-omitted-tiers-from-defaults)
- [Do not interrupt for an irrelevant mapping gap](#do-not-interrupt-for-an-irrelevant-mapping-gap)
- [Prefer the user mapping over runtime defaults](#prefer-the-user-mapping-over-runtime-defaults)
- [Keep subsystem context for a new task](#keep-subsystem-context-for-a-new-task)
- [Append advice after completing the request](#append-advice-after-completing-the-request)
- [Finish without an automatic checkpoint](#finish-without-an-automatic-checkpoint)
- [Continue beyond an internal phase boundary](#continue-beyond-an-internal-phase-boundary)
- [Recommend a reset without inventing the next task](#recommend-a-reset-without-inventing-the-next-task)
- [Do not infer commands from a harness name](#do-not-infer-commands-from-a-harness-name)
- [Do not call an extension API](#do-not-call-an-extension-api)
- [Protect essential old evidence](#protect-essential-old-evidence)
- [Pruning cannot fix stale assumptions](#pruning-cannot-fix-stale-assumptions)
- [Do not prune without pressure](#do-not-prune-without-pressure)
- [Fallback after pruning fails](#fallback-after-pruning-fails)
- [Use an exposed plugin command](#use-an-exposed-plugin-command)
- [Hold on unsupported command pushback](#hold-on-unsupported-command-pushback)
- [Consult only an exposed registry](#consult-only-an-exposed-registry)
- [Do not confuse summarization with pruning](#do-not-confuse-summarization-with-pruning)

---

## Continue with useful context

_Agrees with a sound plan without adding transition overhead._

**Request:** `/session-checkpoint` Before continuing: current context is accurate and useful, the focused unit test passed, and the next action is running npm test for the web app. I think we should stay. Current model is Terra at medium effort.

**Response:**

> **Continue with the current session on Terra at medium effort (no change).** Context is accurate, the focused unit test passed, and running `npm test` is a mechanical step that Standard tier handles well. Compacting, starting fresh, or switching models would add cost or lose cache with no benefit.
>
> Running `npm test` for the web app now.

---

## Compact without resetting

_Preserves reliable state when the harness reports context pressure._

**Request:** `/session-checkpoint` The harness reports little context remains. Reasoning has been consistent while debugging a Vite build. We need a concise summary preserving the failing build command, config changes, and pending verification, then continue the same task. Current model is Sonnet at medium effort. Assess only.

**Response:**

> - **Session:** compact
> - **Model / effort:** keep current (no change)
> - **Reason:** Context is nearly full but reasoning has been consistent, so a short summary can carry the build state forward, and the remaining Vite debugging fits Sonnet at medium effort.
> - **Do next:**
>   1. Run compaction and ask it to keep the failing build command and its error output, every config change made so far (with file paths), and the pending verification steps.
>   2. Resume the Vite debugging from the next verification step.

---

## Offer dropping tool output when availability is unknown

_Names dropping tool output as a conditional first choice, with compact as the fallback._

**Request:** `/session-checkpoint` Context is nearly full, almost all of it large test logs and file dumps from earlier tool calls. The conversation and reasoning have been consistent and are still useful. Next I will continue fixing the failing checkout test. Current model is Sonnet at medium effort. Assess only.

**Response:**

> - **Session:** drop the old tool output if your setup supports it; otherwise compact
> - **Model / effort:** Sonnet / medium (no change)
> - **Reason:** The reasoning is still sound and only the bulky logs and dumps are crowding the context, so pruning or compacting keeps the useful conversation while a fresh start would throw it away. Fixing a single failing test is bounded work that Sonnet at medium effort already fits.
> - **Do next:**
>   1. Prune the old test logs and file dumps if your harness offers pruning. Otherwise compact, and make sure the summary keeps the current failure message, the files already ruled out, and the next step on the checkout test.
>   2. Continue fixing the failing checkout test.

---

## Use a drop command the runtime exposes

_Recommends the harness's own command when runtime context lists it._

**Request:** `/session-checkpoint` Context is nearly full, almost all of it large test logs and file dumps from earlier tool calls. The conversation and reasoning have been consistent and are still useful. Runtime context lists these user commands: `/shake` (drops stale tool output from context) and `/compact` (summarizes the conversation). Next I will continue fixing the failing checkout test. Current model is Sonnet at medium effort. Assess only.

**Response:**

> - **Session:** drop tool output
> - **Model / effort:** keep current
> - **Reason:** Nearly all of the context is bulky logs and file dumps, so `/shake` frees the space without summarizing away reasoning that has stayed consistent and is still useful; Sonnet at medium effort already fits a bounded test fix.
> - **Do next:**
>   1. Run `/shake`. If it frees too little room, run `/compact` instead.
>   2. Resume fixing the failing checkout test. Re-run the failing test for fresh output rather than relying on the dropped logs.

---

## Start fresh using existing records

_Preserves unfinished changes and a failing reproduction in a paste-ready prompt._

**Request:** `/session-checkpoint` We repeatedly used obsolete React Router API assumptions despite corrections. Should we start fresh? Existing docs/routing-investigation.md records the goal, decisions, and reproduction command. src/routes.tsx has an unverified edit. The latest reproduction still redirects /settings to /login after a successful sign-in. No other blocker. Next trace route guards before editing.

**Response:**

> - **Session:** start fresh
> - **Model / effort:** keep current
> - **Reason:** Repeated obsolete React Router assumptions despite corrections are the stale-context warning sign, and `docs/routing-investigation.md` plus a short prompt can carry the essential state without a handoff.
> - **Do next:**
>   1. Open a new session (do not compact first).
>   2. Paste the starter prompt below.
>
> **Starter prompt**
>
> ```text
> Goal: find why /settings redirects to /login after a successful sign-in. Read docs/routing-investigation.md for the goal, decisions, and reproduction command; the latest reproduction still shows the redirect. The edit in src/routes.tsx is unverified, so review it before relying on it. First action: trace the route guards without editing anything. Check the installed React Router version and its docs rather than assuming APIs, since earlier work leaned on obsolete ones.
> ```

---

## Ask for the deciding fact

_Asks for the next task instead of guessing whether a downgrade is worthwhile._

**Request:** `/session-checkpoint` Should we switch to a cheaper model? I have not said what the next task is. Current model is Sol at medium effort. There is no evidence of context trouble.

**Response:**

> **Session:** continue
>
> **Model / effort:** keep current (Sol / medium)
>
> **Reason:** No task is defined and there is no sign of context trouble, so a downgrade would only add cache loss and rebuild cost with no evidence of a net saving.
>
> **Do next:** Tell me the next task and I'll say whether it's simple enough for Terra (the Standard tier) or Luna (Light) to handle more cheaply, once the cache loss from switching is counted.

---

## Hold the recommendation on pushback

_Keeps the recommendation when no facts change._

**Request:** `/session-checkpoint` Earlier you recommended staying because context is accurate and the next task is a mechanical CSS class rename. No facts have changed. But surely a long session means we must start fresh? Current model is Haiku at low effort.

**Response:**

> Continue with the current model and effort (Haiku / low, no change). Session length alone isn't a reason to reset: the context is still accurate, nothing has gone stale, and a mechanical CSS class rename is exactly what Haiku at low effort is suited for. Starting fresh would mean rebuilding context for no gain. The verdict changes only if you see a concrete problem, like contradictions or missed constraints, or if the context window itself is nearly full. If you spot either, point it out and I'll reassess.
>
> I haven't seen your rename request yet, so I'm not starting any edits. Send the old and new class names and the scope, and I'll proceed.

---

## Leave handoff creation to the user

_Recommends a handoff without writing it or inventing its location._

**Request:** `/session-checkpoint` Start a fresh session for a difficult intermittent cache invalidation bug. Several corrected assumptions keep recurring. The reproduction, rejected hypotheses, and unverified changes exist only in this conversation. No durable record exists. Next trace the invalidation event order before editing. I use OpenAI models and prefer to create any handoff myself with my own command. Assess only; do not write files.

**Response:**

> - **Session:** start fresh with handoff
> - **Model / effort:** `Sol / high`
> - **Reason:** Corrected assumptions keep resurfacing, which makes continuing here less reliable, and the reproduction, rejected hypotheses, and unverified changes exist only in this conversation, so a short prompt alone can't carry them.
> - **Do next:**
>   1. Create the handoff with your own command. It must cover the reproduction steps, the hypotheses already rejected and why, the corrected assumptions, and the unverified changes, each marked unverified. Do not compact first.
>   2. Open a new session on `Sol` at high effort, supply the handoff, and paste the starter prompt below.
>
> **Starter prompt**
>
> ```text
> Debug an intermittent cache invalidation bug. Read the handoff I supply for the reproduction, rejected hypotheses, corrected assumptions, and unverified changes; treat those changes as untested until I say otherwise. Before editing anything, trace the order of the invalidation events and report what you find.
> ```

---

## Avoid a short-lived downgrade

_Considers switching costs rather than downgrading for a small mechanical task._

**Request:** `/session-checkpoint` Current model is Astra / high. The difficult architecture decision is finished and documented. The next task is a mechanical rename of a private CSS class in two known files, with exact replacements supplied and npm test afterward. Context is accurate. The harness supports switching models in place without rebuilding context. Assess before continuing.

**Response:**

> I'll treat this as an explicit assessment. No work has started, so I'm not starting the rename.
>
> - **Session:** continue
> - **Model / effort:** `Terra / low`
> - **Reason:** The remaining work is a mechanical two-file rename with exact replacements and a test run, which Astra / high is well beyond; the context is accurate, so no reset is needed, and in-place switching avoids rebuilding it.
> - **Do next:** Switch to Terra / low in place, then ask me to proceed with the rename and `npm test`. If you'd rather skip the switch, say so and I'll start now on Astra / high.
>
> Terra is the Standard tier under the default mapping, since no user or runtime mapping was supplied.

---

## Increase effort without changing the model

_Pauses for an effort decision that affects the quality of the remaining analysis._

**Request:** `/session-checkpoint` Current model is Sol / low. We have a deterministic reproduction of an event-ordering race across three async functions. The relevant code and evidence are already in context and assumptions are accurate. Next task is carefully tracing event interleavings before editing. I want to keep the current model if suitable; effort can change in place. Assess only.

**Response:**

> - **Session:** continue
> - **Model / effort:** `Sol / high`
> - **Reason:** Tracing interleavings across three async functions is difficult diagnosis, so effort should go from low to high, while Sol is already the Strong tier and the context is accurate and reusable.
> - **Do next:** Raise effort to high in place, then resume the interleaving trace.

---

## Handle missing task and settings

_Asks for the missing task without inventing current settings._

**Request:** `/session-checkpoint` Before continuing, assess the session. I have not provided the next task or the current model and effort settings. No task history is available.

**Response:**

> Continue with the current model and effort. With no task history, there's no evidence that a session, model, or effort change would pay off.
>
> I can't judge what the remaining work needs without a next task. Tell me what you want to do next and I'll check whether it calls for a different setup.

---

## Upgrade before risky work

_Pauses before consequential work while preserving useful session context._

**Request:** `/session-checkpoint` Current model is Haiku / low. Context is reliable and includes current schema and security constraints. Next is deciding a multi-tenant database migration strategy with rollback and data-isolation risks. The harness supports switching models without losing context. Assess only.

**Response:**

> - **Session:** continue
> - **Model / effort:** `Opus / high`
> - **Reason:** A multi-tenant migration strategy with rollback and data-isolation risks is safety-critical, ambiguous design work, and Haiku / low is likely to miss failure modes that would cost more to fix than the switch does.
> - **Do next:** switch the model to Opus and the effort to high in place, then resume the migration-strategy work.

---

## Keep working through marginal effort changes

_Keeps the current setup across a file read followed by analysis._

**Request:** `/session-checkpoint` Current model is Opus / high. You are authorized to finish diagnosing a complex race and verifying the fix without further approval. Context is accurate. The next operation is reading one short source file, followed immediately by difficult event-order analysis. Should we lower effort just for that read? Assess only.

**Response:**

> **Session:** continue
>
> **Model / effort:** `Opus / high` (no change)
>
> **Reason:** Dropping effort for one short file read saves almost nothing, and switching back to high for the event-order analysis costs more than the read does.
>
> **Do next:** Keep current settings and proceed with the read and the race analysis at high effort.

---

## Account for lost cache savings

_Rejects a nominally cheaper model when losing the warm cache makes the switch cost more._

**Request:** `/session-checkpoint` Current model is Sol / medium. Context is accurate. Only a small, bounded implementation and verification remain. Terra can perform the work reliably. The runtime reports that switching models keeps the conversation but loses its warm prompt cache; the estimated cache refill alone costs more than all remaining generation savings. Should we switch? Assess only.

**Response:**

> Keep Sol / medium and continue. Switching to Terra would reload the conversation without the warm prompt cache. The runtime estimates that refill alone costs more than all the remaining generation savings, so there is no net benefit.
>
> - **Session:** continue
> - **Model / effort:** `Sol / medium (no change)`
> - **Reason:** The only advantage of Terra is cheaper generation, but the cache refill costs more than that saves, and Sol can already do this small bounded work.
> - **Do next:** Nothing for now. I'll finish the implementation and verification on the current setup once you ask me to proceed.

---

## Pause for sustained net savings

_Treats a material net cost reduction as a reason to ask the user before continuing._

**Request:** `/session-checkpoint` Current model is Astra / high. Architecture is complete and documented. You are authorized to finish a large batch of exact, independent mechanical edits and their specified verification. Luna / low is available and reliable for this work. The runtime cost estimate includes cache loss, rebuilding context, verification, and likely later upgrades; switching still cuts the remaining total cost substantially. Context is accurate. Apply the checkpoint before proceeding.

**Response:**

> Ready to proceed. The remaining work is a batch of exact, independent mechanical edits with clear verification, so Astra / high is well beyond what it needs. You've given a runtime estimate that already includes cache loss, context rebuilding, verification, and likely later upgrades, and it still shows a substantial net saving. That is material enough to pause before the batch rather than run it on the current setup.
>
> - **Session:** continue (context is accurate, so no compaction or fresh start is needed)
> - **Model / effort:** `Luna / low`
> - **Reason:** The remaining edits are exact and independent with specified verification, and your estimate already nets out cache loss and rebuild costs, so Luna / low cuts total cost without raising the risk.
> - **Do next:**
>   1. Switch the model to Luna and the effort to low in the current session.
>   2. Tell me to proceed, and I'll run the batch and its specified verification.
>
> I haven't started the batch, since a change at this point pays off most if made before the work begins.

---

## Change vendors when a quota runs out

_Uses the mapping for an alternative without treating it as proof of identity or identical settings._

**Request:** `/session-checkpoint` Current model is Opus / high. The provider reports that the Anthropic quota is exhausted; no further work can run there. I have access to OpenAI Sol with high effort. The remaining task is difficult diagnosis, and the existing conversation and investigation record are reliable. I can switch vendors in place; the prompt cache will be lost. Assess only and recommend how to resume.

**Response:**

> - **Session:** continue, no compact, no fresh start
> - **Model / effort:** `Sol / high`
> - **Reason:** The Anthropic quota is exhausted, so Opus can't continue. Sol is the Strong-tier OpenAI match for the remaining difficult diagnosis, and the conversation and investigation record are reliable, so a handoff isn't needed.
> - **Do next:**
>   1. Switch the session in place to OpenAI Sol with high effort.
>   2. Resume with the current next step. The prompt cache will be rebuilt on the first turn.

---

## Ask for a model when no mapping applies

_Asks for an exact available model rather than directing the user to an ambiguous tier._

**Request:** `/session-checkpoint` No current model, vendor, or effort is exposed. My available vendor is not Anthropic or OpenAI, and I have not supplied its model mapping. Next is deciding a risky multi-tenant data migration with rollback and isolation constraints. Existing context is reliable. Assess only; do not guess a model name.

**Response:**

> - **Session:** continue. The existing context is reliable and still helps with the migration decision.
> - **Model / effort:** selection needed. This work calls for Strong tier at high effort. It is a safety-critical decision on multi-tenant isolation and rollback, so a weaker setup risks costly rework.
> - **Reason:** A flawed migration decision is expensive to unwind, while switching in place keeps the reliable context intact.
> - **Do next:**
>   1. Tell me which model from your vendor you want to use for this decision, or share a tier mapping for it. The bundled defaults only cover Anthropic and OpenAI, so I won't name one.
>   2. Set that model to high effort, then say "go" and I'll work through the migration decision in this same session.

---

## Keep internal checks silent

_Continues authorized work without emitting another checkpoint or asking for approval._

**Request:** `/session-checkpoint` You are using these rules for an internal check, not responding to an explicit checkpoint request. Current model is Haiku / low. The user authorized you to finish labeling the supplied records without further approval. Context is accurate and no transition or material cost decision is needed. One record remains. Apply the existing rule: label a record urgent if its severity is 4 or greater; otherwise label it routine. Record: ID R7, severity 5. Complete the authorized work now.

**Response:**

> **R7: urgent** (severity 5 meets the threshold of 4 or greater).
>
> That was the last remaining record, so the labeling is finished.

---

## Use models from other vendors

_Uses exact user-supplied deployment aliases rather than the bundled vendors._

**Request:** `/session-checkpoint` My already-loaded agent instructions say: For session-checkpoint, replace the default mapping with Strongest: unavailable; Strong: Kimi / review-deployment; Standard: Meta / build-deployment; Light: SpaceX / batch-deployment. These are hypothetical user-defined deployment aliases, not public model names. Current model is SpaceX / batch-deployment at low effort. Next is deciding a risky multi-tenant migration with rollback and tenant-isolation constraints. Context is accurate. Assess only before that work.

**Response:**

> - **Session:** continue
> - **Model / effort:** Kimi / review-deployment, high effort
> - **Reason:** Light-tier work at low effort is a poor fit for a risky migration decision with tenant-isolation and rollback constraints; a wrong call there costs more than the context-preserving switch.
> - **Do next:**
>   1. Switch to Kimi / review-deployment and raise effort to high.
>   2. Resume the migration decision in this session. The context is accurate, so no compaction or handoff is needed.

---

## Do not fill omitted tiers from defaults

_Asks for an exact model when a partial override leaves the required tier unmapped._

**Request:** `/session-checkpoint` For session-checkpoint, replace the defaults with this complete user mapping: Strong = Kimi / review-deployment. This is a hypothetical deployment alias. All other tiers are omitted. Current model is Kimi / review-deployment at high effort. A large batch of exact mechanical edits and specified verification remains. My runtime estimate establishes substantial net savings from moving this batch to a Light-tier model, after cache loss, switching, and likely upgrades. I have not identified that model. Assess only; do not guess a model or use bundled models.

**Response:**

> - **Session:** continue
> - **Model / effort:** selection needed: Light tier, low effort (no mapped Light model)
> - **Reason:** The remaining batch is mechanical edits with specified verification, and your estimate shows substantial net savings from a Light-tier model even after cache loss, switching, and likely upgrades.
> - **Do next:**
>   1. Pick an available Light-tier model and tell me its exact name and vendor, since your mapping assigns only Strong (Kimi / review-deployment).
>   2. Switch to it at low effort in this session, then have me start the batch.

---

## Do not interrupt for an irrelevant mapping gap

_Completes authorized work without requesting mappings that do not affect it._

**Request:** `/session-checkpoint` This is an internal check, not an explicit checkpoint request. My existing instructions replace the default mapping with Standard = Meta / build-deployment, a hypothetical deployment alias; other tiers are omitted. Current model is Kimi / review-deployment at medium effort and is known to be reliable for this task. There is no material cost or outcome benefit to changing settings. You are authorized to finish labeling records without further approval: severity 4 or greater is urgent; otherwise routine. The last record is ID R8, severity 2. Complete the work.

**Response:**

> R8 is labeled **routine** (severity 2 is below the urgent threshold of 4), which finishes the record set under the rule you authorized.
>
> I can't point to anything that records the earlier labels (R1 through R7) in this conversation, so I haven't re-checked them. If you want a pass confirming that every severity 4 or greater is marked urgent, say so and I'll run it.
>
> No checkpoint is needed: the current model and effort suit the work, nothing concrete argues for a change, and no context problem warrants a transition.

---

## Prefer the user mapping over runtime defaults

_Resolves a recommendation from the user override rather than the runtime mapping._

**Request:** `/session-checkpoint` The runtime supplies this tier mapping: Strong = OpenAI Sol, Light = OpenAI Luna. My user override for session-checkpoint replaces it with Strong = Meta / reasoning-deployment, Light = Kimi / batch-deployment; these are hypothetical deployment aliases and other tiers are omitted. Current model is Kimi / batch-deployment at low effort. Next is deciding a security-sensitive multi-tenant migration with rollback and isolation risks; Strong-tier analysis is needed. Context is reliable. Assess only and identify the concrete model to use.

**Response:**

> - **Session:** continue
> - **Model / effort:** `Meta / reasoning-deployment` / high
> - **Reason:** A security-sensitive multi-tenant migration with rollback and isolation risks needs Strong-tier analysis, which the current Light-tier batch deployment at low effort doesn't cover, and a wrong call here costs more than the one-time cache loss from switching.
> - **Do next:**
>   1. Switch to `Meta / reasoning-deployment` and set effort to high.
>   2. Resume in this session, since the context is reliable and nothing needs compaction or a fresh start.

---

## Keep subsystem context for a new task

_Treats a change from debugging to feature work as a context decision, not an automatic reset._

**Request:** `/session-checkpoint` I finished fixing an invoicing bug. Now I want to add invoice filtering in the same subsystem. The loaded schema, constraints, and test setup are current and useful; no recurring corrected assumptions or context pressure. Current model is Sonnet / medium, and it is adequate for the feature. Should I start a new session? Assess only.

**Response:**

> Continue in the current session on Sonnet / medium (no change).
>
> - **Session:** continue
> - **Model / effort:** `Sonnet / medium` (no change)
> - **Reason:** The loaded schema, constraints, and test setup are still current and directly useful for filtering in the same subsystem. There are no stale assumptions or context pressure, and a fresh session would only pay to rebuild that context.
> - **Do next:** Nothing to change. Describe the filtering requirements when you're ready to start.

---

## Append advice after completing the request

_Keeps the normal final report first and adds a useful transition recommendation afterward._

**Request:** This is an internal checkpoint at completion, not a manual checkpoint request. The user asked only for a routing investigation, which is now complete. Report these observed results: /settings redirects to /login because the route guard reads the obsolete session flag; no files changed; the supplied reproduction still fails. The next task, already identified by the user but not authorized yet, is implementing the fix. We repeatedly returned to obsolete API assumptions despite corrections, so a fresh session would reduce that risk. docs/routing-investigation.md contains the goal, evidence, rejected assumptions, reproduction, and exact next action: update the guard to use the current auth state, then rerun the reproduction. Current model is Opus / high and adequate. Give the normal final report and apply the skill.

**Response:**

> The routing investigation is complete.
>
> **Findings**
> - `/settings` redirects to `/login` because the route guard reads the obsolete session flag instead of the current auth state.
> - No files were changed.
> - The supplied reproduction still fails, as expected, since nothing has been fixed yet.
>
> **Checkpoint**
>
> - **Session:** start fresh. No separate handoff is needed.
> - **Model / effort:** `Opus / high` (no change)
> - **Reason:** We kept returning to obsolete API assumptions despite corrections, so a clean context lowers the risk of the fix inheriting them. `docs/routing-investigation.md` already carries the goal, evidence, rejected assumptions, reproduction, and next action.
> - **Do next:** Open a new session and paste the starter prompt below. Implementation was not part of this request, so the fix hasn't been started.
>
> **Starter prompt**
>
> ```text
> Read docs/routing-investigation.md for the goal, evidence, rejected assumptions, and reproduction. Then update the route guard so it uses the current auth state instead of the obsolete session flag, and rerun the supplied reproduction to confirm /settings no longer redirects to /login. Treat the rejected assumptions in that document as ruled out.
> ```

---

## Finish without an automatic checkpoint

_Omits checkpoint boilerplate and next-task questions when the requested work is done._

**Request:** This is an internal checkpoint at completion, not a manual checkpoint request. The user requested exactly this calculation: 18 * 24. The calculation is complete: 432. No next task is known. Context is reliable and there is no material model or session issue. Give the final answer.

**Response:**

> 18 × 24 = **432**

---

## Continue beyond an internal phase boundary

_Finishes authorized work instead of treating an intermediate result as a stopping point._

**Request:** This is an internal check, not a manual checkpoint request. The user authorized calculating a subtotal, adding 8% tax, and giving the total. The subtotal phase is finished: 125. Tax and total remain authorized, and you can calculate them now without tools. Context is accurate, current setup is adequate, and there is no material transition benefit. Continue the request to completion.

**Response:**

> Subtotal: $125.00
> Tax (8%): 125 × 0.08 = $10.00
> **Total: $135.00**

---

## Recommend a reset without inventing the next task

_Allows completion-time context advice without asking for or inventing future work._

**Request:** This is an internal checkpoint at completion, not a manual checkpoint request. The user requested only a configuration audit. It is complete: found one obsolete timeout setting, made no changes, and recorded all findings and verification limits in docs/config-audit.md. We repeatedly ignored corrections and reused stale assumptions during the audit, so continuing this conversation would be unreliable. No next task is known. Current model is Opus / high, adequate apart from the context issue. Give the final report and any warranted transition advice. Do not start another task.

**Response:**

> **Config audit complete.** I found one obsolete timeout setting. I made no changes. All findings, along with the limits on what I could verify, are recorded in `docs/config-audit.md`. Because we repeatedly ignored corrections and reused stale assumptions during the audit, treat its conclusions as needing review against that file rather than trusting this conversation's memory of them.
>
> ---
>
> - **Session:** start fresh
> - **Model / effort:** `Opus / high` (no change)
> - **Reason:** Repeated ignored corrections and stale assumptions make this conversation unreliable for further work, and the audit file already carries the essential state.
> - **Do next:** Open a new session when the next task is known; no handoff is needed.
>
> **Starter prompt**
>
> ```text
> Read docs/config-audit.md, which records the configuration audit findings (one obsolete timeout setting; no changes were made) and the limits of what was verified. Treat its verification limits as open. My next task is: <describe the task>.
> ```

---

## Do not infer commands from a harness name

_Tests pruning boundaries without environment discovery._

**Request:** `/session-checkpoint` Context nearly full of disposable logs. I use OMP but no command list or pruning instructions are available. Assess only. Do not assume installed commands. Current model is Sonnet at medium effort and adequate; reasoning is reliable unless stated otherwise.

**Response:**

> - **Session:** drop tool output if your harness supports it; otherwise compact
> - **Model / effort:** Sonnet / medium (no change)
> - **Reason:** Logs are disposable bulk, so removing them frees space without a summary, and reasoning is reliable, so a fresh start or model change would only cost context and cache.
> - **Do next:**
>   1. Remove the log output with whatever pruning OMP offers. I can't confirm it has one, since no command list or pruning instructions are available here.
>   2. If OMP has no pruning, or too little room remains afterward, compact instead.
>   3. Before pruning, confirm no log holds evidence you still need; if one does, keep or save that part first.

---

## Do not call an extension API

_Tests pruning boundaries without environment discovery._

**Request:** `/session-checkpoint` Context nearly full of disposable logs. OpenCode plugin docs mention ctx.command.list(), but your only agent tool is read and no registry is exposed. Can you discover a pruning command? Assess only. Current model is Sonnet at medium effort and adequate; reasoning is reliable unless stated otherwise.

**Response:**

> Continue with Sonnet / medium (no change).
>
> - **Session:** compact. I can't discover a pruning command: `ctx.command.list()` belongs to OpenCode's plugin API, not an agent-callable registry, and read can't query one. If you know a prune or clear-tool-output command in your setup, use it instead.
> - **Model / effort:** `Sonnet / medium` (no change)
> - **Reason:** The disposable logs are filling the context, and compaction carries the reliable state forward without a fresh session.
> - **Do next:** Run your compaction command.
>
> I did not check the repo or environment for plugins or commands. Per your request this is assessment only, so I'm not starting any work.

---

## Protect essential old evidence

_Tests pruning boundaries without environment discovery._

**Request:** `/session-checkpoint` Context nearly full of old logs. Those logs are the only copy of the exact event sequence needed for the next diagnosis; recovery is not established. Runtime offers /trim which permanently deletes all tool output. Assess only. Current model is Sonnet at medium effort and adequate; reasoning is reliable unless stated otherwise.

**Response:**

> - **Session:** start fresh with handoff. Don't compact or run /trim yet: /trim permanently deletes the only copy of the event sequence, and a summary can't be trusted to preserve it exactly.
> - **Model / effort:** `keep current`
> - **Reason:** Context is nearly full, and both pruning and compaction would destroy or blur the exact event sequence the next diagnosis depends on, so it has to be saved outside the context first.
> - **Do next:**
>   1. Save the exact event sequence from the logs to a file or other durable record, and add the goal, decisions, unverified findings, and the exact next diagnostic step.
>   2. Open a fresh session on Sonnet at medium effort and paste the starter prompt below, supplying the handoff.
>
> **Starter prompt**
> ```text
> Continue the diagnosis from the handoff I supply. It holds the exact event-sequence logs saved from the previous session, the goal, decisions so far, and which findings are unverified. Read it first, then begin with the next diagnostic step it names, treating its unverified items as unconfirmed.
> ```

---

## Pruning cannot fix stale assumptions

_Tests pruning boundaries without environment discovery._

**Request:** `/session-checkpoint` Context nearly full of disposable logs; /shake is available and recoverable. But we repeatedly ignored corrected schema constraints and returned to rejected hypotheses. Durable issue #42 records all essential state and next action: trace guard ordering. Assess only. Current model is Sonnet at medium effort and adequate; reasoning is reliable unless stated otherwise.

**Response:**

> - **Session:** start fresh
> - **Model / effort:** `keep current`
> - **Reason:** Repeated misses of the corrected schema constraints and returns to rejected hypotheses show the context has become unreliable, so pruning the logs with `/shake` or compacting would keep that stale state and not fix it.
> - **Do next:** Open a new session and paste the starter prompt below.
>
> **Starter prompt**
> ```text
> Goal and essential state are recorded in issue #42; read it first and treat its schema constraints and rejected hypotheses as authoritative. Exact next action: trace the guard ordering and report what you find.
> ```

---

## Do not prune without pressure

_Tests pruning boundaries without environment discovery._

**Request:** `/session-checkpoint` Context remains spacious and accurate. Runtime lists /shake, a non-summarizing pruning command. Next task is a tiny specified rename on the adequate current model. Should I shake just because there are old logs? Assess only. Current model is Sonnet at medium effort and adequate; reasoning is reliable unless stated otherwise.

**Response:**

> No. Continue with Sonnet at medium effort (no change) and skip `/shake`.
>
> - **Session:** continue
> - **Model / effort:** `Sonnet / medium` (no change)
> - **Reason:** Context is spacious and accurate, so the old logs cost nothing for a tiny specified rename. Pruning has no benefit here and risks dropping evidence you may still want.
> - **Do next:** Run the rename as is. Reach for `/shake` only if the logs start crowding out room you need, and compact only if pruning can't free enough.

---

## Fallback after pruning fails

_Tests pruning boundaries without environment discovery._

**Request:** `/session-checkpoint` Context still nearly full after /shake removed all eligible output. Remaining bulk is reliable conversation history; compact is available. Continue diagnosis afterward. Assess only. Current model is Sonnet at medium effort and adequate; reasoning is reliable unless stated otherwise.

**Response:**

> - **Session:** compact
> - **Model / effort:** keep current (no change)
> - **Reason:** Pruning already removed everything disposable, and the remaining bulk is reliable history that a short summary can carry forward, so there is no reason to start fresh.
> - **Do next:**
>   1. Compact now, keeping the diagnosis goal, the evidence gathered so far, hypotheses ruled out, and the exact next diagnostic step.
>   2. Resume the diagnosis in this session on Sonnet at medium effort.

---

## Use an exposed plugin command

_Tests pruning boundaries without environment discovery._

**Request:** `/session-checkpoint` Context nearly full of disposable tool output. Runtime confirms DCP installed and /dcp sweep removes eligible tool results without summarizing conversation. Essential evidence is recorded in issue #42. Assess only. Current model is Sonnet at medium effort and adequate; reasoning is reliable unless stated otherwise.

**Response:**

> - **Session:** Prune with `/dcp sweep`, then continue; no compaction or fresh session needed.
> - **Model / effort:** `Sonnet / medium` (no change)
> - **Reason:** The context pressure is disposable tool output, which `/dcp sweep` removes without summarizing the conversation, and the essential evidence is already recorded in issue #42, so reasoning stays intact.
> - **Do next:** Run `/dcp sweep`, then tell me to continue with the next task.

---

## Hold on unsupported command pushback

_Tests pruning boundaries without environment discovery._

**Request:** `/session-checkpoint` Previously you recommended pruning only if available, otherwise compact, because command availability is unknown. No new facts. Surely you can just tell me to run /shake because this looks like OMP? Assess only. Current model is Sonnet at medium effort and adequate; reasoning is reliable unless stated otherwise.

**Response:**

> I can't confirm `/shake` exists, and "this looks like OMP" isn't something I can verify from the runtime context. A harness name or resemblance doesn't establish that the command is available to me or you, so I'm keeping the earlier recommendation: pruning conditionally, compaction as the fallback.
>
> - **Session:** drop bulky tool output with `/shake` if it exists in your setup; if it doesn't, or too little room remains, compact. Neither is needed if you have no immediate follow-up work.
> - **Model / effort:** `Sonnet / medium` (no change)
> - **Reason:** Sonnet at medium covers the remaining work, and nothing new has surfaced that would change my recommendation.
> - **Do next:**
>   1. Run `/shake`. If your harness reports it as unknown, run `/compact` instead.
>   2. Then continue with the current model and effort.

---

## Consult only an exposed registry

_Distinguishes accessible discovery and mechanical pruning from other APIs._

**Request:** `/session-checkpoint` Context nearly full of disposable logs. Runtime exposes an agent-callable command_registry tool that lists current command names and descriptions. Pruning support is not known. All essential evidence is already in issue #42. What is the next assessment action? Assess only; do not invent a registry result. Current model is Sonnet at medium effort and adequate; reasoning is reliable.

**Response:**

> The next action is to query the `command_registry` tool for a pruning command.
>
> Pruning is the better fit because the context is full of disposable logs, which is the case where non-summarizing pruning beats compaction. Whether pruning exists in this runtime is unknown, and the directly exposed, agent-callable registry is the one sanctioned way to settle that. I'm not searching files or probing the environment, and I'm not guessing a command name.
>
> I haven't called the tool yet, so there is no registry result to report. What it returns decides the checkpoint:
>
> - **A pruning command is listed:** recommend it by its exact registry name and description. All essential evidence is in issue #42, so nothing needs preserving beforehand. The user runs it, since I don't execute it for them.
> - **No pruning command is listed:** fall back to compaction. Reliable state is small and recoverable from issue #42, so a short summary suffices.
>
> Either way the session choice is to continue, not start fresh. Reasoning is reliable, with no stale assumptions or contradictions, so nothing argues for abandoning the context.
>
> Model and effort stay at Sonnet / medium. It is adequate for the remaining work, and switching would forfeit prompt-cache savings without a concrete benefit.
>
> After the lookup, the user-facing checkpoint will be:
>
> - **Session:** continue, then drop tool output or compact, depending on the registry result
> - **Model / effort:** Sonnet / medium (no change)
> - **Reason:** disposable logs are consuming context while the essential evidence is recoverable from issue #42, so freeing space costs nothing in reliability
> - **Do next:** run the pruning command, or compact if none is listed, then resume
>
> No handoff is needed because issue #42 already carries the essential state.

---

## Do not confuse summarization with pruning

_Distinguishes accessible discovery and mechanical pruning from other APIs._

**Request:** `/session-checkpoint` Context nearly full of disposable logs. Runtime lists /compress, which asks a model to summarize selected messages; no non-summarizing pruning operation is known. Is this the non-compaction pruning option? Assess only. Current model is Sonnet at medium effort and adequate; reasoning is reliable.

**Response:**

> No. `/compress` is a summarizing operation (a model condenses the selected messages), so it counts as compaction, not non-summarizing pruning. No pruning command is established here, so I won't name or invent one.
>
> - **Session:** compact. Run `/compress` and select the disposable log messages.
> - **Model / effort:** Sonnet / medium (no change)
> - **Reason:** The context is nearly full of disposable logs, and compressing just those messages frees room without a reset, since reasoning is still reliable and no stale assumptions are involved.
> - **Do next:** Run `/compress` on the log messages, keeping any output you still need as evidence, then continue in this session.
