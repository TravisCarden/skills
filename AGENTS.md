# AGENTS.md

This repo is a collection of agent skills written in Markdown. It has no code, build, or test suite. Each skill is evaluated by running it against a model and having the owner judge the outputs.

## Layout

- `skills/<name>/SKILL.md` is the skill. `<name>` must match the frontmatter `name`, which may use only lowercase letters, numbers, and hyphens (max 64 characters).
- `skills/<name>/examples.md` (optional) holds real outputs for people to read. **Never reference it from `SKILL.md`.** If it is referenced, agents load it and copy its wording and structure.
- `README.md`: the "My Skills" table has a row for every skill. The Resources column links to supporting files such as `examples.md`. Update the table when you add a skill or a resource.

## Writing skills

- Frontmatter follows the existing skills: `name`, `description`, `disable-model-invocation: true`, `user-invocable: true`. Descriptions start with "Manually invoked …" and say what the skill does and when to use it. Max 1,024 characters, no XML tags.
- Write plainly: short sentences, no persona ("You are a ruthless …"), and no emphasis words ("CRITICAL", "mercilessly"). Persona and emphasis change the tone of the output, not how rigorous it is.
- Do not depend on other skills being installed. Users may install just one skill. If you mention another skill, give a fallback ("use X if available; otherwise …").
- Shorter is better, as long as every line changes the output. Test: remove a line and rerun; if nothing changes, delete it. Lines that only describe what the model already does are wasted.
- Avoid:
  - **Fixed counts** ("5–7 ideas", "2 pros and 2 cons"). They produce filler and options invented just to be rejected.
  - **Required disagreement.** Allow "the plan is sound".
  - **Quoted templates** like "Do A. If B, do C instead." Models copy them word for word.
  - **Visible scratchpads.**
- Put the conclusion first in the output format. Models follow the order of the output template literally. A verdict listed fifth ends up halfway down the response.

## Evaluating a skill

This is how `advise-me` was built. Use the same process for new skills and for major revisions.

1. **Critique the draft** against the failure modes in "Writing skills". Then run the skill on its own design, if it is the kind of skill that can do that.
2. **Build a test set of about 8–13 requests.** Include:
   - realistic requests from the target domain
   - a **control** where the user's plan is already right (the skill should agree)
   - a **trivial** question (the answer should stay short)
   - a request **missing one deciding fact** (the answer should be conditional and ask for that fact)
   - a **follow-up pushback** turn without new facts (the skill should keep its position)

   To test a second turn in a single call, summarize the first turn inside the request.
3. **Run each version against the same requests**, one call per request per version:
   - the plain model, with no skill
   - each skill version, with the body as the system prompt: `"The user invoked this skill. Follow it.\n\n" + <SKILL.md body>`

   Occasional unexplained refusals happen. Retry them.
4. **Collect cheap signals**, such as:
   - word counts
   - where the verdict appears in the response
   - grader-model yes/no questions ("commits to one recommendation?", "main answer outside the user's frame?", "raises forced objections?")

   These are only signals. The grader model is coarse, and self-grading is biased.
5. **Get a blind review from the owner.** Shuffle the versions for each case, label them A/B/C, and write `review.md` plus a separate `key.json` to `/tmp`, outside the repo. The owner picks the best response per case. Then unblind and tally. The owner's picks decide.
6. **Test a stripped-down version.** Compare a version with only the essential rules against the full one. For `advise-me`, the owner preferred the roughly 10-line version 9.5–3.5, so it became the shipped skill.
7. **Fix what the review found and rerun.** For `advise-me`, the review found buried verdicts and a quoted template copied word for word. After the fixes, all 13 responses led with the verdict.

Findings worth reusing:
- Strong models already catch wrong premises without a skill.
- The measurable gains came from output rules: commit to a verdict, keep the position unless there is a new fact, do not invent objections, and keep it short.
- Without a skill, models answer the question asked and mention the better option only in passing. With the skill, the better option became the verdict.

## Examples files

Write `examples.md` only from the final `SKILL.md`, using a fresh run per request. Do not reuse outputs from older versions.

- **Content:** responses stay unedited. Group the cases by the behavior they test, with a one-line italic description per group.
- **Header:** state the model and date, that the skill ran without tools, that the responses' technical claims are unverified, and that `SKILL.md` does not load the file.
- **Contents:** a table of contents with GitHub-style anchors, so headings must be unique.
- **Separators:** a `---` rule before each section and between examples.
- **Request:** a plain line, ``**Request:** `/<name>` <request>``.
- **Response:** in a blockquote. Change headings inside responses to `####` so they don't break the document's outline. Before doing that, check that no code block has a `#` line at column 0, which a blanket heading rewrite would alter.

## Commits

Use short imperative subjects in the style of `git log`: "Add advise-me skill", "Add README".
