# session-checkpoint examples

Real, unedited responses from the final [`session-checkpoint`](SKILL.md) skill, generated on 2026-11-09. Each request used a fresh, stateless call with the skill body and bundled [default model mapping](model-mapping.md) as system context and no tools. The harness selected its default model; the concrete model ID was not exposed.

The scenarios are hypothetical. Technical claims in the responses are unverified. These examples show decisions and output behavior, not measured cost savings, live tool execution, or proof of better reasoning. Custom deployment aliases in the override cases are hypothetical user-supplied names, not claims about public models. Internal-check scenarios supply their complete tasks in the requests. Requests without a slash command simulate model invocation after the skill has loaded; they do not test automatic discovery.

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

---

## Continue with useful context

_Agrees with a sound plan without adding transition overhead._

**Request:** `/session-checkpoint` Before continuing: current context is accurate and useful, the focused unit test passed, and the next action is running npm test for the web app. I think we should stay. Current model is Terra at medium effort.

**Response:**

> Agreed: continue with the current model and effort (Terra, medium). The context is accurate, the next step is a bounded command run with a clear pass/fail result, and nothing here justifies a compaction or a model change.
>
> Running `npm test` for the web app now.
>
> I can't run it from here, so please run `npm test` in the web app and paste the output. Once I have the results, I'll pick up from there.

---

## Compact without resetting

_Preserves reliable state when the harness reports context pressure._

**Request:** `/session-checkpoint` The harness reports little context remains. Reasoning has been consistent while debugging a Vite build. We need a concise summary preserving the failing build command, config changes, and pending verification, then continue the same task. Current model is Sonnet at medium effort. Assess only.

**Response:**

> - **Session:** compact. Reasoning has been consistent, so there's no reason for a fresh start. If bulky build logs are what's filling the context, drop that stale tool output instead, if your harness offers it.
> - **Handoff:** no
> - **Model / effort:** Sonnet / medium (no change)
> - **Reason:** Context space is the only constraint, and a short summary can carry the failing build command, config changes, and pending verification without a new session.
> - **Do next:** Compact now, keeping those three items plus the exact next action, then tell me to resume the Vite build debugging.

---

## Offer dropping tool output when availability is unknown

_Names dropping tool output as a conditional first choice, with compact as the fallback._

**Request:** `/session-checkpoint` Context is nearly full, almost all of it large test logs and file dumps from earlier tool calls. The conversation and reasoning have been consistent and are still useful. Next I will continue fixing the failing checkout test. Current model is Sonnet at medium effort. Assess only.

**Response:**

> - **Session:** drop tool output if your harness offers it (compact as the fallback)
> - **Handoff:** no
> - **Model / effort:** Sonnet / medium (no change)
> - **Reason:** The conversation is consistent and still useful, and the bulk is stale test logs and file dumps, so clearing those frees space without losing reasoning, while a fresh session would throw away working context for no gain.
> - **Do next:** Clear the old tool results, then keep fixing the failing checkout test. If your harness can't drop tool output, compact instead.

---

## Use a drop command the runtime exposes

_Recommends the harness's own command when runtime context lists it._

**Request:** `/session-checkpoint` Context is nearly full, almost all of it large test logs and file dumps from earlier tool calls. The conversation and reasoning have been consistent and are still useful. Runtime context lists these user commands: `/shake` (drops stale tool output from context) and `/compact` (summarizes the conversation). Next I will continue fixing the failing checkout test. Current model is Sonnet at medium effort. Assess only.

**Response:**

> - **Session:** drop stale tool output with `/shake`, then continue in this session
> - **Handoff:** no
> - **Model / effort:** Sonnet / medium (no change)
> - **Reason:** Large test logs and file dumps fill the context while the conversation and reasoning are still reliable, so removing the bulky output frees space without summarizing away useful state.
> - **Do next:** Run `/shake`, then resume fixing the failing checkout test. If space is still tight afterward, fall back to `/compact`.

---

## Start fresh using existing records

_Preserves unfinished changes and a failing reproduction in a paste-ready prompt._

**Request:** `/session-checkpoint` We repeatedly used obsolete React Router API assumptions despite corrections. Should we start fresh? Existing docs/routing-investigation.md records the goal, decisions, and reproduction command. src/routes.tsx has an unverified edit. The latest reproduction still redirects /settings to /login after a successful sign-in. No other blocker. Next trace route guards before editing.

**Response:**

