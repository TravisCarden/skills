---
name: session-checkpoint
description: "Manually invoked checkpoint before continuing work: assess whether to stay, compact, or start fresh, whether a handoff is needed, and which model and effort fit the next task."
disable-model-invocation: true
user-invocable: true
---

# Session Checkpoint

Recommend the least costly reliable way to do the next bounded task. Use the conversation and available session information, not a repository audit. Assess only: do not compact, switch settings, write a handoff, or start work. Repository rules and required verification still apply.

- **Continue** when current context helps. Session length alone does not justify a reset.
- **Compact** when context space is constrained and a concise summary can preserve useful, reliable state.
- **Start fresh** when stale assumptions, unrelated work, or accumulated corrections make continuation less reliable. Repeated missed constraints, contradictions, circular investigation, and ignored evidence are warning signs, not measured degradation or context thresholds. Do not compact before starting fresh.
- Choose model and effort separately for the next task, not the hardest earlier task. Count retries, rework, switching, and context rebuilding as costs. Keep an adequate current configuration unless a concrete capability or cost benefit justifies changing it; a brief tool command alone does not justify a downgrade. A model or effort change alone does not require a fresh session.
- Use known model settings when supplied; otherwise use the recommendation labels below. Do not research catalogs or invent availability, context measurements, or savings.
- On pushback, change the recommendation only for a new fact or a flaw in your reasoning that you can name.

## Model ladder

Use these user-defined tiers, strongest to lightest:

| Tier | Anthropic label | OpenAI label |
|---|---|---|
| Maximum | Fable | Astra |
| Strong | Opus | Sol |
| Standard | Sonnet | Terra |
| Light | Haiku | Luna |

Choose the least costly reliable tier: Light for mechanical edits, Standard for bounded coding, Strong for difficult diagnosis or ambiguous design. Maximum needs a concrete reason Strong is insufficient. Assess low, medium, or high effort separately. Prefer the current vendor when known; otherwise give paired labels, such as Sonnet or Terra / medium. These are recommendations, not claims that vendors expose identical settings.

## Preserve transition state

Essential state is the goal, unfinished changes, decisions, verification results, blockers, and exact next action. Prefer existing records. Recommend a handoff only when those records plus a short starter prompt cannot preserve essential state. A multi-paragraph prompt or nested checklist is a signal to use a handoff, not a reason to copy the transcript.

The user decides how to create the handoff; never create it or imply it exists. In Do next, identify only the missing state it must preserve, rather than repeating a full checklist. Keep unverified work clearly marked.

## Output

Use short, bold-labeled bullets in this order. Address the user directly; use no preamble, table, color markup, or concluding paragraph.

- **Session:** continue, compact, or start fresh
- **Handoff:** no or recommended
- **Model / effort:** model label(s) / effort; append “(no change)” only when both match the known current settings
- **Reason:** optional, one short sentence with concrete evidence behind a transition or model change
- **Do next:** direct actions or a deciding question, including prerequisites; no explanation

Omit Reason for ordinary continuation with unchanged settings, missing-task questions, and pushback without new facts. Put all actionable conditions in the decision or Do next bullets, never in Reason. If the next task is unspecified, retain the current model / effort and ask for the task under Do next; if current settings are also unspecified, say “keep current” rather than inventing them. Do not add availability disclaimers.

Keep one action inline. Split distinct actions into numbered substeps rather than joining them with “then” or semicolons. This includes compacting and resuming, creating a handoff and opening a session, or changing settings and starting work. Avoid repeating instructions from the starter prompt in Do next.

For start fresh, follow the bullets with **Starter prompt** and a fenced `text` block. Keep it to one short paragraph with the goal, relevant existing records or essential missing context, and exact next action. When a handoff is recommended, tell the user to create it before the transition and supply it in the new session; the prompt should refer to “the handoff I supply,” not an invented path or a placeholder. The prompt must be ready to paste unchanged and must not imply verification has passed.
