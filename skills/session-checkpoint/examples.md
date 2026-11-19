# session-checkpoint examples

Real, unedited responses from the final [`session-checkpoint`](SKILL.md) skill, generated on 2026-11-18. Each request used a fresh stateless call with the skill body and bundled [default model mapping](model-mapping.md) as system context, without tools. The harness selected its default model; the concrete model ID was not exposed.

Technical claims are unverified. These hypothetical scenarios show decision and output behavior, not measured savings, live command discovery, tool execution, or automatic invocation. Custom deployment aliases are hypothetical. Runtime command descriptions are scenario inputs, not a current command catalog. Tool-dependent requests cannot establish same-turn execution here. Explicit assessments without execution instructions stop after the assessment; internal checks during work remain silent unless intervention is needed.

This file is for people. `SKILL.md` does not reference or load it.

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

> Continue with Terra at medium effort (no change); the context remains useful, and running the web app’s `npm test` needs no transition.

---

## Compact without resetting

_Preserves reliable state when the harness reports context pressure._

**Request:** `/session-checkpoint` The harness reports little context remains. Reasoning has been consistent while debugging a Vite build. We need a concise summary preserving the failing build command, config changes, and pending verification, then continue the same task. Current model is Sonnet at medium effort. Assess only.

**Response:**

> - **Session:** compact.
> - **Model / effort:** Sonnet / medium (no change).
> - **Reason:** Context is nearly exhausted, but consistent reasoning makes a concise summary preferable to a fresh start.
> - **Do next:**
>   1. Compact, preserving the exact failing build command and error, config changes, and pending verification; mark unverified changes explicitly.
>   2. Resume the Vite debugging task from the pending verification.

---

## Offer dropping tool output when availability is unknown

_Names dropping tool output as a conditional first choice, with compact as the fallback._

**Request:** `/session-checkpoint` Context is nearly full, almost all of it large test logs and file dumps from earlier tool calls. The conversation and reasoning have been consistent and are still useful. Next I will continue fixing the failing checkout test. Current model is Sonnet at medium effort. Assess only.

**Response:**

> - **Session:** drop bulky tool output if supported; otherwise compact.
> - **Model / effort:** Sonnet / medium (no change)
> - **Reason:** Context space is nearly exhausted, but the consistent reasoning remains useful; a fresh session would discard valuable state.
> - **Do next:**
>   1. If your runtime supports pruning, remove recoverable logs and file dumps while retaining key failure evidence. Otherwise compact, preserving the checkout-test failure, unfinished changes, decisions, verification results, and exact next action.
>   2. Resume fixing the failing checkout test.

---

## Use a drop command the runtime exposes

_Recommends the harness's own command when runtime context lists it._

**Request:** `/session-checkpoint` Context is nearly full, almost all of it large test logs and file dumps from earlier tool calls. The conversation and reasoning have been consistent and are still useful. Runtime context lists these user commands: `/shake` (drops stale tool output from context) and `/compact` (summarizes the conversation). Next I will continue fixing the failing checkout test. Current model is Sonnet at medium effort. Assess only.

**Response:**

> - **Session:** drop tool output.
> - **Model / effort:** Sonnet / medium (no change)
> - **Reason:** Pruning bulky logs and dumps frees context while preserving the useful, consistent conversation.
> - **Do next:** Run `/shake`, preserving essential failure evidence or ensuring it can be recovered.

---

## Start fresh using existing records

_Preserves unfinished changes and a failing reproduction in a paste-ready prompt._

**Request:** `/session-checkpoint` We repeatedly used obsolete React Router API assumptions despite corrections. Should we start fresh? Existing docs/routing-investigation.md records the goal, decisions, and reproduction command. src/routes.tsx has an unverified edit. The latest reproduction still redirects /settings to /login after a successful sign-in. No other blocker. Next trace route guards before editing.

**Response:**

