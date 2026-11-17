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

> Continue with Terra at medium effort (no change).

---

## Compact without resetting

_Preserves reliable state when the harness reports context pressure._

**Request:** `/session-checkpoint` The harness reports little context remains. Reasoning has been consistent while debugging a Vite build. We need a concise summary preserving the failing build command, config changes, and pending verification, then continue the same task. Current model is Sonnet at medium effort. Assess only.

**Response:**

> - **Session:** compact
> - **Handoff:** no
> - **Model / effort:** Sonnet / medium (no change)
> - **Reason:** Context is nearly exhausted, but the debugging remains consistent; a concise summary can preserve the reliable state.
> - **Do next:**
>   1. Compact, preserving the exact failing build command and error, config changes, and pending verification; mark unverified changes explicitly.
>   2. Resume the same Vite debugging task and run the pending verification.

---

## Offer dropping tool output when availability is unknown

_Names dropping tool output as a conditional first choice, with compact as the fallback._

**Request:** `/session-checkpoint` Context is nearly full, almost all of it large test logs and file dumps from earlier tool calls. The conversation and reasoning have been consistent and are still useful. Next I will continue fixing the failing checkout test. Current model is Sonnet at medium effort. Assess only.

**Response:**

> - **Session:** Drop bulky tool output if supported; otherwise compact.
> - **Handoff:** No.
> - **Model / effort:** Sonnet / medium (no change).
> - **Reason:** Removing bulky logs and file dumps will free context while preserving useful reasoning and avoiding a fresh investigation.
> - **Do next:**
>   1. If your runtime supports non-summarizing pruning, remove recoverable logs and dumps while retaining the checkout failure evidence, unfinished changes, and verification state. Otherwise, compact with those essentials and the exact next action preserved.
>   2. Resume fixing the failing checkout test.

---

## Use a drop command the runtime exposes

_Recommends the harness's own command when runtime context lists it._

**Request:** `/session-checkpoint` Context is nearly full, almost all of it large test logs and file dumps from earlier tool calls. The conversation and reasoning have been consistent and are still useful. Runtime context lists these user commands: `/shake` (drops stale tool output from context) and `/compact` (summarizes the conversation). Next I will continue fixing the failing checkout test. Current model is Sonnet at medium effort. Assess only.

**Response:**

> - **Session:** drop tool output with `/shake`; keep this session.
> - **Handoff:** no.
> - **Model / effort:** Sonnet / medium (no change).
> - **Reason:** Removing bulky tool output preserves useful reasoning and frees context without the information loss of summarization.
> - **Do next:** Run `/shake`, preserving the latest checkout failure details or confirming they can be reproduced; then continue fixing the failing checkout test.

---

## Start fresh using existing records

_Preserves unfinished changes and a failing reproduction in a paste-ready prompt._

**Request:** `/session-checkpoint` We repeatedly used obsolete React Router API assumptions despite corrections. Should we start fresh? Existing docs/routing-investigation.md records the goal, decisions, and reproduction command. src/routes.tsx has an unverified edit. The latest reproduction still redirects /settings to /login after a successful sign-in. No other blocker. Next trace route guards before editing.

**Response:**

