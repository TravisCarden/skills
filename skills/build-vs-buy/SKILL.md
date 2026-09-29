---
name: build-vs-buy
description: "Manually invoked review for a significant decision: reuse existing code, use a platform capability, adopt a dependency or service, build custom code, or a mix."
disable-model-invocation: true
user-invocable: true
---

# Build vs Buy

**Goal: the least complex, most maintainable solution that meets the real requirement over its expected lifetime.**

Complexity means what the project must own and understand later: code, dependencies, concepts, and moving parts. Fewer lines is not the measure.

This skill guides a decision. It does not authorize new dependencies, services, migrations, spikes, or scope changes. Do not change the requirement.

## 1. Understand first

- Read the affected code, conventions, dependency manifest, and tests.
- State the outcome, constraints, and a testable success check. Separate demonstrated needs from guesses about the future.
- Name what matters most here (for example security, correctness, reliability, cost, reversibility).
- Existing code is evidence, not proof of a good pattern. Say so if the local pattern is the problem.

## 2. Consider options in this order

1. **Reuse locally:** existing code, fixed or extended if needed.
2. **Built-ins:** standard library, framework, runtime, database, or platform.
3. **Existing dependency:** one the project already carries, used as intended.
4. **New proven solution:** a maintained library, tool, or service.
5. **Build:** small, clear, project-specific code.

Pick the option that meets the requirement with the least owned complexity. Do not stop at a rung that only nearly fits.

**Tie-breakers, in order** (when options both meet the requirement):
1. Less code, fewer dependencies, fewer concepts for the project to own.
2. Easier to reverse or remove.
3. More conventional for this codebase and ecosystem.

**Exceptions:** For hard, standardized problems (cryptography, authentication protocols, complex formats), use a vetted implementation, not hand-written code. A **hybrid** is often best: reuse the hard general part, own the small project-specific part.

## 3. Depth

**Quick check** (small, reversible change): compare the options in a few sentences, then proceed.

**Deep check** when any of these apply: a new dependency or service, replacing an established component, auth/crypto/security/privacy/payments, data migration, or a choice that is costly to reverse.

For a deep check:
- Use the `research` skill (https://www.skills.sh/mattpocock/skills/research) if available. If it is not installed, research directly.
- Use first-party docs, source, and release history. Test claims against the *actual required behavior*, not the name of a function or marketing.
- Check: does it fully do the job; license compatibility with this project; maintenance and abandonment risk; transitive dependencies and vulnerabilities; lock-in and replacement cost.
- Compare the best reuse option, a focused custom build, and the status quo if replacing something. Do not invent options to fill a matrix.
- If a fact cannot be verified, mark it **unverified**. Prefer the more reversible option, or defer and say what evidence would decide it.

## 4. Boundary

- The project owns its distinct behavior, contracts, and policy. Reuse mature code for generic complexity.
- No wrappers, interfaces, config, or fallbacks without a concrete need today. A hypothetical future vendor switch is not one.
- Never trade away required safety, data integrity, accessibility, security, or error handling for simplicity.
- A recommendation is not adoption. Do not quietly add a dependency or commit to a service.

## 5. Adversarial review (deep check only)

Before recommending, get an independent challenge. If the harness supports subagents, spawn one with only the need, the options, the evidence, and your leading choice. Do not share your reasoning. Ask it to argue for the strongest rejected option and to find the weakest claim in the evidence. If subagents are not available, write the best case for the runner-up yourself.

Check the challenge against these failure modes: owning a hard problem for no good reason; a dependency that costs more than the problem; foreign concepts pushed into core logic; breakage on bad input, outage, upgrade, or abandonment; anything added that the requirement doesn't need.

If the challenge holds up, change the decision. Report the strongest surviving objection in the output under Consequences. Then verify with the smallest meaningful test, including a failure path.

## Output

**Ordinary change:** mention only a non-obvious trade-off or rejected option. No memo.

**Deep check:** follow the repo's decision-record convention if it has one. Otherwise:

- **Need:** requirement and success check.
- **Options:** credible paths, key findings, unknowns.
- **Choice:** proposed, adopted, or deferred; what the project owns versus delegates.
- **Consequences:** main trade-off, maintenance cost, how to replace it.
- **Revisit when:** the specific condition that reopens this.

Example:
- **Need:** Parse RFC 3339 dates from webhooks; must reject malformed input.
- **Options:** `Date.parse` (accepts non-RFC formats); existing `dayjs` (strict mode fits); new library (no gain).
- **Choice:** Recommended: `dayjs` strict parse. Project owns the validation error mapping.
- **Consequences:** No new dependency. Replaceable behind one function.
- **Revisit when:** `dayjs` is dropped from the project.

Link supporting code, tests, and docs. Never claim verification or adoption that has not happened.
