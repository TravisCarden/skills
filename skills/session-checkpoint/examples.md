# session-checkpoint examples

Real, unedited responses from the final [`session-checkpoint`](SKILL.md) skill, generated on 2026-11-08. Each request used a fresh, stateless call with the skill body and bundled [default model mapping](model-mapping.md) as system context and no tools. The harness selected its default model; the concrete model ID was not exposed.

The scenarios are hypothetical. Technical claims in the responses are unverified. These examples show decisions and output behavior, not measured cost savings, live tool execution, or proof of better reasoning. Custom deployment aliases in the override cases are hypothetical user-supplied names, not claims about public models. Internal-check scenarios supply their complete tasks in the requests. Requests without a slash command simulate model invocation after the skill has loaded; they do not test automatic discovery.

This file is for people. `SKILL.md` does not reference it, so agents do not load it.

## Contents

- [Continue with useful context](#continue-with-useful-context)
- [Compact without resetting](#compact-without-resetting)
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
> - **Reason:** Context is constrained, but consistent reasoning makes a concise summary sufficient to preserve reliable progress.
> - **Do next:**
>   1. Compact, preserving the exact failing build command and error, config changes and their rationale, and pending verification.
>   2. Resume the same Vite debugging task, starting with the pending verification; keep unverified changes explicitly marked.

---

## Start fresh using existing records

_Preserves unfinished changes and a failing reproduction in a paste-ready prompt._

**Request:** `/session-checkpoint` We repeatedly used obsolete React Router API assumptions despite corrections. Should we start fresh? Existing docs/routing-investigation.md records the goal, decisions, and reproduction command. src/routes.tsx has an unverified edit. The latest reproduction still redirects /settings to /login after a successful sign-in. No other blocker. Next trace route guards before editing.

**Response:**

> - **Session:** start fresh
> - **Handoff:** no
> - **Model / effort:** keep current / keep current
> - **Reason:** Repeated obsolete API assumptions despite corrections make continuation less reliable; the existing investigation record and a short prompt preserve the essential state.
> - **Do next:** Open a fresh session with the prompt below.
>
> **Starter prompt**
> ```text
> Investigate why /settings still redirects to /login after a successful sign-in. Read docs/routing-investigation.md for the goal, decisions, and reproduction command, and follow repository instructions. src/routes.tsx contains an unverified edit; the latest reproduction still fails, and there is no other blocker. Prior attempts repeatedly relied on obsolete React Router APIs despite corrections, so establish the installed version and use its actual APIs. Next, trace the route guards before making any further edits.
> ```

---

## Ask for the deciding fact

_Asks for the next task instead of guessing whether a downgrade is worthwhile._

**Request:** `/session-checkpoint` Should we switch to a cheaper model? I have not said what the next task is. Current model is Sol at medium effort. There is no evidence of context trouble.

**Response:**

> Continue with Sol at medium effort (no change). What is the next task?

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

> - **Session:** start fresh
> - **Handoff:** recommended
> - **Model / effort:** OpenAI Sol / high
> - **Reason:** Recurring corrected assumptions make continuation unreliable, and a handoff is needed to preserve the investigation state that exists only in this conversation.
> - **Do next:**
>   1. Use your own command to create a handoff capturing the reproduction, rejected hypotheses and corrected assumptions, unverified changes, and any verification results or blockers. Mark unverified work explicitly.
>   2. Open a fresh session using OpenAI Sol with high effort, supply your handoff, and paste the starter prompt below.
>
> **Starter prompt**
> ```text
> Investigate the difficult intermittent cache invalidation bug. Read the handoff I supply and the applicable repository instructions first. Preserve the corrected assumptions and rejected hypotheses, and treat unverified changes as unverified. Next, trace the invalidation event order before editing; use the trace to identify where the observed sequence diverges from the expected sequence.
> ```

---

## Avoid a short-lived downgrade

_Considers switching costs rather than downgrading for a small mechanical task._

**Request:** `/session-checkpoint` Current model is Astra / high. The difficult architecture decision is finished and documented. The next task is a mechanical rename of a private CSS class in two known files, with exact replacements supplied and npm test afterward. Context is accurate. The harness supports switching models in place without rebuilding context. Assess before continuing.

**Response:**

> Continue with Astra / high (no change). The remaining task is small and context is accurate; switching offers no established material net saving.

---

## Increase effort without changing the model

_Pauses for an effort decision that affects the quality of the remaining analysis._

**Request:** `/session-checkpoint` Current model is Sol / low. We have a deterministic reproduction of an event-ordering race across three async functions. The relevant code and evidence are already in context and assumptions are accurate. Next task is carefully tracing event interleavings before editing. I want to keep the current model if suitable; effort can change in place. Assess only.

**Response:**

> - **Session:** continue
> - **Handoff:** no
> - **Model / effort:** Sol / high
> - **Reason:** Sol is suitable, but carefully tracing interleavings across three async functions warrants higher effort to avoid missing ordering constraints; the existing context remains useful.
> - **Do next:** Raise effort to high in place, then trace the event interleavings before editing.

---

## Handle missing task and settings

_Asks for the missing task without inventing current settings._

**Request:** `/session-checkpoint` Before continuing, assess the session. I have not provided the next task or the current model and effort settings. No task history is available.

**Response:**

> Continue with the current model and effort—keep current settings. What task would you like to work on next?

---

## Upgrade before risky work

_Pauses before consequential work while preserving useful session context._

**Request:** `/session-checkpoint` Current model is Haiku / low. Context is reliable and includes current schema and security constraints. Next is deciding a multi-tenant database migration strategy with rollback and data-isolation risks. The harness supports switching models without losing context. Assess only.

**Response:**

> - **Session:** continue
> - **Handoff:** no
> - **Model / effort:** Opus / high
> - **Reason:** Multi-tenant migration design involves consequential rollback and data-isolation tradeoffs that warrant stronger reasoning than Haiku / low; the reliable context can be retained.
> - **Do next:**
>   1. Switch to Opus with high effort in this session.
>   2. Resume assessing migration strategies against the current schema, security constraints, rollback requirements, and tenant-isolation risks.

---

## Keep working through marginal effort changes

_Keeps the current setup across a file read followed by analysis._

**Request:** `/session-checkpoint` Current model is Opus / high. You are authorized to finish diagnosing a complex race and verifying the fix without further approval. Context is accurate. The next operation is reading one short source file, followed immediately by difficult event-order analysis. Should we lower effort just for that read? Assess only.

**Response:**

> Continue with Opus / high (no change). Lowering effort for one short read before difficult event-order analysis offers no material benefit.

---

## Account for lost cache savings

_Rejects a nominally cheaper model when losing the warm cache makes the switch cost more._

**Request:** `/session-checkpoint` Current model is Sol / medium. Context is accurate. Only a small, bounded implementation and verification remain. Terra can perform the work reliably. The runtime reports that switching models keeps the conversation but loses its warm prompt cache; the estimated cache refill alone costs more than all remaining generation savings. Should we switch? Assess only.

**Response:**

> Continue with Sol / medium (no change); switching to Terra would cost more in cache refill alone than the remaining generation savings.

---

## Pause for sustained net savings

_Treats a material net cost reduction as a reason to ask the user before continuing._

**Request:** `/session-checkpoint` Current model is Astra / high. Architecture is complete and documented. You are authorized to finish a large batch of exact, independent mechanical edits and their specified verification. Luna / low is available and reliable for this work. The runtime cost estimate includes cache loss, rebuilding context, verification, and likely later upgrades; switching still cuts the remaining total cost substantially. Context is accurate. Apply the checkpoint before proceeding.

**Response:**

> - **Session:** continue
> - **Handoff:** no
> - **Model / effort:** Luna / low
> - **Reason:** Your total-cost estimate establishes substantial savings for the remaining mechanical edits and verification, with no need to rebuild the session.
> - **Do next:**
>   1. Switch to Luna / low in this session.
>   2. Resume the authorized edits and their specified verification using the documented architecture.

---

## Change vendors when a quota runs out

_Uses the mapping for an alternative without treating it as proof of identity or identical settings._

**Request:** `/session-checkpoint` Current model is Opus / high. The provider reports that the Anthropic quota is exhausted; no further work can run there. I have access to OpenAI Sol with high effort. The remaining task is difficult diagnosis, and the existing conversation and investigation record are reliable. I can switch vendors in place; the prompt cache will be lost. Assess only and recommend how to resume.

**Response:**

> - **Session:** continue
> - **Handoff:** no
> - **Model / effort:** OpenAI Sol / high
> - **Reason:** Anthropic’s exhausted quota prevents continuation on Opus; Sol fits the difficult diagnosis, and the reliable existing context warrants continuing in place despite losing the prompt cache.
> - **Do next:**
>   1. Switch in place to OpenAI Sol with high effort.
>   2. Resume the diagnosis from the existing conversation and investigation record.

---

## Ask for a model when no mapping applies

_Asks for an exact available model rather than directing the user to an ambiguous tier._

**Request:** `/session-checkpoint` No current model, vendor, or effort is exposed. My available vendor is not Anthropic or OpenAI, and I have not supplied its model mapping. Next is deciding a risky multi-tenant data migration with rollback and isolation constraints. Existing context is reliable. Assess only; do not guess a model name.

**Response:**

> - **Session:** continue
> - **Handoff:** no
> - **Model / effort:** selection needed for Strong capability / high effort; current model, vendor, and effort are unknown
> - **Reason:** The migration requires safety-critical judgment about rollback and tenant isolation; reliable existing context should be retained.
> - **Do next:** Tell me which models your vendor offers, or which available model you map to Strong, so I can recommend a concrete model without guessing.

---

## Keep internal checks silent

_Continues authorized work without emitting another checkpoint or asking for approval._

**Request:** `/session-checkpoint` You are using these rules for an internal check, not responding to an explicit checkpoint request. Current model is Haiku / low. The user authorized you to finish labeling the supplied records without further approval. Context is accurate and no transition or material cost decision is needed. One record remains. Apply the existing rule: label a record urgent if its severity is 4 or greater; otherwise label it routine. Record: ID R7, severity 5. Complete the authorized work now.

**Response:**

> R7: urgent.

---

## Use models from other vendors

_Uses exact user-supplied deployment aliases rather than the bundled vendors._

**Request:** `/session-checkpoint` My already-loaded agent instructions say: For session-checkpoint, replace the default mapping with Strongest: unavailable; Strong: Kimi / review-deployment; Standard: Meta / build-deployment; Light: SpaceX / batch-deployment. These are hypothetical user-defined deployment aliases, not public model names. Current model is SpaceX / batch-deployment at low effort. Next is deciding a risky multi-tenant migration with rollback and tenant-isolation constraints. Context is accurate. Assess only before that work.

**Response:**

> - **Session:** continue
> - **Handoff:** no
> - **Model / effort:** Kimi / review-deployment at high effort
> - **Reason:** The migration requires safety-critical judgment about rollback and tenant isolation; the current SpaceX / batch-deployment at low effort is not an adequate fit, while the accurate context remains useful.
> - **Do next:**
>   1. Switch to Kimi / review-deployment at high effort in this session.
>   2. Then begin the migration decision, including rollback viability and tenant-isolation failure modes.

---

## Do not fill omitted tiers from defaults

_Asks for an exact model when a partial override leaves the required tier unmapped._

**Request:** `/session-checkpoint` For session-checkpoint, replace the defaults with this complete user mapping: Strong = Kimi / review-deployment. This is a hypothetical deployment alias. All other tiers are omitted. Current model is Kimi / review-deployment at high effort. A large batch of exact mechanical edits and specified verification remains. My runtime estimate establishes substantial net savings from moving this batch to a Light-tier model, after cache loss, switching, and likely upgrades. I have not identified that model. Assess only; do not guess a model or use bundled models.

**Response:**

> - **Session:** continue
> - **Handoff:** no
> - **Model / effort:** selection needed for Light tier / low effort
> - **Reason:** Your estimate establishes substantial net savings for the mechanical batch, but your mapping supplies no Light-tier model.
> - **Do next:** Which available model and vendor should you use for the Light-tier work?

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

> - **Session:** continue
> - **Handoff:** no
> - **Model / effort:** Meta / reasoning-deployment / high
> - **Reason:** Your override replaces the runtime mapping; Strong-tier analysis is needed to assess the migration’s security, rollback, and tenant-isolation risks reliably.
> - **Do next:** Switch to Meta / reasoning-deployment at high effort, then resume the migration decision in this session.

---

## Keep subsystem context for a new task

_Treats a change from debugging to feature work as a context decision, not an automatic reset._

**Request:** `/session-checkpoint` I finished fixing an invoicing bug. Now I want to add invoice filtering in the same subsystem. The loaded schema, constraints, and test setup are current and useful; no recurring corrected assumptions or context pressure. Current model is Sonnet / medium, and it is adequate for the feature. Should I start a new session? Assess only.

**Response:**

> Continue in the same session with Sonnet / medium (no change); the current context is useful, with no reliability or context-pressure reason to reset.

---

## Append advice after completing the request

_Keeps the normal final report first and adds a useful transition recommendation afterward._

**Request:** This is an internal checkpoint at completion, not a manual checkpoint request. The user asked only for a routing investigation, which is now complete. Report these observed results: /settings redirects to /login because the route guard reads the obsolete session flag; no files changed; the supplied reproduction still fails. The next task, already identified by the user but not authorized yet, is implementing the fix. We repeatedly returned to obsolete API assumptions despite corrections, so a fresh session would reduce that risk. docs/routing-investigation.md contains the goal, evidence, rejected assumptions, reproduction, and exact next action: update the guard to use the current auth state, then rerun the reproduction. Current model is Opus / high and adequate. Give the normal final report and apply the skill.

**Response:**

> Investigation complete: `/settings` redirects to `/login` because the route guard reads the obsolete session flag.
>
> - No files changed.
> - The supplied reproduction still fails.
> - Findings and the exact next action are recorded in `docs/routing-investigation.md`. Implementing the fix remains unauthorized.
>
> - **Session:** start fresh
> - **Handoff:** no
> - **Model / effort:** Opus / high (no change)
> - **Reason:** We repeatedly returned to obsolete API assumptions despite corrections; a fresh session would reduce that risk.
> - **Do next:**
>   1. Open a fresh session with the same model and effort.
>   2. When ready to authorize implementation, paste the starter prompt below.
>
> **Starter prompt**
> ```text
> Implement the routing fix. Read docs/routing-investigation.md for the goal, evidence, rejected assumptions, and reproduction. Update the route guard to use the current auth state instead of the obsolete session flag, then rerun the supplied reproduction and report the result. The investigation changed no files, and the reproduction still failed at its conclusion.
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

> The configuration audit is complete. It found one obsolete timeout setting; no changes were made. All findings and verification limits are recorded in `docs/config-audit.md`.
>
> - **Session:** start fresh
> - **Handoff:** no
> - **Model / effort:** Opus / high (no change)
> - **Reason:** Repeatedly ignored corrections and reused stale assumptions make this conversation unreliable for further work.
> - **Do next:** When you have another task, open a fresh session with the prompt below and add your request.
>
> **Starter prompt**
> ```text
> The configuration audit is complete, and no configuration changes were made. Read docs/config-audit.md for the findings and verification limits before addressing my next request. Treat the recorded limits as unresolved, not as passed verification. Wait for my task before taking further action.
> ```
