# session-checkpoint examples

Real, unedited responses from the final [`session-checkpoint`](SKILL.md) skill, generated on 2026-11-08. Each request used a fresh, stateless call with the skill body and bundled [default model mapping](model-mapping.md) as system context and no tools. The harness selected its default model; the concrete model ID was not exposed.

The scenarios are hypothetical. Technical claims in the responses are unverified. These examples show decisions and output behavior, not measured cost savings, live tool execution, or proof of better reasoning. Custom deployment aliases in the override cases are hypothetical user-supplied names, not claims about public models. Internal-check scenarios supply their complete tasks in the requests.

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

---

## Continue with useful context

_Agrees with a sound plan without adding transition overhead._

**Request:** `/session-checkpoint` Before continuing: current context is accurate and useful, the focused unit test passed, and the next action is running npm test for the web app. I think we should stay. Current model is Terra at medium effort.

**Response:**

> Continue with Terra at medium effort (no change). Next: run `npm test` for the web app.

---

## Compact without resetting

_Preserves reliable state when the harness reports context pressure._

**Request:** `/session-checkpoint` The harness reports little context remains. Reasoning has been consistent while debugging a Vite build. We need a concise summary preserving the failing build command, config changes, and pending verification, then continue the same task. Current model is Sonnet at medium effort. Assess only.

**Response:**

> - **Session:** compact
> - **Handoff:** no
> - **Model / effort:** Sonnet / medium (no change)
> - **Reason:** Context is nearly exhausted, but consistent reasoning makes a concise summary sufficient to preserve reliable progress.
> - **Do next:** Compact, preserving the exact failing build command, configuration changes, and pending verification; then resume the same Vite build debugging task.

---

## Start fresh using existing records

_Preserves unfinished changes and a failing reproduction in a paste-ready prompt._

**Request:** `/session-checkpoint` We repeatedly used obsolete React Router API assumptions despite corrections. Should we start fresh? Existing docs/routing-investigation.md records the goal, decisions, and reproduction command. src/routes.tsx has an unverified edit. The latest reproduction still redirects /settings to /login after a successful sign-in. No other blocker. Next trace route guards before editing.

**Response:**

> - **Session:** start fresh
> - **Handoff:** no
> - **Model / effort:** keep current / keep current
> - **Reason:** Repeated obsolete React Router assumptions despite corrections make continued reasoning less reliable; the existing investigation document can carry the essential state.
> - **Do next:** Open a fresh session and paste the starter prompt below.
>
> **Starter prompt**
> ```text
> Investigate why /settings still redirects to /login after a successful sign-in. Read the repository instructions and docs/routing-investigation.md for the goal, decisions, and reproduction command. src/routes.tsx contains an unverified edit, and the latest reproduction still fails. Trace the route guards before editing, checking API assumptions against the installed React Router version. There are no other known blockers.
> ```

---

## Ask for the deciding fact

_Asks for the next task instead of guessing whether a downgrade is worthwhile._

**Request:** `/session-checkpoint` Should we switch to a cheaper model? I have not said what the next task is. Current model is Sol at medium effort. There is no evidence of context trouble.

**Response:**

> Keep Sol at medium effort. What’s the next task?

---

## Hold the recommendation on pushback

_Keeps the recommendation when no facts change._

**Request:** `/session-checkpoint` Earlier you recommended staying because context is accurate and the next task is a mechanical CSS class rename. No facts have changed. But surely a long session means we must start fresh? Current model is Haiku at low effort.

**Response:**

> Continue with Haiku at low effort (no change). Session length alone does not justify starting fresh; the accurate context still helps with the mechanical CSS class rename.

---

## Leave handoff creation to the user

_Recommends a handoff without writing it or inventing its location._

**Request:** `/session-checkpoint` Start a fresh session for a difficult intermittent cache invalidation bug. Several corrected assumptions keep recurring. The reproduction, rejected hypotheses, and unverified changes exist only in this conversation. No durable record exists. Next trace the invalidation event order before editing. I use OpenAI models and prefer to create any handoff myself with my own command. Assess only; do not write files.

**Response:**

> - **Session:** start fresh
> - **Handoff:** recommended
> - **Model / effort:** keep current / keep current
> - **Reason:** Recurring corrected assumptions make continuation unreliable, and a handoff is needed to preserve evidence that exists only in this conversation.
> - **Do next:**
>   1. Use your own command to create a handoff capturing the reproduction, corrected assumptions, rejected hypotheses and supporting evidence, and unverified changes. Record tracing invalidation event order as the next action.
>   2. Open a fresh session and supply that handoff with the starter prompt below.
>
> **Starter prompt**
> ```text
> Investigate the difficult intermittent cache invalidation bug. Read the handoff I supply for the reproduction, corrected assumptions, rejected hypotheses, and unverified changes. First trace the invalidation event order before editing; distinguish observed evidence from hypotheses, and do not treat unverified changes as validated.
> ```

