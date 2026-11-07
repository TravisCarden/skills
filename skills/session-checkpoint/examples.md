# session-checkpoint examples

Real, unedited responses from the final [`session-checkpoint`](SKILL.md) skill, generated on 2026-11-07. Each request used a fresh, stateless call with the skill body as the system prompt and no tools. The harness selected its default model; the concrete model ID was not exposed.

The scenarios are hypothetical. Technical claims in the responses are unverified. These examples show the skill's output, not measured cost savings or proof of better reasoning.

This file is for people. `SKILL.md` does not reference it, so agents do not load it.

## Contents

- [Continue with useful context](#continue-with-useful-context)
- [Compact without resetting](#compact-without-resetting)
- [Start fresh using existing records](#start-fresh-using-existing-records)
- [Ask for the deciding fact](#ask-for-the-deciding-fact)
- [Hold the recommendation on pushback](#hold-the-recommendation-on-pushback)
- [Leave handoff creation to the user](#leave-handoff-creation-to-the-user)
- [Downgrade for mechanical work](#downgrade-for-mechanical-work)
- [Increase effort without changing the model](#increase-effort-without-changing-the-model)
- [Handle missing task and settings](#handle-missing-task-and-settings)
- [Change models without resetting](#change-models-without-resetting)

---

## Continue with useful context

_Agrees with a sound plan without adding transition overhead._

**Request:** `/session-checkpoint` Before continuing: current context is accurate and useful, the focused unit test passed, and the next action is running npm test for the web app. I think we should stay. Current model is Terra at medium effort.

**Response:**

> - **Session:** continue
> - **Handoff:** no
> - **Model / effort:** Terra / medium (no change)
> - **Do next:** Run `npm test` for the web app.

---

## Compact without resetting

_Preserves reliable working context when the harness reports little space remains._

**Request:** `/session-checkpoint` The harness reports little context remains. Reasoning has been consistent while debugging a Vite build. We need a concise summary preserving the failing build command, config changes, and pending verification, then continue the same task. Current model is Sonnet at medium effort. Assess only.

**Response:**

> - **Session:** compact
> - **Handoff:** no
> - **Model / effort:** Sonnet / medium (no change)
> - **Reason:** Reported context pressure warrants compaction; consistent debugging supports preserving the current approach.
> - **Do next:**
>   1. Compact the context, preserving the exact failing build command, failure output, config changes, and pending verification; mark unverified changes clearly.
>   2. Resume the Vite build debugging with the pending verification, following repository rules.

---

## Start fresh using existing records

_Carries existing coding evidence and unfinished changes into a fresh-session prompt._

**Request:** `/session-checkpoint` We repeatedly used obsolete React Router API assumptions despite corrections. Should we start fresh? Existing docs/routing-investigation.md records the goal, decisions, and reproduction command. src/routes.tsx has an unverified edit. The latest reproduction still redirects /settings to /login after a successful sign-in. No other blocker. Next trace route guards before editing.

**Response:**

> - **Session:** start fresh
> - **Handoff:** no
> - **Model / effort:** Opus or Sol / medium
> - **Reason:** Repeated obsolete API assumptions despite corrections make continuation less reliable; the existing record and a short prompt preserve the useful state.
> - **Do next:** Open a new session with the starter prompt below.
>
> **Starter prompt**
> ```text
> Investigate why /settings still redirects to /login after a successful sign-in. Read docs/routing-investigation.md for the goal, decisions, and reproduction command. src/routes.tsx contains an unverified edit; the latest reproduction still fails, and there are no other known blockers. Follow repository rules and required verification. Trace route guards before editing, checking assumptions against the installed React Router version rather than relying on obsolete APIs.
> ```

---

## Ask for the deciding fact

_Keeps the current configuration until the next task is specified._

**Request:** `/session-checkpoint` Should we switch to a cheaper model? I have not said what the next task is. Current model is Sol at medium effort. There is no evidence of context trouble.

**Response:**

> - **Session:** continue
> - **Handoff:** no
> - **Model / effort:** Sol / medium (no change)
> - **Do next:** Tell me the next task so I can assess whether a cheaper model would be reliable.

---

## Hold the recommendation on pushback

_Keeps the recommendation when no facts change._

**Request:** `/session-checkpoint` Earlier you recommended staying because context is accurate and the next task is a mechanical CSS class rename. No facts have changed. But surely a long session means we must start fresh? Current model is Haiku at low effort.

**Response:**

> - **Session:** continue
> - **Handoff:** no
> - **Model / effort:** Haiku / low (no change)
> - **Do next:** Proceed with the mechanical CSS class rename under the repository rules and required verification.

---

## Leave handoff creation to the user

_Recommends a handoff without creating it or inventing a path._

**Request:** `/session-checkpoint` Start a fresh session for a difficult intermittent cache invalidation bug. Several corrected assumptions keep recurring. The reproduction, rejected hypotheses, and unverified changes exist only in this conversation. No durable record exists. Next trace the invalidation event order before editing. I use OpenAI models and prefer to create any handoff myself with my own command. Assess only; do not write files.

**Response:**

> - **Session:** start fresh
> - **Handoff:** recommended
> - **Model / effort:** Sol / high
> - **Reason:** Recurring corrected assumptions make continuation less reliable; this intermittent diagnosis warrants Strong-tier reasoning.
> - **Do next:**
>   1. Use your own command to create a handoff preserving the reproduction, corrected assumptions, rejected hypotheses, and unverified changes.
>   2. Open a fresh session with Sol / high and supply your handoff with the starter prompt below.
>
> **Starter prompt**
> ```text
> Diagnose the difficult intermittent cache invalidation bug using the handoff I supply. It preserves the reproduction, corrected assumptions, rejected hypotheses, and unverified changes from the prior session. Follow repository rules and required verification. Your next action is to trace the invalidation event order before making any edits; treat existing changes as unverified.
> ```

---

## Downgrade for mechanical work

_Matches capability to the next task rather than earlier architecture work._

**Request:** `/session-checkpoint` Current model is Astra / high. The difficult architecture decision is finished and documented. The next task is a mechanical rename of a private CSS class in two known files, with exact replacements supplied and npm test afterward. Context is accurate. The harness supports switching models in place without rebuilding context. Assess before continuing.

**Response:**

> - **Session:** continue
> - **Handoff:** no
> - **Model / effort:** Luna / low
> - **Reason:** The rename is mechanical and fully specified; in-place switching preserves accurate context without rebuilding it.
> - **Do next:**
>   1. Switch to Luna / low.
>   2. Apply the supplied replacements in the two known files, following repository rules.
>   3. Run `npm test` and any other required verification.

---

## Increase effort without changing the model

_Separates effort from model capability and session choice._

**Request:** `/session-checkpoint` Current model is Sol / low. We have a deterministic reproduction of an event-ordering race across three async functions. The relevant code and evidence are already in context and assumptions are accurate. Next task is carefully tracing event interleavings before editing. I want to keep the current model if suitable; effort can change in place. Assess only.

**Response:**

> - **Session:** continue
> - **Handoff:** no
> - **Model / effort:** Sol / high
> - **Reason:** Tracing a race across three async functions warrants higher effort; the accurate context and deterministic reproduction support keeping Sol.
> - **Do next:** Trace the event interleavings against the existing reproduction and evidence before making edits.

---

## Handle missing task and settings

_Asks for the deciding task without inventing a current configuration._

**Request:** `/session-checkpoint` Before continuing, assess the session. I have not provided the next task or the current model and effort settings. No task history is available.

**Response:**

> - **Session:** continue
> - **Handoff:** no
> - **Model / effort:** keep current
> - **Do next:** What is your next task?

---

## Change models without resetting

_Preserves useful context while increasing capability for a consequential decision._

**Request:** `/session-checkpoint` Current model is Haiku / low. Context is reliable and includes current schema and security constraints. Next is deciding a multi-tenant database migration strategy with rollback and data-isolation risks. The harness supports switching models without losing context. Assess only.

**Response:**

> - **Session:** continue
> - **Handoff:** no
> - **Model / effort:** Opus / high
> - **Reason:** Migration strategy with rollback and tenant-isolation risks warrants deeper design reasoning than Haiku / low; your reliable schema and security context remains useful.
> - **Do next:**
>   1. Switch to Opus / high without resetting context.
>   2. Evaluate migration strategies against the current schema, security constraints, rollback feasibility, and tenant-isolation risks.