> - **Session:** start fresh.
> - **Model / effort:** keep current.
> - **Reason:** Repeated obsolete API assumptions despite corrections make this context unreliable; the existing record and a short starter prompt are sufficient.
> - **Do next:** Open a fresh session and paste the starter prompt below.
>
> **Starter prompt**
> ```text
> Investigate why /settings still redirects to /login after a successful sign-in. Read docs/routing-investigation.md for the goal, decisions, and reproduction command. src/routes.tsx contains an unverified edit; the latest reproduction still fails. Previous investigation repeatedly relied on obsolete React Router APIs despite corrections, so verify the installed version and applicable APIs rather than carrying those assumptions forward. Next, trace the route guards before making any edits. No other blocker is known.
> ```

---

## Ask for the deciding fact

_Asks for the next task instead of guessing whether a downgrade is worthwhile._

**Request:** `/session-checkpoint` Should we switch to a cheaper model? I have not said what the next task is. Current model is Sol at medium effort. There is no evidence of context trouble.

**Response:**

> Keep current: Sol at medium effort (no change). What is the next task?

---

## Hold the recommendation on pushback

_Keeps the recommendation when no facts change._

**Request:** `/session-checkpoint` Earlier you recommended staying because context is accurate and the next task is a mechanical CSS class rename. No facts have changed. But surely a long session means we must start fresh? Current model is Haiku at low effort.

**Response:**

> Continue with Haiku at low effort (no change). Session length alone doesn’t justify starting fresh; the accurate context still supports the mechanical CSS class rename.

---

## Leave handoff creation to the user

_Recommends a handoff without writing it or inventing its location._

**Request:** `/session-checkpoint` Start a fresh session for a difficult intermittent cache invalidation bug. Several corrected assumptions keep recurring. The reproduction, rejected hypotheses, and unverified changes exist only in this conversation. No durable record exists. Next trace the invalidation event order before editing. I use OpenAI models and prefer to create any handoff myself with my own command. Assess only; do not write files.

**Response:**

> - **Session:** start fresh with handoff.
> - **Model / effort:** Sol / high
> - **Reason:** Recurring corrected assumptions undermine reliability; a handoff preserves essential evidence that exists only in this conversation.
> - **Do next:**
>   1. Create a handoff using your own command, capturing the reproduction, rejected hypotheses and corrected assumptions, unverified changes, and remaining blockers.
>   2. Open a fresh session with Sol at high effort, supply the handoff, and paste the starter prompt below.
>
> **Starter prompt**
> ```text
> Investigate the intermittent cache invalidation bug. Read the handoff I supply for the reproduction, rejected hypotheses, corrected assumptions, unverified changes, and blockers. Treat those changes as unverified. First trace the invalidation event order against the reproduction before making any edits.
> ```

---

## Avoid a short-lived downgrade

_Considers switching costs rather than downgrading for a small mechanical task._

**Request:** `/session-checkpoint` Current model is Astra / high. The difficult architecture decision is finished and documented. The next task is a mechanical rename of a private CSS class in two known files, with exact replacements supplied and npm test afterward. Context is accurate. The harness supports switching models in place without rebuilding context. Assess before continuing.

**Response:**

> Continue with Astra / high (no change); this small remaining task does not justify switching overhead. Please provide the two file paths and exact replacements so I can apply the rename and run `npm test`.

---

## Increase effort without changing the model

_Pauses for an effort decision that affects the quality of the remaining analysis._

**Request:** `/session-checkpoint` Current model is Sol / low. We have a deterministic reproduction of an event-ordering race across three async functions. The relevant code and evidence are already in context and assumptions are accurate. Next task is carefully tracing event interleavings before editing. I want to keep the current model if suitable; effort can change in place. Assess only.

**Response:**

> - **Session:** continue
> - **Model / effort:** Sol / high
> - **Reason:** Accurate context supports continuation; tracing async interleavings warrants higher effort, not a model switch or reset.
> - **Do next:** Raise effort to high in place before tracing the event interleavings.

---

## Handle missing task and settings

_Asks for the missing task without inventing current settings._

**Request:** `/session-checkpoint` Before continuing, assess the session. I have not provided the next task or the current model and effort settings. No task history is available.