---

## Avoid a short-lived downgrade

_Considers switching costs rather than downgrading for a small mechanical task._

**Request:** `/session-checkpoint` Current model is Astra / high. The difficult architecture decision is finished and documented. The next task is a mechanical rename of a private CSS class in two known files, with exact replacements supplied and npm test afterward. Context is accurate. The harness supports switching models in place without rebuilding context. Assess before continuing.

**Response:**

> Continue with Astra / high (no change); the two-file rename and test run are too small to establish a material net benefit from switching.

---

## Increase effort without changing the model

_Pauses for an effort decision that affects the quality of the remaining analysis._

**Request:** `/session-checkpoint` Current model is Sol / low. We have a deterministic reproduction of an event-ordering race across three async functions. The relevant code and evidence are already in context and assumptions are accurate. Next task is carefully tracing event interleavings before editing. I want to keep the current model if suitable; effort can change in place. Assess only.

**Response:**

> - **Session:** continue
> - **Handoff:** no
> - **Model / effort:** Sol / high
> - **Reason:** Sol is suitable, but tracing interleavings across three async functions warrants higher effort to avoid missing ordering constraints before editing; the accurate context should be preserved.
> - **Do next:** Change effort to high in this session, then request the interleaving trace before any edits.

---

## Handle missing task and settings

_Asks for the missing task without inventing current settings._

**Request:** `/session-checkpoint` Before continuing, assess the session. I have not provided the next task or the current model and effort settings. No task history is available.

**Response:**

> Keep current model and effort settings. What task would you like to work on next?

---

## Upgrade before risky work

_Pauses before consequential work while preserving useful session context._

**Request:** `/session-checkpoint` Current model is Haiku / low. Context is reliable and includes current schema and security constraints. Next is deciding a multi-tenant database migration strategy with rollback and data-isolation risks. The harness supports switching models without losing context. Assess only.

**Response:**

> - **Session:** continue
> - **Handoff:** no
> - **Model / effort:** Opus / high
> - **Reason:** Tenant-isolation and rollback risks warrant stronger architectural judgment; keeping the reliable context avoids rebuilding it.
> - **Do next:**
>   1. Switch to Opus with high effort in this session.
>   2. Resume the migration-strategy decision using the current schema and security constraints.

---

## Keep working through marginal effort changes

_Keeps the current setup across a file read followed by analysis._

**Request:** `/session-checkpoint` Current model is Opus / high. You are authorized to finish diagnosing a complex race and verifying the fix without further approval. Context is accurate. The next operation is reading one short source file, followed immediately by difficult event-order analysis. Should we lower effort just for that read? Assess only.

**Response:**

> Continue with Opus / high (no change); lowering effort for a short read before difficult event-order analysis offers no worthwhile total-cost benefit.

---

## Account for lost cache savings

_Rejects a nominally cheaper model when losing the warm cache makes the switch cost more._

**Request:** `/session-checkpoint` Current model is Sol / medium. Context is accurate. Only a small, bounded implementation and verification remain. Terra can perform the work reliably. The runtime reports that switching models keeps the conversation but loses its warm prompt cache; the estimated cache refill alone costs more than all remaining generation savings. Should we switch? Assess only.

**Response:**

> Continue with Sol / medium (no change); the cache-refill cost exceeds the remaining generation savings.

---

## Pause for sustained net savings

_Treats a material net cost reduction as a reason to ask the user before continuing._

**Request:** `/session-checkpoint` Current model is Astra / high. Architecture is complete and documented. You are authorized to finish a large batch of exact, independent mechanical edits and their specified verification. Luna / low is available and reliable for this work. The runtime cost estimate includes cache loss, rebuilding context, verification, and likely later upgrades; switching still cuts the remaining total cost substantially. Context is accurate. Apply the checkpoint before proceeding.

**Response:**

> - **Session:** continue
> - **Handoff:** no
> - **Model / effort:** Luna / low
> - **Reason:** The remaining edits are mechanical, and your total-cost estimate shows substantial savings from switching without sacrificing reliability.
> - **Do next:** Switch to Luna / low, then resume the authorized edits and their specified verification in this session.

---

## Change vendors when a quota runs out

_Uses the mapping for an alternative without treating it as proof of identity or identical settings._