> - **Session:** Start fresh.
> - **Handoff:** No; the existing investigation document plus the starter prompt below carries the essential state.
> - **Model / effort:** Keep current model and effort.
> - **Reason:** Repeated obsolete API assumptions despite corrections indicate stale context that could undermine the route-guard investigation.
> - **Do next:** Open a fresh session and paste this prompt.
>
> **Starter prompt**
> ```text
> Investigate why /settings still redirects to /login after a successful sign-in. Read repository instructions and docs/routing-investigation.md for the goal, decisions, and reproduction command. src/routes.tsx contains an unverified edit; the latest reproduction still fails, and there are no other known blockers. Prior work repeatedly relied on obsolete React Router APIs despite corrections, so verify API assumptions against the installed version. Next, trace the route guards before making further edits.
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

> Continue with Haiku at low effort (no change). Session length alone does not justify starting fresh; the context remains accurate and useful for the mechanical CSS class rename.

---

## Leave handoff creation to the user

_Recommends a handoff without writing it or inventing its location._

**Request:** `/session-checkpoint` Start a fresh session for a difficult intermittent cache invalidation bug. Several corrected assumptions keep recurring. The reproduction, rejected hypotheses, and unverified changes exist only in this conversation. No durable record exists. Next trace the invalidation event order before editing. I use OpenAI models and prefer to create any handoff myself with my own command. Assess only; do not write files.

**Response:**

> - **Session:** Start fresh.
> - **Handoff:** Recommended; create it yourself with your own command.
> - **Model / effort:** OpenAI Sol / high.
> - **Reason:** Recurring corrected assumptions make continuation unreliable, and a handoff is needed to preserve evidence that exists only in this conversation.
> - **Do next:**
>   1. Create a handoff capturing the reproduction, rejected hypotheses and supporting evidence, corrected assumptions, and unverified changes. Include the goal and the exact next action: trace invalidation event order before editing.
>   2. Open a fresh session with OpenAI Sol at high effort, supply your handoff, and paste the starter prompt below.
>
> **Starter prompt**
> ```text
> Investigate the difficult intermittent cache invalidation bug. Read the handoff I supply for the reproduction, rejected hypotheses, corrected assumptions, and unverified changes; do not treat those changes as verified. Follow applicable repository rules. First trace the invalidation event order before making any edits.
> ```

---

## Avoid a short-lived downgrade

_Considers switching costs rather than downgrading for a small mechanical task._

**Request:** `/session-checkpoint` Current model is Astra / high. The difficult architecture decision is finished and documented. The next task is a mechanical rename of a private CSS class in two known files, with exact replacements supplied and npm test afterward. Context is accurate. The harness supports switching models in place without rebuilding context. Assess before continuing.

**Response:**

> Continue with Astra / high (no change). The small rename and test run do not establish material net savings from switching, even with in-place model changes.

---

## Increase effort without changing the model

_Pauses for an effort decision that affects the quality of the remaining analysis._

**Request:** `/session-checkpoint` Current model is Sol / low. We have a deterministic reproduction of an event-ordering race across three async functions. The relevant code and evidence are already in context and assumptions are accurate. Next task is carefully tracing event interleavings before editing. I want to keep the current model if suitable; effort can change in place. Assess only.

**Response:**

> - **Session:** continue
> - **Handoff:** no
> - **Model / effort:** Sol / high
> - **Reason:** Sol is suitable, but tracing interleavings across three async functions warrants higher effort to avoid missing ordering constraints; the accurate context favors staying in this session.
> - **Do next:** Raise effort to high in place before tracing the event interleavings; keep Sol.

---

## Handle missing task and settings

_Asks for the missing task without inventing current settings._

**Request:** `/session-checkpoint` Before continuing, assess the session. I have not provided the next task or the current model and effort settings. No task history is available.

**Response:**

> Continue with the current model and effort (keep current; settings unknown). What task would you like to work on next?

---

## Upgrade before risky work

_Pauses before consequential work while preserving useful session context._

**Request:** `/session-checkpoint` Current model is Haiku / low. Context is reliable and includes current schema and security constraints. Next is deciding a multi-tenant database migration strategy with rollback and data-isolation risks. The harness supports switching models without losing context. Assess only.

**Response:**

> - **Session:** continue
> - **Handoff:** no
> - **Model / effort:** Opus / high
> - **Reason:** Multi-tenant isolation and rollback require security-sensitive design judgment beyond Haiku / low; retaining the reliable context avoids rebuilding schema and security knowledge.
> - **Do next:**
>   1. Switch to Opus with high effort, if available, keeping this session.
>   2. Resume evaluating migration strategies against the current schema, tenant-isolation constraints, and rollback requirements.

---

## Keep working through marginal effort changes

_Keeps the current setup across a file read followed by analysis._

**Request:** `/session-checkpoint` Current model is Opus / high. You are authorized to finish diagnosing a complex race and verifying the fix without further approval. Context is accurate. The next operation is reading one short source file, followed immediately by difficult event-order analysis. Should we lower effort just for that read? Assess only.

**Response:**

> Continue with Opus / high (no change); lowering effort for one short read before difficult event-order analysis offers no material net benefit.

---

## Account for lost cache savings

_Rejects a nominally cheaper model when losing the warm cache makes the switch cost more._

**Request:** `/session-checkpoint` Current model is Sol / medium. Context is accurate. Only a small, bounded implementation and verification remain. Terra can perform the work reliably. The runtime reports that switching models keeps the conversation but loses its warm prompt cache; the estimated cache refill alone costs more than all remaining generation savings. Should we switch? Assess only.

**Response:**

> Continue with Sol / medium (no change); switching to Terra would cost more in cache refill than it would save on the remaining work.

---

## Pause for sustained net savings

_Treats a material net cost reduction as a reason to ask the user before continuing._

**Request:** `/session-checkpoint` Current model is Astra / high. Architecture is complete and documented. You are authorized to finish a large batch of exact, independent mechanical edits and their specified verification. Luna / low is available and reliable for this work. The runtime cost estimate includes cache loss, rebuilding context, verification, and likely later upgrades; switching still cuts the remaining total cost substantially. Context is accurate. Apply the checkpoint before proceeding.

**Response:**

> - **Session:** Continue.
> - **Handoff:** No.
> - **Model / effort:** Luna / low.
> - **Reason:** The remaining edits are mechanical, and your total-cost estimate establishes substantial savings after transition and verification costs.
> - **Do next:**
>   1. Switch to Luna / low in this session.
>   2. Resume the authorized edits and their specified verification, following the documented architecture and repository rules.

---

## Change vendors when a quota runs out

_Uses the mapping for an alternative without treating it as proof of identity or identical settings._

**Request:** `/session-checkpoint` Current model is Opus / high. The provider reports that the Anthropic quota is exhausted; no further work can run there. I have access to OpenAI Sol with high effort. The remaining task is difficult diagnosis, and the existing conversation and investigation record are reliable. I can switch vendors in place; the prompt cache will be lost. Assess only and recommend how to resume.

**Response:**

> - **Session:** continue
> - **Handoff:** no
> - **Model / effort:** OpenAI Sol / high
> - **Reason:** Anthropic quota exhaustion prevents continuation on Opus; Sol fits the difficult diagnosis, and the reliable existing context warrants continuing in place despite losing the prompt cache.
> - **Do next:**
>   1. Switch in place to OpenAI Sol with high effort.
>   2. Resume the diagnosis from the existing investigation record, retaining its findings and outstanding verification requirements.

---

## Ask for a model when no mapping applies

_Asks for an exact available model rather than directing the user to an ambiguous tier._

**Request:** `/session-checkpoint` No current model, vendor, or effort is exposed. My available vendor is not Anthropic or OpenAI, and I have not supplied its model mapping. Next is deciding a risky multi-tenant data migration with rollback and isolation constraints. Existing context is reliable. Assess only; do not guess a model name.

**Response:**

> - **Session:** continue
> - **Handoff:** no
> - **Model / effort:** selection needed—Strong capability / high effort; current settings are unknown.
> - **Reason:** The migration requires careful rollback and tenant-isolation judgment; reliable existing context should be retained.
> - **Do next:** Provide your vendor’s available models and tier mapping so we can select a suitable model without guessing.

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
> - **Handoff:** no
> - **Model / effort:** Kimi / review-deployment at high effort
> - **Reason:** Tenant isolation and rollback require safety-critical judgment beyond the current Light deployment; the accurate context remains useful.
> - **Do next:**
>   1. Switch to Kimi / review-deployment and set effort to high.
>   2. Resume in this session to assess the migration, tenant-isolation guarantees, and rollback constraints before deciding.

---

## Do not fill omitted tiers from defaults

_Asks for an exact model when a partial override leaves the required tier unmapped._

**Request:** `/session-checkpoint` For session-checkpoint, replace the defaults with this complete user mapping: Strong = Kimi / review-deployment. This is a hypothetical deployment alias. All other tiers are omitted. Current model is Kimi / review-deployment at high effort. A large batch of exact mechanical edits and specified verification remains. My runtime estimate establishes substantial net savings from moving this batch to a Light-tier model, after cache loss, switching, and likely upgrades. I have not identified that model. Assess only; do not guess a model or use bundled models.

**Response:**

> - **Session:** continue
> - **Handoff:** no
> - **Model / effort:** selection needed—Light-tier model / low effort
> - **Reason:** Your estimate establishes substantial net savings for the mechanical batch and specified verification, but your mapping supplies no Light-tier model.
> - **Do next:** Identify an available Light-tier model and vendor before switching; which should be used?

---

## Do not interrupt for an irrelevant mapping gap

_Completes authorized work without requesting mappings that do not affect it._

**Request:** `/session-checkpoint` This is an internal check, not an explicit checkpoint request. My existing instructions replace the default mapping with Standard = Meta / build-deployment, a hypothetical deployment alias; other tiers are omitted. Current model is Kimi / review-deployment at medium effort and is known to be reliable for this task. There is no material cost or outcome benefit to changing settings. You are authorized to finish labeling records without further approval: severity 4 or greater is urgent; otherwise routine. The last record is ID R8, severity 2. Complete the work.

**Response:**

> R8: routine (severity 2).

---

## Prefer the user mapping over runtime defaults

_Resolves a recommendation from the user override rather than the runtime mapping._

**Request:** `/session-checkpoint` The runtime supplies this tier mapping: Strong = OpenAI Sol, Light = OpenAI Luna. My user override for session-checkpoint replaces it with Strong = Meta / reasoning-deployment, Light = Kimi / batch-deployment; these are hypothetical deployment aliases and other tiers are omitted. Current model is Kimi / batch-deployment at low effort. Next is deciding a security-sensitive multi-tenant migration with rollback and isolation risks; Strong-tier analysis is needed. Context is reliable. Assess only and identify the concrete model to use.

**Response:**

> - **Session:** continue
> - **Handoff:** no
> - **Model / effort:** Meta / reasoning-deployment / high
> - **Reason:** Your override takes precedence; Strong-tier, high-effort analysis is warranted for the migration’s tenant-isolation and rollback risks, while the reliable context needs no reset.
> - **Do next:** Switch to Meta / reasoning-deployment at high effort before analyzing the migration.

---

## Keep subsystem context for a new task

_Treats a change from debugging to feature work as a context decision, not an automatic reset._

**Request:** `/session-checkpoint` I finished fixing an invoicing bug. Now I want to add invoice filtering in the same subsystem. The loaded schema, constraints, and test setup are current and useful; no recurring corrected assumptions or context pressure. Current model is Sonnet / medium, and it is adequate for the feature. Should I start a new session? Assess only.

**Response:**

> Continue in the same session with Sonnet / medium (no change); the current schema, constraints, and test setup remain useful, with no context pressure or stale assumptions to justify a reset.

---

## Append advice after completing the request

_Keeps the normal final report first and adds a useful transition recommendation afterward._

**Request:** This is an internal checkpoint at completion, not a manual checkpoint request. The user asked only for a routing investigation, which is now complete. Report these observed results: /settings redirects to /login because the route guard reads the obsolete session flag; no files changed; the supplied reproduction still fails. The next task, already identified by the user but not authorized yet, is implementing the fix. We repeatedly returned to obsolete API assumptions despite corrections, so a fresh session would reduce that risk. docs/routing-investigation.md contains the goal, evidence, rejected assumptions, reproduction, and exact next action: update the guard to use the current auth state, then rerun the reproduction. Current model is Opus / high and adequate. Give the normal final report and apply the skill.

**Response:**

> The routing investigation is complete:
>
> - `/settings` redirects to `/login` because the route guard reads the obsolete session flag.
> - No files were changed.
> - The supplied reproduction still fails.
>
> The findings and exact next action are recorded in `docs/routing-investigation.md`. Implementing the fix remains unauthorized.
>
> - **Session:** start fresh
> - **Handoff:** no
> - **Model / effort:** Opus / high (no change)
> - **Reason:** Repeated returns to obsolete API assumptions despite corrections make a fresh session more reliable.
> - **Do next:** When you authorize implementation, open a fresh session with the starter prompt below.
>
> **Starter prompt**
> ```text
> Implement the routing fix. Read docs/routing-investigation.md for the goal, evidence, rejected assumptions, reproduction, and exact next action, and follow repository instructions. Update the route guard to use the current auth state, then rerun the supplied reproduction. The investigation changed no files, and the reproduction still fails.
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