**Response:**

> Keep current model and effort. What task would you like to work on next?

---

## Upgrade before risky work

_Pauses before consequential work while preserving useful session context._

**Request:** `/session-checkpoint` Current model is Haiku / low. Context is reliable and includes current schema and security constraints. Next is deciding a multi-tenant database migration strategy with rollback and data-isolation risks. The harness supports switching models without losing context. Assess only.

**Response:**

> - **Session:** continue
> - **Model / effort:** Opus / high
> - **Reason:** Reliable context should be retained, but tenant-isolation and rollback risks warrant stronger reasoning than Haiku / low.
> - **Do next:** Switch to Opus / high before evaluating the migration strategy.

---

## Keep working through marginal effort changes

_Keeps the current setup across a file read followed by analysis._

**Request:** `/session-checkpoint` Current model is Opus / high. You are authorized to finish diagnosing a complex race and verifying the fix without further approval. Context is accurate. The next operation is reading one short source file, followed immediately by difficult event-order analysis. Should we lower effort just for that read? Assess only.

**Response:**

> Continue with Opus / high (no change); lowering effort for one short read before difficult event-order analysis offers no meaningful savings.

---

## Account for lost cache savings

_Rejects a nominally cheaper model when losing the warm cache makes the switch cost more._

**Request:** `/session-checkpoint` Current model is Sol / medium. Context is accurate. Only a small, bounded implementation and verification remain. Terra can perform the work reliably. The runtime reports that switching models keeps the conversation but loses its warm prompt cache; the estimated cache refill alone costs more than all remaining generation savings. Should we switch? Assess only.

**Response:**

> Continue with Sol / medium (no change); switching to Terra would cost more in cache refill than it saves on the remaining work.

---

## Pause for sustained net savings

_Treats a material net cost reduction as a reason to ask the user before continuing._

**Request:** `/session-checkpoint` Current model is Astra / high. Architecture is complete and documented. You are authorized to finish a large batch of exact, independent mechanical edits and their specified verification. Luna / low is available and reliable for this work. The runtime cost estimate includes cache loss, rebuilding context, verification, and likely later upgrades; switching still cuts the remaining total cost substantially. Context is accurate. Apply the checkpoint before proceeding.

**Response:**

> - **Session:** continue
> - **Model / effort:** Luna / low
> - **Reason:** Your accurate context can stay in place, and switching substantially reduces total remaining cost while retaining reliable execution.
> - **Do next:**
>   1. Switch to Luna with low effort in this session.
>   2. Resume the authorized mechanical edits and their specified verification.

---

## Change vendors when a quota runs out

_Uses the mapping for an alternative without treating it as proof of identity or identical settings._

**Request:** `/session-checkpoint` Current model is Opus / high. The provider reports that the Anthropic quota is exhausted; no further work can run there. I have access to OpenAI Sol with high effort. The remaining task is difficult diagnosis, and the existing conversation and investigation record are reliable. I can switch vendors in place; the prompt cache will be lost. Assess only and recommend how to resume.

**Response:**

> - **Session:** continue
> - **Model / effort:** Sol / high
> - **Reason:** Anthropic’s exhausted quota requires a vendor switch; the reliable investigation context remains useful despite losing the prompt cache.
> - **Do next:**
>   1. Switch in place to OpenAI Sol with high effort.
>   2. Resume the difficult diagnosis using the existing conversation and investigation record.

---

## Ask for a model when no mapping applies

_Asks for an exact available model rather than directing the user to an ambiguous tier._

**Request:** `/session-checkpoint` No current model, vendor, or effort is exposed. My available vendor is not Anthropic or OpenAI, and I have not supplied its model mapping. Next is deciding a risky multi-tenant data migration with rollback and isolation constraints. Existing context is reliable. Assess only; do not guess a model name.

**Response:**

> - **Session:** continue
> - **Model / effort:** selection needed (Strong tier) / high
> - **Reason:** Keep the reliable context; migration, rollback, and tenant-isolation risks warrant Strong capability and high effort.
> - **Do next:** Supply your vendor’s model-to-tier mapping, or identify an available model suitable for Strong-tier analysis.

