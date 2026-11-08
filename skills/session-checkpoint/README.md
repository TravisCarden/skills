# session-checkpoint

Decide whether the remaining work warrants a different session, model, or effort level—without turning every checkpoint into a stop.

## When to use it

Use this when you want to reassess context quality, model capability, or total cost, including when moving to a new task in the same subsystem. The model can also invoke it before work that needs a material decision or at task completion when transition advice is useful. Session length alone is not a reason to reset. A brief easier step is not a reason to downgrade and immediately switch back.

## Use

Invoke the skill manually with the remaining task and any known settings or constraints:

```text
/session-checkpoint Context is accurate. The design is settled, and a large batch of specified mechanical edits and verification remains. Is a model or effort change worth it? Assess only.
```

Without an assessment-only request, the agent continues work you have already authorized unless your decision would materially affect cost or outcome. A worthwhile net saving, risky work that needs stronger judgment, or a needed session transition can justify a pause. Completing a phase does not justify stopping while requested work remains.

At task completion, the agent appends a checkpoint to its normal final response only when a known next task or a concrete context problem warrants a material recommendation. Otherwise it finishes without checkpoint boilerplate or asking what to do next. Internal checks stay silent while work continues unless a decision is needed.

## What it decides

- **Session:** continue with useful context, compact reliable state under context pressure, or start fresh when recurring stale assumptions make continuation less reliable.
- **Model and effort:** choose each for the remaining work. Total cost includes retries, switching, rebuilding context, lost prompt-cache savings, and likely later upgrades.
- **Handoff:** recommend one only when existing records and a short starter prompt cannot carry the essential state. Fresh-session recommendations include a paste-ready starter prompt.

The agent does not change settings, compact, open a session, or create a handoff for you. It does not invent current settings, availability, or measured savings.

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
- [25 real example responses](examples.md), including completion-time advice, silent internal checks, same-subsystem task changes, cache costs, and model mapping overrides

The examples ran without tools. They demonstrate decision and output behavior, not measured savings or live-session performance.
