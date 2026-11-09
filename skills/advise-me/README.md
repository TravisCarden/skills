# advise-me

Get one recommendation for a technical or business decision, including whether you are solving the right problem.

## When to use it

Use this when you have a proposed solution, a decision you are stuck on, or an outcome without a clear plan. It looks for ways to remove the need rather than add machinery to meet it. If your plan is already sound, it should agree.

For a focused choice between reuse, a dependency, and custom code, see [build-vs-buy](../build-vs-buy/README.md).

## Install

```sh
npx skills add https://github.com/TravisCarden/skills --skill advise-me
```

## Use

Invoke the skill manually with your question and relevant constraints:

```text
/advise-me Our support team spends hours on password-reset tickets. Should we hire another support engineer?
```

Expect the verdict first. If the request targets the wrong problem, the response states the real objective before the verdict. Small decisions get a first step; larger ones also cover rejected options, what would change the recommendation, and the cheapest way to check key assumptions.

The skill can read available code, data, and documentation before advising. It should ask for a deciding fact when one is missing, not invent certainty or objections. Pushback alone should not change its recommendation.

## Read more

- [Skill instructions](SKILL.md)
- [13 real example responses](examples.md), including reframed questions, sound plans, missing facts, and follow-up pushback

The examples ran without tools. Their technical claims are unverified; they show response behavior, not proof that a recommendation is correct.