> - **Session:** start fresh. Repeated obsolete-API assumptions that survived corrections are the warning sign, and the existing record already carries the state, so don't compact first.
> - **Handoff:** no. `docs/routing-investigation.md` holds the goal, decisions, and reproduction command; the only gap is the unverified `src/routes.tsx` edit, which the starter prompt flags.
> - **Model / effort:** keep current. No model or effort change is warranted, and I can't confirm your current settings from here.
> - **Reason:** the remaining work is a guard trace on a pre-existing reproduction, and stale API assumptions in this context risk repeating the same error.
> - **Do next:**
>   1. Open a new session.
>   2. Paste the prompt below.
>
> **Starter prompt**
>
> ```text
> Goal: find why /settings redirects to /login after a successful sign-in. Read docs/routing-investigation.md for the goal, decisions, and reproduction command. The existing edit in src/routes.tsx is unverified, so check it against the installed React Router version's docs rather than assuming it is correct. Do not rely on prior assumptions about React Router APIs; confirm against the installed version. Exact next action: trace the route guards without editing anything, then report findings before making changes.
> ```

---

## Ask for the deciding fact

_Asks for the next task instead of guessing whether a downgrade is worthwhile._

**Request:** `/session-checkpoint` Should we switch to a cheaper model? I have not said what the next task is. Current model is Sol at medium effort. There is no evidence of context trouble.

**Response:**

> Continue with Sol at medium effort. No change is warranted.
>
> - **Session:** continue
> - **Handoff:** no
> - **Model / effort:** Sol / medium (no change)
> - **Reason:** The next task is unknown, so there is no basis to price a cheaper model, and switching would forfeit prompt-cache savings and context for a saving I can't show.
> - **Do next:** Tell me the next task. If it is bounded, mechanical work with clear acceptance criteria, I'll reassess whether Terra (Standard) or Luna (Light) is worth the switch.

---

## Hold the recommendation on pushback

_Keeps the recommendation when no facts change._

**Request:** `/session-checkpoint` Earlier you recommended staying because context is accurate and the next task is a mechanical CSS class rename. No facts have changed. But surely a long session means we must start fresh? Current model is Haiku at low effort.

**Response:**

> Continue with Haiku at low effort (no change).
>
> - **Session:** continue
> - **Handoff:** no
> - **Model / effort:** Haiku / low (no change)
> - **Reason:** Session length alone doesn't justify a reset. The context is still accurate, and a mechanical CSS class rename is already at the Light tier. Starting fresh would only add context-rebuilding cost for no reliability gain. Nothing has changed since my earlier assessment, and no new fact or flaw in my reasoning has come up.
> - **Do next:** Nothing for you to do. I'll proceed with the rename.
>
> A fresh start would make sense if I started missing constraints, contradicting myself, going in circles, or ignoring evidence. I'm not seeing any of that.

---

## Leave handoff creation to the user

_Recommends a handoff without writing it or inventing its location._

**Request:** `/session-checkpoint` Start a fresh session for a difficult intermittent cache invalidation bug. Several corrected assumptions keep recurring. The reproduction, rejected hypotheses, and unverified changes exist only in this conversation. No durable record exists. Next trace the invalidation event order before editing. I use OpenAI models and prefer to create any handoff myself with my own command. Assess only; do not write files.

**Response:**

> - **Session:** start fresh
> - **Handoff:** recommended
> - **Model / effort:** Sol / high (current model and effort unknown)
> - **Reason:** Corrected assumptions keep recurring, which makes continuing here less reliable. No durable record exists, so the reproduction, rejected hypotheses, and unverified changes would be lost without a handoff.
> - **Do next:**
>   1. Create the handoff yourself with your own command. It must capture the reproduction steps, the rejected hypotheses with the evidence that rejected them, the corrected assumptions, and the unverified changes, each marked unverified.
>   2. Open a new session on Sol at high effort and paste the starter prompt below, supplying the handoff.
>
> **Starter prompt**
>
> ```text
> Debug an intermittent cache invalidation bug. Read the handoff I supply for the reproduction, the rejected hypotheses, and the unverified changes; treat those changes as unverified and the rejected hypotheses as closed unless you find new evidence. Next action: trace the order of invalidation events, with no edits until the trace is complete.
> ```

---

## Avoid a short-lived downgrade

_Considers switching costs rather than downgrading for a small mechanical task._

**Request:** `/session-checkpoint` Current model is Astra / high. The difficult architecture decision is finished and documented. The next task is a mechanical rename of a private CSS class in two known files, with exact replacements supplied and npm test afterward. Context is accurate. The harness supports switching models in place without rebuilding context. Assess before continuing.