---

## Keep internal checks silent

_Continues authorized work without emitting another checkpoint or asking for approval._

**Request:** `/session-checkpoint` You are using these rules for an internal check, not responding to an explicit checkpoint request. Current model is Haiku / low. The user authorized you to finish labeling the supplied records without further approval. Context is accurate and no transition or material cost decision is needed. One record remains. Apply the existing rule: label a record urgent if its severity is 4 or greater; otherwise label it routine. Record: ID R7, severity 5. Complete the authorized work now.

**Response:**

> R7: urgent

---

## Use models from other vendors

_Uses exact user-supplied deployment aliases rather than the bundled vendors._

**Request:** `/session-checkpoint` My already-loaded agent instructions say: For session-checkpoint, replace the default mapping with Strongest: unavailable; Strong: Kimi / review-deployment; Standard: Meta / build-deployment; Light: SpaceX / batch-deployment. These are hypothetical user-defined deployment aliases, not public model names. Current model is SpaceX / batch-deployment at low effort. Next is deciding a risky multi-tenant migration with rollback and tenant-isolation constraints. Context is accurate. Assess only before that work.

**Response:**

> - **Session:** continue
> - **Model / effort:** Kimi / review-deployment / high
> - **Reason:** Your context is accurate, but the migration’s rollback and tenant-isolation risks warrant stronger reasoning than the current Light deployment at low effort.
> - **Do next:**
>   1. Switch to Kimi / review-deployment at high effort.
>   2. Resume the migration decision in this session.

---

## Do not fill omitted tiers from defaults

_Asks for an exact model when a partial override leaves the required tier unmapped._

**Request:** `/session-checkpoint` For session-checkpoint, replace the defaults with this complete user mapping: Strong = Kimi / review-deployment. This is a hypothetical deployment alias. All other tiers are omitted. Current model is Kimi / review-deployment at high effort. A large batch of exact mechanical edits and specified verification remains. My runtime estimate establishes substantial net savings from moving this batch to a Light-tier model, after cache loss, switching, and likely upgrades. I have not identified that model. Assess only; do not guess a model or use bundled models.

**Response:**

> - **Session:** continue
> - **Model / effort:** selection needed (Light tier) / low
> - **Reason:** Your estimate establishes substantial net savings for the mechanical batch, with no evidence that the session needs resetting.
> - **Do next:** Which available model should fill the Light tier in your mapping?

---

## Do not interrupt for an irrelevant mapping gap

_Completes authorized work without requesting mappings that do not affect it._

**Request:** `/session-checkpoint` This is an internal check, not an explicit checkpoint request. My existing instructions replace the default mapping with Standard = Meta / build-deployment, a hypothetical deployment alias; other tiers are omitted. Current model is Kimi / review-deployment at medium effort and is known to be reliable for this task. There is no material cost or outcome benefit to changing settings. You are authorized to finish labeling records without further approval: severity 4 or greater is urgent; otherwise routine. The last record is ID R8, severity 2. Complete the work.

**Response:**

> R8: routine.

---

## Prefer the user mapping over runtime defaults

_Resolves a recommendation from the user override rather than the runtime mapping._

**Request:** `/session-checkpoint` The runtime supplies this tier mapping: Strong = OpenAI Sol, Light = OpenAI Luna. My user override for session-checkpoint replaces it with Strong = Meta / reasoning-deployment, Light = Kimi / batch-deployment; these are hypothetical deployment aliases and other tiers are omitted. Current model is Kimi / batch-deployment at low effort. Next is deciding a security-sensitive multi-tenant migration with rollback and isolation risks; Strong-tier analysis is needed. Context is reliable. Assess only and identify the concrete model to use.

**Response:**

> - **Session:** continue.
> - **Model / effort:** Meta / reasoning-deployment / high.
> - **Reason:** Context remains reliable, but the migration’s rollback and tenant-isolation risks require Strong-tier analysis using your override.
> - **Do next:** Switch to Meta / reasoning-deployment at high effort before analyzing the migration.