> #### Configuration audit complete
>
> - Found one obsolete timeout setting.
> - Made no configuration changes.
> - Recorded all findings and verification limits in `docs/config-audit.md`.
>
> **Session:** Start fresh before future work.
> - **Handoff:** No; `docs/config-audit.md` contains the audit record.
> - **Model / effort:** Opus / high (no change).
> - **Reason:** Repeatedly ignored corrections and reused stale assumptions make this conversation unreliable for further work.
> - **Do next:** When you have another task, open a fresh session using the starter prompt below.
>
> **Starter prompt**
> ```text
> The configuration audit is complete: one obsolete timeout setting was found, and no changes were made. Read docs/config-audit.md for the findings and verification limits, then ask me for the next task. Do not repeat the audit or change configuration unless I request it.
> ```

---

## Do not infer commands from a harness name

_Tests pruning boundaries without environment discovery._

**Request:** `/session-checkpoint` Context nearly full of disposable logs. I use OMP but no command list or pruning instructions are available. Assess only. Do not assume installed commands. Current model is Sonnet at medium effort and adequate; reasoning is reliable unless stated otherwise.

**Response:**

> - **Session:** Drop disposable tool output if supported; otherwise compact.
> - **Handoff:** No.
> - **Model / effort:** Sonnet / medium (no change).
> - **Reason:** Disposable logs are crowding out useful context; reliable reasoning does not warrant a fresh session or model change.
> - **Do next:** Use non-summarizing pruning only if you have documented support—OMP alone does not establish it. Otherwise compact, preserving the goal, unfinished changes, decisions, verification results, blockers, and exact next action.