**Response:**

> - **Session:** continue
> - **Handoff:** no
> - **Model / effort:** Luna / low
> - **Reason:** The remaining work is a mechanical rename with exact replacements and a test run, so Strongest/high capability is no longer needed, and the context is accurate so there's no reason to reset.
> - **Do next:**
>   1. Switch the model to Luna and effort to low in place.
>   2. Resume the rename in the two files and run `npm test`.
>
> Switching models loses prompt-cache savings on the existing context, but with the rename this small and the in-place switch not rebuilding context, the remaining work should still cost less on Luna than staying on Astra / high.

---

## Increase effort without changing the model

_Pauses for an effort decision that affects the quality of the remaining analysis._

**Request:** `/session-checkpoint` Current model is Sol / low. We have a deterministic reproduction of an event-ordering race across three async functions. The relevant code and evidence are already in context and assumptions are accurate. Next task is carefully tracing event interleavings before editing. I want to keep the current model if suitable; effort can change in place. Assess only.

**Response:**

> - **Session:** continue (context is accurate and directly useful)
> - **Handoff:** no
> - **Model / effort:** Sol / high (keep the model, raise effort)
> - **Reason:** Tracing interleavings across three async functions is difficult diagnosis where a missed ordering means a wrong fix and rework, and Sol is the Strong tier suited to it; low effort is the risk here, not the model.
> - **Do next:** Raise effort to high in place, then proceed with the trace. Once the root cause is pinned down and the edit is bounded, dropping back to low is reasonable.

---

## Handle missing task and settings

_Asks for the missing task without inventing current settings._

**Request:** `/session-checkpoint` Before continuing, assess the session. I have not provided the next task or the current model and effort settings. No task history is available.

**Response:**

> Continue with the current model and effort (keep current): there is no task or context problem to act on, so no session change is warranted.
>
> What's the next task? Once I know it, I can say whether it calls for a different model, effort, or a fresh session. Tell me the current model and effort too if you want an exact "(no change)" confirmation.

---

## Upgrade before risky work

_Pauses before consequential work while preserving useful session context._

**Request:** `/session-checkpoint` Current model is Haiku / low. Context is reliable and includes current schema and security constraints. Next is deciding a multi-tenant database migration strategy with rollback and data-isolation risks. The harness supports switching models without losing context. Assess only.

**Response:**