---

## Keep subsystem context for a new task

_Treats a change from debugging to feature work as a context decision, not an automatic reset._

**Request:** `/session-checkpoint` I finished fixing an invoicing bug. Now I want to add invoice filtering in the same subsystem. The loaded schema, constraints, and test setup are current and useful; no recurring corrected assumptions or context pressure. Current model is Sonnet / medium, and it is adequate for the feature. Should I start a new session? Assess only.

**Response:**

> Continue in this session with Sonnet / medium (no change); the current context remains useful and reliable.

---

## Append advice after completing the request

_Keeps the normal final report first and adds a useful transition recommendation afterward._

**Request:** This is an internal checkpoint at completion, not a manual checkpoint request. The user asked only for a routing investigation, which is now complete. Report these observed results: /settings redirects to /login because the route guard reads the obsolete session flag; no files changed; the supplied reproduction still fails. The next task, already identified by the user but not authorized yet, is implementing the fix. We repeatedly returned to obsolete API assumptions despite corrections, so a fresh session would reduce that risk. docs/routing-investigation.md contains the goal, evidence, rejected assumptions, reproduction, and exact next action: update the guard to use the current auth state, then rerun the reproduction. Current model is Opus / high and adequate. Give the normal final report and apply the skill.

**Response:**

> The routing investigation is complete:
> - `/settings` redirects to `/login` because the route guard reads the obsolete session flag.
> - No files changed.
> - The supplied reproduction still fails.
>
> The findings and exact next action are recorded in `docs/routing-investigation.md`. Implementing the fix has not yet been authorized.
>
> - **Session:** start fresh.
> - **Model / effort:** Opus / high (no change).
> - **Reason:** A fresh session anchored to the investigation record will reduce repeated reliance on obsolete API assumptions.
> - **Do next:** When ready to authorize the fix, open a fresh session with the prompt below.
>
> **Starter prompt**
> ```text
> Implement the routing fix documented in docs/routing-investigation.md. Read that record for the goal, evidence, rejected assumptions, and reproduction, and follow applicable repository instructions. Update the route guard to use the current auth state, then rerun the supplied reproduction and report the result; the investigation made no file changes, and the reproduction still failed.
> ```

---

## Finish without an automatic checkpoint

_Omits checkpoint boilerplate and next-task questions when the requested work is done._

**Request:** This is an internal checkpoint at completion, not a manual checkpoint request. The user requested exactly this calculation: 18 * 24. The calculation is complete: 432. No next task is known. Context is reliable and there is no material model or session issue. Give the final answer.

**Response:**

> 432

---

## Continue beyond an internal phase boundary

_Finishes authorized work instead of treating an intermediate result as a stopping point._

**Request:** This is an internal check, not a manual checkpoint request. The user authorized calculating a subtotal, adding 8% tax, and giving the total. The subtotal phase is finished: 125. Tax and total remain authorized, and you can calculate them now without tools. Context is accurate, current setup is adequate, and there is no material transition benefit. Continue the request to completion.

**Response:**

> Subtotal: $125.00  
> Tax (8%): $10.00  
> **Total: $135.00**

---

## Recommend a reset without inventing the next task

_Allows completion-time context advice without asking for or inventing future work._

**Request:** This is an internal checkpoint at completion, not a manual checkpoint request. The user requested only a configuration audit. It is complete: found one obsolete timeout setting, made no changes, and recorded all findings and verification limits in docs/config-audit.md. We repeatedly ignored corrections and reused stale assumptions during the audit, so continuing this conversation would be unreliable. No next task is known. Current model is Opus / high, adequate apart from the context issue. Give the final report and any warranted transition advice. Do not start another task.

**Response:**