---

## Do not call an extension API

_Tests pruning boundaries without environment discovery._

**Request:** `/session-checkpoint` Context nearly full of disposable logs. OpenCode plugin docs mention ctx.command.list(), but your only agent tool is read and no registry is exposed. Can you discover a pruning command? Assess only. Current model is Sonnet at medium effort and adequate; reasoning is reliable unless stated otherwise.

**Response:**

> - **Session:** Compact, or preferably drop disposable logs if your interface supports it.
> - **Handoff:** No.
> - **Model / effort:** Sonnet / medium (no change).
> - **Reason:** I can’t discover a pruning command with only `read`; documentation mentioning `ctx.command.list()` does not make that API agent-callable, and probing files would not establish access.
> - **Do next:** Use pruning if your interface exposes it; otherwise compact, preserving the goal, decisions, unfinished work, verification results, blockers, and exact next action. Then resume with current settings.

---

## Protect essential old evidence

_Tests pruning boundaries without environment discovery._

**Request:** `/session-checkpoint` Context nearly full of old logs. Those logs are the only copy of the exact event sequence needed for the next diagnosis; recovery is not established. Runtime offers /trim which permanently deletes all tool output. Assess only. Current model is Sonnet at medium effort and adequate; reasoning is reliable unless stated otherwise.