> - **Session:** continue (context is reliable; no reset or compact needed)
> - **Handoff:** no
> - **Model / effort:** Opus / high (Strong tier; Haiku is Light and Opus is the Anthropic Strong label from the default mapping, since no user or runtime mapping was supplied)
> - **Reason:** A migration strategy with rollback and data-isolation risks is safety-critical judgment, so Light-tier capability at low effort risks a flawed plan that costs far more to unwind than the model switch does.
> - **Do next:**
>   1. Switch to Opus at high effort (switching in place keeps this context, though prompt-cache savings from the current model won't carry over).
>   2. Resume with the migration strategy decision.

---

## Keep working through marginal effort changes

_Keeps the current setup across a file read followed by analysis._

**Request:** `/session-checkpoint` Current model is Opus / high. You are authorized to finish diagnosing a complex race and verifying the fix without further approval. Context is accurate. The next operation is reading one short source file, followed immediately by difficult event-order analysis. Should we lower effort just for that read? Assess only.

**Response:**

> Continue with Opus / high (no change). Lowering effort for one short file read saves almost nothing, and the event-order analysis that follows needs high effort anyway, so a drop-then-raise swap adds interruption for no benefit.

---

## Account for lost cache savings

_Rejects a nominally cheaper model when losing the warm cache makes the switch cost more._

**Request:** `/session-checkpoint` Current model is Sol / medium. Context is accurate. Only a small, bounded implementation and verification remain. Terra can perform the work reliably. The runtime reports that switching models keeps the conversation but loses its warm prompt cache; the estimated cache refill alone costs more than all remaining generation savings. Should we switch? Assess only.

**Response:**

> - **Session:** continue
> - **Handoff:** no
> - **Model / effort:** Sol / medium (no change)
> - **Reason:** Terra could do the work, but the cache refill alone costs more than all the generation savings, so switching raises total cost for no gain in reliability.
> - **Do next:** Nothing; stay on Sol / medium for the remaining implementation and verification, since you asked for assessment only.

---

## Pause for sustained net savings

_Treats a material net cost reduction as a reason to ask the user before continuing._

**Request:** `/session-checkpoint` Current model is Astra / high. Architecture is complete and documented. You are authorized to finish a large batch of exact, independent mechanical edits and their specified verification. Luna / low is available and reliable for this work. The runtime cost estimate includes cache loss, rebuilding context, verification, and likely later upgrades; switching still cuts the remaining total cost substantially. Context is accurate. Apply the checkpoint before proceeding.

**Response:**

> - **Session:** continue (context is accurate; no compact or fresh start needed)
> - **Handoff:** no
> - **Model / effort:** Luna / low (changed from Astra / high)
> - **Reason:** The remaining work is exact, independent mechanical edits against complete architecture, and your runtime estimate, which already counts cache loss, context rebuilding, verification, and later upgrades, shows a substantial net saving.
> - **Do next:** Switch to Luna / low, then tell me to resume the batch edits and their specified verification. I can't change the model setting myself, so I'm pausing here rather than continuing on Astra / high.

---

## Change vendors when a quota runs out

_Uses the mapping for an alternative without treating it as proof of identity or identical settings._

**Request:** `/session-checkpoint` Current model is Opus / high. The provider reports that the Anthropic quota is exhausted; no further work can run there. I have access to OpenAI Sol with high effort. The remaining task is difficult diagnosis, and the existing conversation and investigation record are reliable. I can switch vendors in place; the prompt cache will be lost. Assess only and recommend how to resume.

**Response:**

> - **Session:** continue. Neither stale context nor corrections are the problem here, and the record is reliable.
> - **Handoff:** no. The conversation and the existing investigation record can carry the state, so no separate handoff is needed.
> - **Model / effort:** Sol / high, which is Strong tier and matches the difficulty of the remaining diagnosis. The tier comes from the default mapping, since none was supplied.
> - **Reason:** Anthropic quota is exhausted, so staying on Opus is not an option. Sol at high effort matches the diagnosis the remaining work needs, and the lost prompt cache is an unavoidable one-time cost.
> - **Do next:**
>   1. Switch the vendor to OpenAI Sol with high effort, in place and without compacting.
>   2. Resume with the exact next diagnostic step from the investigation record. Let Sol re-read the record before acting, since it starts with a cold cache.

---

## Ask for a model when no mapping applies

_Asks for an exact available model rather than directing the user to an ambiguous tier._

**Request:** `/session-checkpoint` No current model, vendor, or effort is exposed. My available vendor is not Anthropic or OpenAI, and I have not supplied its model mapping. Next is deciding a risky multi-tenant data migration with rollback and isolation constraints. Existing context is reliable. Assess only; do not guess a model name.

**Response:**

> - **Session:** continue. You describe the existing context as reliable, and nothing points to staleness or a context-space problem.
> - **Handoff:** no
> - **Model / effort:** selection needed. The target is the Strong tier at high effort, but your vendor has no mapping, so I won't name a model. The current model and effort are unknown, so I can't say whether anything needs to change.
> - **Reason:** a risky multi-tenant migration with rollback and isolation constraints is safety-critical judgment, so a rework or missed isolation case would cost more than the extra effort.
> - **Do next:** tell me which model on your vendor corresponds to Strong (or paste a tier mapping), and confirm your current model and effort. If the current model is already at that capability, keep it and set effort to high; if not, switch before we decide the migration.

---

## Keep internal checks silent

_Continues authorized work without emitting another checkpoint or asking for approval._

**Request:** `/session-checkpoint` You are using these rules for an internal check, not responding to an explicit checkpoint request. Current model is Haiku / low. The user authorized you to finish labeling the supplied records without further approval. Context is accurate and no transition or material cost decision is needed. One record remains. Apply the existing rule: label a record urgent if its severity is 4 or greater; otherwise label it routine. Record: ID R7, severity 5. Complete the authorized work now.

**Response:**

> **R7: urgent** (severity 5 is 4 or greater).

---

## Use models from other vendors

_Uses exact user-supplied deployment aliases rather than the bundled vendors._

**Request:** `/session-checkpoint` My already-loaded agent instructions say: For session-checkpoint, replace the default mapping with Strongest: unavailable; Strong: Kimi / review-deployment; Standard: Meta / build-deployment; Light: SpaceX / batch-deployment. These are hypothetical user-defined deployment aliases, not public model names. Current model is SpaceX / batch-deployment at low effort. Next is deciding a risky multi-tenant migration with rollback and tenant-isolation constraints. Context is accurate. Assess only before that work.

**Response:**

> - **Session:** continue
> - **Handoff:** no
> - **Model / effort:** Kimi / review-deployment, high effort
> - **Reason:** A multi-tenant migration with rollback and tenant-isolation constraints is safety-critical judgment, which needs Strong capability; the current Light batch-deployment at low effort is built for mechanical work, and a wrong call here costs more than a model switch.
> - **Do next:** Switch to Kimi / review-deployment at high effort, then resume in this session. The context is accurate, so no compact or fresh start is needed. If the switch loses prompt-cache savings, that is minor next to the risk of a weak migration decision.
>
> Strongest is unavailable under your mapping. Strong is the highest tier you have, so no further upgrade applies. Assessment only; I have not started the migration work.

---

## Do not fill omitted tiers from defaults

_Asks for an exact model when a partial override leaves the required tier unmapped._

**Request:** `/session-checkpoint` For session-checkpoint, replace the defaults with this complete user mapping: Strong = Kimi / review-deployment. This is a hypothetical deployment alias. All other tiers are omitted. Current model is Kimi / review-deployment at high effort. A large batch of exact mechanical edits and specified verification remains. My runtime estimate establishes substantial net savings from moving this batch to a Light-tier model, after cache loss, switching, and likely upgrades. I have not identified that model. Assess only; do not guess a model or use bundled models.

**Response:**

> - **Session:** continue
> - **Handoff:** no
> - **Model / effort:** selection needed (target tier: Light; effort: low) — your mapping assigns only Strong, so no Light model is mapped. Current Kimi / review-deployment at high effort is mapped to Strong.
> - **Reason:** The remaining exact mechanical edits and specified verification fit Light at low effort, and your estimate shows substantial net savings after cache loss, switching, and likely upgrades. Staying at Strong and high effort is adequate but costlier.
> - **Do next:**
>   1. Tell me which available Light-tier model to use. I won't guess one or fall back to bundled models.
>   2. Switch to it at low effort. A new session isn't needed; compaction would only matter if context space is constrained.
>   3. Keep the specified verification in the batch so its results confirm the edits.
>
> If you'd rather not pick a model, keep Kimi / review-deployment and finish the batch; it's adequate, just costlier.

---

## Do not interrupt for an irrelevant mapping gap

_Completes authorized work without requesting mappings that do not affect it._

**Request:** `/session-checkpoint` This is an internal check, not an explicit checkpoint request. My existing instructions replace the default mapping with Standard = Meta / build-deployment, a hypothetical deployment alias; other tiers are omitted. Current model is Kimi / review-deployment at medium effort and is known to be reliable for this task. There is no material cost or outcome benefit to changing settings. You are authorized to finish labeling records without further approval: severity 4 or greater is urgent; otherwise routine. The last record is ID R8, severity 2. Complete the work.

**Response:**

> R8 (severity 2): routine, since it falls below the severity 4 threshold for urgent.
>
> That completes the labeling task, and R8 was the last record. One caveat: I only have R8's ID and severity in this conversation, so I can't restate the full set of records or earlier labels here. I haven't verified them.
>
> I don't recommend a model, effort, or session change. Kimi / review-deployment at medium effort is reliable for this task, and no material cost or outcome benefit has been shown for a switch, so I'm continuing with the current settings.

---

## Prefer the user mapping over runtime defaults

_Resolves a recommendation from the user override rather than the runtime mapping._

**Request:** `/session-checkpoint` The runtime supplies this tier mapping: Strong = OpenAI Sol, Light = OpenAI Luna. My user override for session-checkpoint replaces it with Strong = Meta / reasoning-deployment, Light = Kimi / batch-deployment; these are hypothetical deployment aliases and other tiers are omitted. Current model is Kimi / batch-deployment at low effort. Next is deciding a security-sensitive multi-tenant migration with rollback and isolation risks; Strong-tier analysis is needed. Context is reliable. Assess only and identify the concrete model to use.

**Response:**

> - **Session:** continue
> - **Handoff:** no
> - **Model / effort:** Meta / reasoning-deployment, high effort (change from Kimi / batch-deployment at low effort)
> - **Reason:** A security-sensitive multi-tenant migration with rollback and isolation risks needs Strong-tier judgment, and a wrong call costs far more than the switch; your override maps Strong to Meta / reasoning-deployment, replacing the runtime's OpenAI Sol mapping.
> - **Do next:** Switch the model to Meta / reasoning-deployment and set effort to high in this session, then ask for the migration decision. Context is reliable, so no compaction is needed. The switch will forfeit the current model's prompt-cache savings, which is acceptable here.
>
> I have not started the analysis, as you asked for assessment only.

---

## Keep subsystem context for a new task

_Treats a change from debugging to feature work as a context decision, not an automatic reset._

**Request:** `/session-checkpoint` I finished fixing an invoicing bug. Now I want to add invoice filtering in the same subsystem. The loaded schema, constraints, and test setup are current and useful; no recurring corrected assumptions or context pressure. Current model is Sonnet / medium, and it is adequate for the feature. Should I start a new session? Assess only.

**Response:**

> **Session:** continue. A new session isn't needed.
>
> **Handoff:** no
>
> **Model / effort:** Sonnet / medium (no change)
>
> **Reason:** The feature stays in the same subsystem, the loaded schema, constraints, and test setup are still current and useful, and nothing points to stale assumptions or context pressure. A fresh session would only rebuild context you already have.
>
> **Do next:** Keep going in this session. When you're ready, tell me the filtering requirements (which fields to filter on, and whether it's API-only or UI too).

