---
name: session-checkpoint
description: "Assess whether to continue, compact, or start fresh, whether a handoff is needed, and which model tier and effort fit the next work. Use for explicit session-transition questions, when new evidence breaks the reliability of the current plan or recommendation, at task completion when a known next task or context problem warrants transition advice, or before work that needs a material session or model decision."
user-invocable: true
---

# Session Checkpoint

Assess whether the remaining work needs a session, model, or effort change. Base the assessment on the conversation and the runtime context, not on a fresh audit of the repository or environment. Repository rules and required verification stay in force.

Reassess when new evidence invalidates a recommendation or exposes stale assumptions, contradictions, or repeated missed constraints that make the current approach unreliable. This reliability break triggers an assessment, not an automatic transition or end of turn. Distinguish a local, recoverable mistake from a session state that can no longer reliably guide the remaining work.

## Continue or pause

An explicit invocation without an instruction to execute work is assessment-only, even when previously authorized work remains. Approval alone does not request execution. Recognize execution intent from ordinary language anywhere in the user's request, not a required keyword, command spelling, or argument: asking to proceed, finish the work, or get on with it can all request assessment followed by action. An explicit request to assess only or not start work takes precedence.

Use the conversation to identify the work and its authorization. A direct request to do a task, or to go ahead with a clearly identified proposed plan, can authorize that work; invoking the skill alone cannot. If the intended work or permission is unclear, ask only for what is missing rather than inventing a task or approval.

When execution is requested, or during an internal check while working, continue authorized work in the same turn unless a user decision would materially affect total cost or the reliability of the outcome. Pause before the affected work for a worthwhile cost reduction, an important capability or effort change, or a needed session transition. An instruction to proceed does not override that pause. Explain the concrete benefit or risk; marginal optimizations do not justify an interruption. Completing a phase is not completing the user's request.

Assessment-only invocations end with the assessment. Otherwise, if the assessment chooses continue, perform the next authorized action in the same turn; do not end with a status update, a promise to resume, or a request for permission already given. Ordinary continuation, a completed slice, and a recoverable mistake are not reasons to stop execution. Internal checks stay silent while work continues unless a user decision is needed.

After completing the requested work, append a checkpoint to the normal final response only when a known next task or a concrete context problem warrants a material recommendation. Otherwise finish without checkpoint boilerplate or asking for another task. The checkpoint follows a justified stop; it does not create one.

The user performs transitions. Do not change settings, compact, open a session, or write a handoff on the user's behalf.

## Decision

- **Continue** when the current context still helps the remaining work. Session length alone does not justify a reset.
- **Compact** when context space is constrained and a short summary can carry the reliable state forward. When disposable bulky output fills the context, prefer non-summarizing pruning if the runtime offers it. Preserve essential evidence or confirm it can be recovered; old output is not necessarily disposable. Pruning does not repair stale assumptions. If pruning is unavailable or leaves too little room, compact.
- **Start fresh** when stale assumptions, unrelated work, or accumulated corrections make continuation less reliable. Warning signs: repeated missed constraints, contradictions, circular investigation, and ignored evidence. Do not compact before starting fresh.
- On pushback, change the verdict only for a new fact or a flaw in your reasoning that you can name.

For pruning, use availability and behavior established by runtime context, loaded instructions, or the user. Consult a directly exposed, agent-callable command registry only when pruning is relevant and support is unknown. An extension API or harness name does not establish agent access or command availability. Do not search files, probe the environment, or invent commands or installed plugins. If support stays unknown, offer pruning conditionally, with compaction as the fallback. Recommend the operation; leave execution to the user.

## Model and effort

Identify the current model, vendor, and effort only from explicit runtime metadata or the user's statement, not from earlier recommendations or a tier mapping. Leave missing values unknown.

Choose capability tier and low, medium, or high effort separately for the remaining bounded work, not the hardest earlier task:

| Tier | Use for |
|---|---|
| Strongest | A concrete reason Strong is insufficient |
| Strong | Difficult diagnosis, ambiguous design, safety-critical judgment |
| Standard | Bounded implementation with clear acceptance criteria |
| Light | Mechanical edits and data collection |