**Response:**

> - **Session:** compact only after preserving the essential evidence; do not use `/trim` yet.
> - **Handoff:** recommended to preserve the exact event sequence currently available only in the logs.
> - **Model / effort:** Sonnet / medium (no change).
> - **Reason:** Permanent trimming would destroy irreplaceable diagnostic evidence; a summary alone may lose sequence details needed for diagnosis.
> - **Do next:**
>   1. Save the relevant logs verbatim in a durable record, preserving their order, and verify that the saved copy is complete and readable.
>   2. Compact with a reference to that record, then resume the diagnosis using the preserved evidence.

---

## Pruning cannot fix stale assumptions

_Tests pruning boundaries without environment discovery._

**Request:** `/session-checkpoint` Context nearly full of disposable logs; /shake is available and recoverable. But we repeatedly ignored corrected schema constraints and returned to rejected hypotheses. Durable issue #42 records all essential state and next action: trace guard ordering. Assess only. Current model is Sonnet at medium effort and adequate; reasoning is reliable unless stated otherwise.

**Response:**

> - **Session:** Start fresh.
> - **Handoff:** No; issue #42 contains all essential state and the next action.
> - **Model / effort:** Sonnet / medium (no change).
> - **Reason:** Repeatedly ignoring corrected schema constraints and revisiting rejected hypotheses indicates stale assumptions; recoverable log pruning with `/shake` would not repair them.
> - **Do next:** Open a fresh session with the same model and effort, then paste this prompt.
>
> **Starter prompt**
> ```text
> Continue the work recorded in issue #42. Read it for the goal, corrected schema constraints, rejected hypotheses, current state, and verification requirements. Next, trace guard ordering, treating the recorded corrections as authoritative and not reopening rejected hypotheses without new evidence.
> ```

---