> Configuration audit complete. Found one obsolete timeout setting; no changes were made. Findings and verification limits are recorded in `docs/config-audit.md`.
>
> - **Session:** start fresh.
> - **Model / effort:** Opus / high (no change).
> - **Reason:** Repeatedly ignored corrections and reused stale assumptions make this conversation unreliable for future work; the audit record preserves the findings.
> - **Do next:** When you have another task, open a fresh session using the starter prompt below.
>
> **Starter prompt**
> ```text
> The configuration audit is complete. It found one obsolete timeout setting, and no changes were made. Findings and verification limits are recorded in docs/config-audit.md; use that record rather than assumptions from the prior conversation. Ask me for the next task before starting any work.
> ```

---

## Do not infer commands from a harness name

_Tests pruning boundaries without environment discovery._

**Request:** `/session-checkpoint` Context nearly full of disposable logs. I use OMP but no command list or pruning instructions are available. Assess only. Do not assume installed commands. Current model is Sonnet at medium effort and adequate; reasoning is reliable unless stated otherwise.

**Response:**

> - **Session:** Drop disposable log output if supported; otherwise compact.
> - **Model / effort:** Sonnet / medium (no change)
> - **Reason:** Context space is constrained, but reliable reasoning and an adequate model do not justify a fresh session or model switch.
> - **Do next:** Use non-summarizing pruning only if you can confirm support; otherwise compact, preserving the goal, unfinished changes, decisions, verification results, blockers, and exact next action.

---

## Do not call an extension API

_Tests pruning boundaries without environment discovery._

**Request:** `/session-checkpoint` Context nearly full of disposable logs. OpenCode plugin docs mention ctx.command.list(), but your only agent tool is read and no registry is exposed. Can you discover a pruning command? Assess only. Current model is Sonnet at medium effort and adequate; reasoning is reliable unless stated otherwise.

**Response:**

> - **Session:** drop disposable logs if pruning is available; otherwise compact.
> - **Model / effort:** Sonnet / medium (no change)
> - **Reason:** Context is constrained, but reasoning remains reliable; plugin docs do not establish agent access, so I cannot discover a pruning command through `read`.
> - **Do next:** Use pruning only if your interface exposes it; otherwise compact, preserving essential task state and verification evidence.

---

## Protect essential old evidence

_Tests pruning boundaries without environment discovery._

**Request:** `/session-checkpoint` Context nearly full of old logs. Those logs are the only copy of the exact event sequence needed for the next diagnosis; recovery is not established. Runtime offers /trim which permanently deletes all tool output. Assess only. Current model is Sonnet at medium effort and adequate; reasoning is reliable unless stated otherwise.

**Response:**

> - **Session:** compact.
> - **Model / effort:** Sonnet / medium (no change).
> - **Reason:** Reasoning remains reliable, but space is constrained; `/trim` would permanently destroy the only copy of essential evidence.
> - **Do next:**
>   1. Preserve the exact event sequence losslessly outside the context and verify it is accessible before removing anything.
>   2. Compact, retaining the diagnosis goal and the saved evidence’s location, then resume diagnosis.

---

## Pruning cannot fix stale assumptions

_Tests pruning boundaries without environment discovery._

**Request:** `/session-checkpoint` Context nearly full of disposable logs; /shake is available and recoverable. But we repeatedly ignored corrected schema constraints and returned to rejected hypotheses. Durable issue #42 records all essential state and next action: trace guard ordering. Assess only. Current model is Sonnet at medium effort and adequate; reasoning is reliable unless stated otherwise.

**Response:**

> - **Session:** start fresh
> - **Model / effort:** Sonnet / medium (no change)
> - **Reason:** Repeatedly ignoring corrected constraints and revisiting rejected hypotheses indicates stale reasoning that dropping logs with `/shake` would not repair; issue #42 preserves the essential state.
> - **Do next:** Open a fresh session with the same settings and paste the starter prompt below.
>
> **Starter prompt**
> ```text
> Continue the work recorded in durable issue #42. Read it for the goal, corrected schema constraints, rejected hypotheses, verification status, and next action. Preserve those constraints and do not reopen rejected hypotheses without new evidence. Begin by tracing guard ordering.
> ```

---

## Do not prune without pressure

_Tests pruning boundaries without environment discovery._