Resolve model names from a user mapping in the conversation or already-loaded agent instructions (such as `AGENTS.md` or `CLAUDE.md`), then a runtime-supplied tier mapping. A mapping can be plain text assigning tiers to exact model names and vendors; no special file or syntax is required. A supplied mapping replaces the bundled defaults completely: omitted or unavailable tiers have no mapped model. Do not search for override files.

Only when neither source supplies a mapping, read [model-mapping.md](model-mapping.md) for the default labels. Mappings do not establish identity, availability, equal prices, or identical capabilities and settings. Prefer the current vendor unless availability, user preference, or material benefit favors another; offer cross-vendor alternatives only when relevant, such as a reported quota limit.

Name the mapped model in a change recommendation, not just its tier. If a material change needs a tier with no applicable model, state the needed capability and ask which available model to use instead of guessing or directing the user to an ambiguous category. An unresolved mapping alone does not justify interrupting adequate ongoing work.

Compare total cost over the remaining work: retries, rework, switching, context rebuilding, lost prompt-cache savings when changing models or vendors, and likely later upgrades. Preserving the conversation does not preserve its cache across models. Keep an adequate current setup unless concrete evidence supports a material net benefit. An easier next step, a lower generation rate, or switching in place without rebuilding context does not by itself establish a material net saving. Without that evidence, keep working rather than interrupting to price a switch. Lowering effort for a file read before raising it for analysis is not a useful saving. A model or effort change alone does not require a fresh session. Do not research catalogs or invent availability, cache measurements, context measurements, or savings.

## Transition state

Essential state is the goal, unfinished changes, decisions, verification results, blockers, and the exact next action. Prefer existing records: issue trackers, status files, commits. Recommend a handoff only when those records plus a short starter prompt cannot carry the essential state. If the prompt needs several paragraphs or a nested checklist, recommend a handoff instead of copying the transcript.

The user decides how to create a handoff. Never create one or imply it exists. Mark unverified work as unverified.

## Output

For an explicit checkpoint with no material decision, give one short line saying to continue with the current model and effort. Use "keep current" for unknown settings; mark "(no change)" only when both settings are known. If execution was requested, this is a non-blocking progress report: immediately perform the next authorized action in the same turn. Otherwise end with the assessment. Honor requests to omit the continue report, but still surface material decisions that require the user's intervention.

For a material decision, use short bold-labeled bullets in this order, addressed to the user. For an internal check at task completion, append them after the normal final response; otherwise give the checkpoint directly. The checkpoint itself needs no preamble, table, or closing paragraph. Keep each recommendation field to one concise line; Do next may contain numbered actions. Do not add commentary about unknown current settings.

- **Session:** continue, compact (or drop tool output), start fresh, or start fresh with handoff. Name a handoff only when it is recommended; omit any "no handoff" statement.
- **Model / effort:** the value alone, such as `Sol / high`, or `keep current`, or selection needed with the target tier if unresolved. Add "(no change)" only when both settings are known and match. Nothing else: no commentary, and no remarks about the current settings being unknown. Explanations go in Reason.
- **Reason:** one short sentence with the concrete benefit or risk behind the whole recommendation, including the session choice and any handoff
- **Do next:** direct actions for the user, including prerequisites. Keep a single action inline. Put distinct actions on separate numbered lines, including changing settings and resuming, compacting and resuming, or creating a handoff and opening a session. For a handoff, identify only the state missing from existing records.

If the next task is unknown, keep current settings. For an explicit checkpoint, ask only for the missing task rather than forcing a transition report. For an internal check, omit the checkpoint unless a concrete context problem warrants a transition recommendation; do not invent the next task.

For start fresh, add **Starter prompt** in a fenced `text` block: one short paragraph with the goal, the records to read, and the exact next action. If a handoff is recommended, tell the user to create it first, and have the prompt refer to "the handoff I supply," not an invented path. The prompt must paste unchanged and must not imply that unverified work passed.