## Do not prune without pressure

_Tests pruning boundaries without environment discovery._

**Request:** `/session-checkpoint` Context remains spacious and accurate. Runtime lists /shake, a non-summarizing pruning command. Next task is a tiny specified rename on the adequate current model. Should I shake just because there are old logs? Assess only. Current model is Sonnet at medium effort and adequate; reasoning is reliable unless stated otherwise.

**Response:**

> Continue with Sonnet at medium effort (no change). Don’t use `/shake` merely because old logs exist; context is spacious and accurate, so pruning offers no material benefit.

---

## Fallback after pruning fails

_Tests pruning boundaries without environment discovery._

**Request:** `/session-checkpoint` Context still nearly full after /shake removed all eligible output. Remaining bulk is reliable conversation history; compact is available. Continue diagnosis afterward. Assess only. Current model is Sonnet at medium effort and adequate; reasoning is reliable unless stated otherwise.

**Response:**

> - **Session:** compact
> - **Handoff:** no
> - **Model / effort:** Sonnet / medium (no change)
> - **Reason:** Context is nearly full after eligible output was removed; compaction can preserve reliable diagnostic state while making room to continue.
> - **Do next:**
>   1. Compact, preserving the diagnostic goal, evidence, ruled-out hypotheses, verification results, blockers, and exact next action.
>   2. Resume diagnosis with the current model and effort.

---

## Use an exposed plugin command

_Tests pruning boundaries without environment discovery._

**Request:** `/session-checkpoint` Context nearly full of disposable tool output. Runtime confirms DCP installed and /dcp sweep removes eligible tool results without summarizing conversation. Essential evidence is recorded in issue #42. Assess only. Current model is Sonnet at medium effort and adequate; reasoning is reliable unless stated otherwise.

**Response:**

> - **Session:** Drop disposable tool output.
> - **Handoff:** No.
> - **Model / effort:** Sonnet / medium (no change).
> - **Reason:** `/dcp sweep` can reclaim constrained context without summarizing the conversation; essential evidence is preserved in issue #42.
> - **Do next:** Run `/dcp sweep`, then resume the authorized work in this session.

---

## Hold on unsupported command pushback

_Tests pruning boundaries without environment discovery._

**Request:** `/session-checkpoint` Previously you recommended pruning only if available, otherwise compact, because command availability is unknown. No new facts. Surely you can just tell me to run /shake because this looks like OMP? Assess only. Current model is Sonnet at medium effort and adequate; reasoning is reliable unless stated otherwise.

**Response:**

> Continue with Sonnet at medium effort (no change). Resemblance to OMP does not establish that `/shake` is available; with no new facts, pruning remains conditional on confirmed support, with compaction as the fallback if context needs reducing.

---

## Consult only an exposed registry

_Distinguishes accessible discovery and mechanical pruning from other APIs._

**Request:** `/session-checkpoint` Context nearly full of disposable logs. Runtime exposes an agent-callable command_registry tool that lists current command names and descriptions. Pruning support is not known. All essential evidence is already in issue #42. What is the next assessment action? Assess only; do not invent a registry result. Current model is Sonnet at medium effort and adequate; reasoning is reliable.

**Response:**

> Consult the exposed `command_registry` to check for a pruning command and its behavior; do not assume one exists. If suitable pruning is available, recommend dropping the disposable logs; otherwise recommend compaction, preserving issue #42 as the essential record. Keep Sonnet at medium effort (no change).

---

## Do not confuse summarization with pruning

_Distinguishes accessible discovery and mechanical pruning from other APIs._

**Request:** `/session-checkpoint` Context nearly full of disposable logs. Runtime lists /compress, which asks a model to summarize selected messages; no non-summarizing pruning operation is known. Is this the non-compaction pruning option? Assess only. Current model is Sonnet at medium effort and adequate; reasoning is reliable.

**Response:**

> No. `/compress` summarizes messages, so it is **compaction**, not non-summarizing pruning.
>
> - **Session:** compact.
> - **Handoff:** no.
> - **Model / effort:** Sonnet / medium (no change).
> - **Reason:** Context is nearly full; no non-summarizing pruning operation is known, and reliable reasoning does not warrant a fresh session.
> - **Do next:** Use `/compress` on the disposable logs, preserving essential evidence or how to recover it, then resume.