**Request:** `/session-checkpoint` Context remains spacious and accurate. Runtime lists /shake, a non-summarizing pruning command. Next task is a tiny specified rename on the adequate current model. Should I shake just because there are old logs? Assess only. Current model is Sonnet at medium effort and adequate; reasoning is reliable unless stated otherwise.

**Response:**

> Continue with Sonnet at medium effort (no change); don’t run `/shake` just because old logs remain when context is spacious and reliable.

---

## Fallback after pruning fails

_Tests pruning boundaries without environment discovery._

**Request:** `/session-checkpoint` Context still nearly full after /shake removed all eligible output. Remaining bulk is reliable conversation history; compact is available. Continue diagnosis afterward. Assess only. Current model is Sonnet at medium effort and adequate; reasoning is reliable unless stated otherwise.

**Response:**

> - **Session:** compact
> - **Model / effort:** Sonnet / medium (no change)
> - **Reason:** Pruning is exhausted; compaction can preserve reliable diagnostic state while freeing context.
> - **Do next:**
>   1. Compact, preserving the diagnosis goal, evidence, ruled-out hypotheses, blockers, and exact next action.
>   2. Resume diagnosis with the current model and effort.

---

## Use an exposed plugin command

_Tests pruning boundaries without environment discovery._

**Request:** `/session-checkpoint` Context nearly full of disposable tool output. Runtime confirms DCP installed and /dcp sweep removes eligible tool results without summarizing conversation. Essential evidence is recorded in issue #42. Assess only. Current model is Sonnet at medium effort and adequate; reasoning is reliable unless stated otherwise.

**Response:**

> - **Session:** drop tool output.
> - **Model / effort:** Sonnet / medium (no change).
> - **Reason:** Removing disposable tool results frees context without summarizing reliable reasoning; essential evidence is preserved in issue #42.
> - **Do next:** Run `/dcp sweep`.

---

## Hold on unsupported command pushback

_Tests pruning boundaries without environment discovery._

**Request:** `/session-checkpoint` Previously you recommended pruning only if available, otherwise compact, because command availability is unknown. No new facts. Surely you can just tell me to run /shake because this looks like OMP? Assess only. Current model is Sonnet at medium effort and adequate; reasoning is reliable unless stated otherwise.

**Response:**

> - **Session:** compact, or drop tool output if supported.
> - **Model / effort:** Sonnet / medium (no change)
> - **Reason:** Looking like OMP does not establish `/shake` availability; no new evidence changes the conditional recommendation.
> - **Do next:** Use pruning only if its availability is confirmed; otherwise compact.

---

## Consult only an exposed registry

_Distinguishes accessible discovery and mechanical pruning from other APIs._

**Request:** `/session-checkpoint` Context nearly full of disposable logs. Runtime exposes an agent-callable command_registry tool that lists current command names and descriptions. Pruning support is not known. All essential evidence is already in issue #42. What is the next assessment action? Assess only; do not invent a registry result. Current model is Sonnet at medium effort and adequate; reasoning is reliable.

**Response:**

> Consult `command_registry` to check for non-summarizing pruning of disposable logs. Do not assume a result: recommend pruning if supported, otherwise compaction, preserving issue #42 as the essential record.
>
> Keep Sonnet / medium (no change).

---

## Do not confuse summarization with pruning

_Distinguishes accessible discovery and mechanical pruning from other APIs._

**Request:** `/session-checkpoint` Context nearly full of disposable logs. Runtime lists /compress, which asks a model to summarize selected messages; no non-summarizing pruning operation is known. Is this the non-compaction pruning option? Assess only. Current model is Sonnet at medium effort and adequate; reasoning is reliable.

**Response:**

> - **Session:** compact.
> - **Model / effort:** Sonnet / medium (no change).
> - **Reason:** `/compress` summarizes messages, so it is compaction—not non-summarizing pruning—and can relieve context pressure without discarding reliable task state.
> - **Do next:** Use `/compress` on the log-heavy context, preserving the goal, unfinished changes, decisions, verification results, blockers, and exact next action.