**Request:** `/session-checkpoint` Current model is Opus / high. The provider reports that the Anthropic quota is exhausted; no further work can run there. I have access to OpenAI Sol with high effort. The remaining task is difficult diagnosis, and the existing conversation and investigation record are reliable. I can switch vendors in place; the prompt cache will be lost. Assess only and recommend how to resume.

**Response:**

> - **Session:** continue
> - **Handoff:** no
> - **Model / effort:** OpenAI Sol / high
> - **Reason:** Anthropic’s exhausted quota prevents continuation there; Sol is mapped to Strong for difficult diagnosis, and the reliable context warrants staying in this session despite the cache loss.
> - **Do next:**
>   1. Switch in place to OpenAI Sol with high effort.
>   2. Resume the diagnosis from the existing investigation record, retaining its constraints and distinguishing verified findings from open hypotheses.

---

## Ask for a model when no mapping applies

_Asks for an exact available model rather than directing the user to an ambiguous tier._

**Request:** `/session-checkpoint` No current model, vendor, or effort is exposed. My available vendor is not Anthropic or OpenAI, and I have not supplied its model mapping. Next is deciding a risky multi-tenant data migration with rollback and isolation constraints. Existing context is reliable. Assess only; do not guess a model name.

**Response:**

> - **Session:** continue
> - **Handoff:** no
> - **Model / effort:** selection needed—Strong-tier capability / high effort; current settings unknown.
> - **Reason:** Tenant-isolation and rollback failures could cause data loss or cross-tenant exposure, so model suitability matters before deciding the migration.
> - **Do next:** Which model from your available vendor should we use for this high-risk analysis? Supply its name or your tier mapping; the existing context can stay.

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
> - **Reason:** The risky migration requires difficult judgment about rollback and tenant isolation; the current Light deployment at low effort is not adequate, while the accurate context remains useful.
> - **Do next:**
>   1. Switch to Kimi / review-deployment and set effort to high.
>   2. Resume in this session to evaluate the migration, rollback strategy, and tenant-isolation constraints.

---

## Do not fill omitted tiers from defaults

_Asks for an exact model when a partial override leaves the required tier unmapped._

**Request:** `/session-checkpoint` For session-checkpoint, replace the defaults with this complete user mapping: Strong = Kimi / review-deployment. This is a hypothetical deployment alias. All other tiers are omitted. Current model is Kimi / review-deployment at high effort. A large batch of exact mechanical edits and specified verification remains. My runtime estimate establishes substantial net savings from moving this batch to a Light-tier model, after cache loss, switching, and likely upgrades. I have not identified that model. Assess only; do not guess a model or use bundled models.

**Response:**

> - **Session:** continue
> - **Handoff:** no
> - **Model / effort:** selection needed for Light tier / low effort
> - **Reason:** Your estimate establishes substantial net savings for the mechanical batch, but your mapping provides no Light-tier model.
> - **Do next:** Identify an available Light-tier model and vendor before switching; retain the current settings until then.

---

## Do not interrupt for an irrelevant mapping gap

_Completes authorized work without requesting mappings that do not affect it._

**Request:** `/session-checkpoint` This is an internal check, not an explicit checkpoint request. My existing instructions replace the default mapping with Standard = Meta / build-deployment, a hypothetical deployment alias; other tiers are omitted. Current model is Kimi / review-deployment at medium effort and is known to be reliable for this task. There is no material cost or outcome benefit to changing settings. You are authorized to finish labeling records without further approval: severity 4 or greater is urgent; otherwise routine. The last record is ID R8, severity 2. Complete the work.

**Response:**

> R8: routine

---

## Prefer the user mapping over runtime defaults

_Resolves a recommendation from the user override rather than the runtime mapping._

**Request:** `/session-checkpoint` The runtime supplies this tier mapping: Strong = OpenAI Sol, Light = OpenAI Luna. My user override for session-checkpoint replaces it with Strong = Meta / reasoning-deployment, Light = Kimi / batch-deployment; these are hypothetical deployment aliases and other tiers are omitted. Current model is Kimi / batch-deployment at low effort. Next is deciding a security-sensitive multi-tenant migration with rollback and isolation risks; Strong-tier analysis is needed. Context is reliable. Assess only and identify the concrete model to use.

**Response:**

> - **Session:** continue
> - **Handoff:** no
> - **Model / effort:** Meta / reasoning-deployment / high
> - **Reason:** Your override replaces the runtime mapping; the migration’s security-sensitive isolation and rollback decisions warrant Strong-tier analysis, while the reliable context supports continuing this session.
> - **Do next:**
>   1. Switch to Meta / reasoning-deployment at high effort.
>   2. Continue the migration analysis in this session.
