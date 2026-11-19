# session-checkpoint

Decide whether the remaining work warrants a different session, model, or effort level—without turning every checkpoint into a stop.

## When to use it

Use this when you want to reassess context quality, model capability, or total cost, including when moving to a new task in the same subsystem. The model can also invoke it when new evidence invalidates a recommendation or exposes unreliable assumptions, before work that needs a material decision, or at task completion when transition advice is useful. Session length alone is not a reason to reset. A brief easier step is not a reason to downgrade and immediately switch back.

## Install

```sh
npx skills add https://github.com/TravisCarden/skills --skill session-checkpoint
```

## Use

Invoke the skill by itself for an assessment, even if previously authorized work remains. Include the remaining task and any known settings or constraints when they are not already in the conversation:

```text
/session-checkpoint Context is accurate. The design is settled, and a large batch of specified mechanical edits and verification remains. Is a model or effort change worth it? Assess only.
```

To assess and act, ask in ordinary language. No special argument or exact phrase is required. For example, after receiving a proposed plan:

```text
Approved. /session-checkpoint and proceed.
```

"Proceed according to session-checkpoint," "check first, then go ahead," and "quick checkpoint, then finish the work" express the same intent. Approval alone, followed by the invocation, is still assessment-only. "Assess only" or "don't start yet" keeps the invocation assessment-only even if the request also mentions proceeding.

When action is requested and no material decision is needed, the agent briefly reports the continue decision, current model, and effort, then performs the next authorized action in the same turn without waiting for another reply. You can request no report when continuing. A direct request to do a task or go ahead with a clear proposed plan can authorize it; invoking the skill alone does not. If the task or permission is unclear, the agent asks for the missing information.

A worthwhile net saving, risky work that needs stronger judgment, or a needed session transition can justify a pause before affected work even when you ask to proceed. A reliability break triggers reassessment, not an automatic stop: a recoverable mistake or completed slice does not justify ending an execution turn. Internal assessments that choose continue perform the next authorized action in that same turn rather than promising to resume or asking for permission again.

At task completion, the agent appends a checkpoint to its normal final response only when a known next task or a concrete context problem warrants a material recommendation. Otherwise it finishes without checkpoint boilerplate or asking what to do next. Internal checks stay silent while work continues unless a decision is needed.

## What it decides

- **Session:** continue with useful context, compact reliable state under context pressure (or drop stale tool output when the harness offers it), or start fresh when recurring stale assumptions make continuation less reliable. A handoff appears here only when existing records and a short starter prompt cannot carry the essential state. Fresh-session recommendations include a paste-ready starter prompt.
- **Model and effort:** choose each for the remaining work. Total cost includes retries, switching, rebuilding context, lost prompt-cache savings, and likely later upgrades.

The agent does not change settings, compact, open a session, or create a handoff for you. It does not invent current settings, availability, or measured savings.

For bulky disposable output under context pressure, the agent prefers pruning without summarizing. It names a command only when runtime context, loaded instructions, the user, or a directly exposed agent-callable registry establishes its availability and behavior. It does not search configuration or assume an extension API is an agent tool. Essential evidence must remain available; pruning is not a remedy for recurring stale assumptions. If pruning is unavailable or insufficient, compact instead.

## Model mapping overrides

`session-checkpoint` includes a [default model mapping](model-mapping.md). To use other models or vendors, add a short block to your existing global or project `AGENTS.md`, `CLAUDE.md`, or equivalent instructions that your agent already loads. You can also put the mapping directly in a checkpoint request. No new file or changes to the installed skill are needed.

For example, replace the values below with exact model names and vendors you can use:

```text
For session-checkpoint, replace the default model mapping with:
- Strongest: unavailable
- Strong: <vendor and exact model name for difficult diagnosis and risky decisions>
- Standard: <vendor and exact model name for bounded implementation>
- Light: <vendor and exact model name for mechanical work>
```

Use any vendors, and list alternatives when useful. The tier definitions are in [the skill](SKILL.md#model-and-effort). Plain text is enough; no parser or special syntax is required.

A user mapping takes precedence over a runtime tier mapping, which takes precedence over the bundled defaults, subject to your agent's instruction rules. Each supplied mapping replaces the defaults completely, not just matching rows. Omitted or unavailable tiers stay unmapped. The skill uses a concrete model name for a change recommendation; if a material change needs an unmapped tier, it asks which available model to use. Missing mappings do not interrupt work that the current setup can reliably finish.

The mapping is a recommendation guide, not model detection, an availability check, or a promise of equal capabilities or prices. Keep your overrides current as your access and model choices change.

## Read more

- [Skill instructions](SKILL.md)
- [Default model mapping](model-mapping.md)
- [37 real example responses](examples.md), including pruning availability, essential evidence, exposed registries, completion-time advice, silent internal checks, cache costs, and model mapping overrides

The examples ran without tools. They demonstrate decision and output behavior, not measured savings, command routing, or live-session tool execution.
