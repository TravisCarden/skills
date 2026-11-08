---
name: session-checkpoint
description: "Assess whether to continue, compact, or start fresh, whether a handoff is needed, and which model tier and effort fit the next work. Use for explicit session-transition questions, at task completion when a known next task or context problem warrants transition advice, or before work that needs a material session or model decision."
user-invocable: true
---

# Session Checkpoint

Assess whether the remaining work needs a session, model, or effort change. Base the assessment on the conversation and the runtime context, not on a fresh audit of the repository or environment. Repository rules and required verification stay in force.

## Continue or pause

Continue authorized work in the same turn unless a user decision would materially affect total cost or the reliability of the outcome. Pause before the affected work for a worthwhile cost reduction, an important capability or effort change, or a needed session transition. Explain the concrete benefit or risk; marginal optimizations do not justify an interruption. Completing a phase is not completing the user's request.

An explicit assessment-only request ends with the assessment. Do not start work the user has not authorized. Internal checks stay silent while work continues unless a user decision is needed.

After completing the requested work, append a checkpoint to the normal final response only when a known next task or a concrete context problem warrants a material recommendation. Otherwise finish without checkpoint boilerplate or asking for another task. The checkpoint follows a justified stop; it does not create one.

The user performs transitions. Do not change settings, compact, open a session, or write a handoff on the user's behalf.

## Decision

- **Continue** when the current context still helps the remaining work. Session length alone does not justify a reset.
- **Compact** when context space is constrained and a short summary can carry the reliable state forward.
- **Start fresh** when stale assumptions, unrelated work, or accumulated corrections make continuation less reliable. Warning signs: repeated missed constraints, contradictions, circular investigation, and ignored evidence. Do not compact before starting fresh.
- On pushback, change the verdict only for a new fact or a flaw in your reasoning that you can name.

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

For an explicit checkpoint with no material decision, give one short line saying to continue with the current model and effort. Use "keep current" for unknown settings; mark "(no change)" only when both settings are known. Then continue authorized work, unless the user asked for assessment only.

For a material decision, use short bold-labeled bullets in this order, addressed to the user. For an internal check at task completion, append them after the normal final response; otherwise give the checkpoint directly. The checkpoint itself needs no preamble, table, or closing paragraph.

- **Session:** continue, compact, or start fresh
- **Handoff:** no or recommended
- **Model / effort:** concrete mapped model / effort, or selection needed with the target tier if unresolved; mark "(no change)" only if both match the known current settings
- **Reason:** one short sentence with the concrete benefit or risk that warrants the recommendation
- **Do next:** direct actions for the user, including prerequisites. Keep a single action inline. Put distinct actions on separate numbered lines, including changing settings and resuming, compacting and resuming, or creating a handoff and opening a session. For a handoff, identify only the state missing from existing records.

If the next task is unknown, keep current settings. For an explicit checkpoint, ask only for the missing task rather than forcing a transition report. For an internal check, omit the checkpoint unless a concrete context problem warrants a transition recommendation; do not invent the next task.

For start fresh, add **Starter prompt** in a fenced `text` block: one short paragraph with the goal, the records to read, and the exact next action. If a handoff is recommended, tell the user to create it first, and have the prompt refer to "the handoff I supply," not an invented path. The prompt must paste unchanged and must not imply that unverified work passed.