---

## Append advice after completing the request

_Keeps the normal final report first and adds a useful transition recommendation afterward._

**Request:** This is an internal checkpoint at completion, not a manual checkpoint request. The user asked only for a routing investigation, which is now complete. Report these observed results: /settings redirects to /login because the route guard reads the obsolete session flag; no files changed; the supplied reproduction still fails. The next task, already identified by the user but not authorized yet, is implementing the fix. We repeatedly returned to obsolete API assumptions despite corrections, so a fresh session would reduce that risk. docs/routing-investigation.md contains the goal, evidence, rejected assumptions, reproduction, and exact next action: update the guard to use the current auth state, then rerun the reproduction. Current model is Opus / high and adequate. Give the normal final report and apply the skill.

**Response:**

> The investigation is complete.
>
> **Findings**
> - `/settings` redirects to `/login` because the route guard reads the obsolete session flag instead of the current auth state.
> - No files changed.
> - The supplied reproduction still fails, as expected, since nothing has been fixed.
>
> **Checkpoint**
> - **Session:** start fresh
> - **Handoff:** no
> - **Model / effort:** Opus / high (no change)
> - **Reason:** The conversation repeatedly returned to obsolete API assumptions despite corrections, so a fresh session lowers the risk that the fix repeats them.
> - **Do next:** Open a new session and paste the starter prompt below.
>
> **Starter prompt**
> ```text
> Read docs/routing-investigation.md for the goal, evidence, rejected assumptions, and reproduction. Then update the route guard to use the current auth state instead of the obsolete session flag, and rerun the reproduction to verify the fix. Do not rely on the rejected assumptions listed in that document.
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

> Tax: 125 × 0.08 = 10.00
>
> **Total: 125 + 10.00 = 135.00**

---

## Recommend a reset without inventing the next task

_Allows completion-time context advice without asking for or inventing future work._

**Request:** This is an internal checkpoint at completion, not a manual checkpoint request. The user requested only a configuration audit. It is complete: found one obsolete timeout setting, made no changes, and recorded all findings and verification limits in docs/config-audit.md. We repeatedly ignored corrections and reused stale assumptions during the audit, so continuing this conversation would be unreliable. No next task is known. Current model is Opus / high, adequate apart from the context issue. Give the final report and any warranted transition advice. Do not start another task.

**Response:**

> **Configuration audit complete.** I found one obsolete timeout setting and made no changes. All findings and the limits of what I could verify are recorded in `docs/config-audit.md`. Treat that file as the source of truth for this audit, not my recollection of the conversation, since I reused stale assumptions and ignored corrections along the way.
>
> **Checkpoint**
>
> - **Session:** start fresh
> - **Handoff:** no
> - **Model / effort:** Opus / high (no change)
> - **Reason:** This conversation repeatedly ignored corrections and reused stale assumptions, so continuing here would be unreliable.
> - **Do next:** Open a new session for any follow-up, such as acting on the obsolete timeout, and begin with the starter prompt below.
>
> **Starter prompt**
>
> ```text
> Read docs/config-audit.md, which records a completed, read-only configuration audit: one obsolete timeout setting was found, nothing was changed, and the file notes what could not be verified. Treat its findings as the starting point and confirm the timeout setting against the current config before relying on it. Then wait for my instructions on what to do with the findings.
> ```
