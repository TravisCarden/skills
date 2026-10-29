# build-vs-buy examples

Real responses from the [`build-vs-buy`](SKILL.md) skill, unedited. Each one was produced from a single run on the current `SKILL.md`, using the default Claude model in the author's agent harness (October 2026). The skill was loaded as the system prompt with no tools. It is designed to read the project's code and, for deep checks, research sources and get an independent review; none of that was possible here, so the responses say what they would confirm and mark claims they could not check.

Technical details in the responses come from the model and have not been verified. Treat them as examples of how the skill reasons and formats its answer, not as engineering advice.

This file is for people. `SKILL.md` does not reference it, so agents do not load it.

## Contents

- [Built-ins first](#built-ins-first)
  - ["5 minutes ago" timestamps in React](#5-minutes-ago-timestamps-in-react)
  - [Parsing key=value lines in Go](#parsing-keyvalue-lines-in-go)
- [Reuse what the project already has](#reuse-what-the-project-already-has)
  - [Posting to a Slack webhook from Drupal](#posting-to-a-slack-webhook-from-drupal)
  - [Backoff and jitter for an SQS consumer](#backoff-and-jitter-for-an-sqs-consumer)
  - [Zod for backend validation](#zod-for-backend-validation)
- [Hard, standardized problems](#hard-standardized-problems)
  - [Verifying JWTs in Node](#verifying-jwts-in-node)
  - [Expiring download links for files in S3](#expiring-download-links-for-files-in-s3)
- [No wrappers for hypothetical futures](#no-wrappers-for-hypothetical-futures)
  - [A PaymentProvider interface around Stripe](#a-paymentprovider-interface-around-stripe)
- [Adopting a proven module](#adopting-a-proven-module)
  - [3,000 redirects for a Drupal migration](#3000-redirects-for-a-drupal-migration)
- [Deep checks](#deep-checks)
  - [PDF invoices from Drupal](#pdf-invoices-from-drupal)
  - [Feature flags for a Rails app](#feature-flags-for-a-rails-app)
  - [Replacing Elasticsearch with Algolia](#replacing-elasticsearch-with-algolia)

---

## Built-ins first

_The standard library or platform already does the job, so no dependency is added._

### "5 minutes ago" timestamps in React

**Request:** `/build-vs-buy` Our React app needs to show timestamps like '5 minutes ago' and 'in 2 days'. Should I add date-fns or Day.js for this?

**Response:**

> **Probably neither.** The browser already does this with `Intl.RelativeTimeFormat`. I haven't seen your codebase, so check two things first.
>
> #### Check first
>
> 1. **Is `date-fns` or `dayjs` already in `package.json`?** If so, use it. `formatDistanceToNow(date, { addSuffix: true })` in date-fns, or Day.js's `relativeTime` plugin. Don't add a second date library.
> 2. **Is there already a relative-time helper in the code?** Search for `ago`, `fromNow`, or `RelativeTimeFormat`. If there is one, reuse or fix it.
>
> #### If neither exists, use the built-in
>
> `Intl.RelativeTimeFormat` produces "5 minutes ago" and "in 2 days", localized, with no dependency. It works in all modern browsers. The only code you own is choosing the unit, which takes about 15 lines:
>
> ```ts
> // src/lib/relativeTime.ts
> const UNITS: [Intl.RelativeTimeFormatUnit, number][] = [
>   ["year", 31_536_000],
>   ["month", 2_592_000],
>   ["week", 604_800],
>   ["day", 86_400],
>   ["hour", 3_600],
>   ["minute", 60],
>   ["second", 1],
> ];
>
> export function formatRelative(
>   date: Date | number,
>   now: number = Date.now(),
>   locale?: string
> ): string {
>   const t = typeof date === "number" ? date : date.getTime();
>   if (!Number.isFinite(t)) throw new RangeError("Invalid date");
>   const diffSec = (t - now) / 1000;
>   const rtf = new Intl.RelativeTimeFormat(locale, { numeric: "auto" });
>   for (const [unit, secs] of UNITS) {
>     if (Math.abs(diffSec) >= secs || unit === "second") {
>       return rtf.format(Math.round(diffSec / secs), unit);
>     }
>   }
>   return rtf.format(0, "second");
> }
> ```
>
> `numeric: "auto"` gives "yesterday" and "now" instead of "1 day ago". Use `"always"` if you want the numeric form every time.
>
> Minimal tests, including a failure path:
>
> ```ts
> const now = Date.UTC(2024, 0, 10, 12);
> expect(formatRelative(now - 5 * 60_000, now, "en")).toBe("5 minutes ago");
> expect(formatRelative(now + 2 * 86_400_000, now, "en")).toBe("in 2 days");
> expect(() => formatRelative(new Date("garbage"), now)).toThrow(RangeError);
> ```
>
> #### Trade-offs
>
> - **Rounding vs. a library's thresholds.** date-fns uses fuzzy wording like "about 1 hour" and "almost 2 years", with tuned cutoffs. If design needs that exact wording, date-fns is a reasonable new dependency. Otherwise you'd be adding a library for a loop.
> - **Month and year are approximate** (30 and 365 days). That's fine for "ago" labels but not for calendar math. If you need real date arithmetic elsewhere, that is a separate reason to adopt date-fns.
> - **Live updating isn't included either way.** "5 minutes ago" goes stale unless a component re-renders, for example with a `useEffect` interval. Libraries don't solve this for you.
> - **Easy to reverse.** Everything sits behind one function, so you can swap in a library later without touching call sites.
>
> **Revisit when:** you need fuzzy wording, calendar arithmetic, or timezone handling beyond display. At that point date-fns (tree-shakable, functional) is the safer default over Day.js. If you only want the smallest possible footprint, choose Day.js.

---

### Parsing key=value lines in Go

**Request:** `/build-vs-buy` Our Go CLI needs to read the output of an internal tool: one `key=value` pair per line, no quoting, no escapes. Should I pull in a parsing library?

**Response:**

> **No, the standard library already covers this.** It's a small, reversible change, so a quick check is enough. I haven't seen your repo yet, so first confirm there isn't already a helper for this format, or an existing dependency that parses it. If there is, reuse it.
>
> **Options considered**
> 1. **Local reuse:** search for an existing parser (`strings.Cut`, `SplitN(..., "=", 2)` near "key=value"). If one exists, extend it.
> 2. **Built-ins:** `bufio.Scanner` plus `strings.Cut` handles the whole format in about 20 lines.
> 3. **Existing dependency:** an INI or dotenv library would bring in quoting, sections, and escape rules your format doesn't have. That means accepting input the tool never produces.
> 4. **New library:** nothing to gain. It adds a dependency, license, and upgrade burden for a trivial grammar.
>
> **Recommendation:** build it with the stdlib. Your CLI owns the format's policy: what counts as malformed and how errors are reported.
>
> ```go
> func parseKV(r io.Reader) (map[string]string, error) {
> 	out := make(map[string]string)
> 	sc := bufio.NewScanner(r)
> 	line := 0
> 	for sc.Scan() {
> 		line++
> 		text := strings.TrimSuffix(sc.Text(), "\r")
> 		if text == "" {
> 			continue
> 		}
> 		k, v, ok := strings.Cut(text, "=")
> 		if !ok || k == "" {
> 			return nil, fmt.Errorf("line %d: malformed pair %q", line, text)
> 		}
> 		if _, dup := out[k]; dup {
> 			return nil, fmt.Errorf("line %d: duplicate key %q", line, k)
> 		}
> 		out[k] = v
> 	}
> 	if err := sc.Err(); err != nil {
> 		return nil, err
> 	}
> 	return out, nil
> }
> ```
>
> **Decisions to confirm against the tool's actual output:**
> - **Values containing `=`:** `Cut` splits on the first `=`, so `a=b=c` gives the value `b=c`. Check that keys can never contain `=`.
> - **Blank lines and CRLF:** both are tolerated above. Make them errors if the tool never emits them.
> - **Duplicate keys:** the code rejects them. Use last-wins instead if the tool may repeat keys intentionally.
> - **Whitespace around keys and values:** the code keeps it. Don't trim it unless the tool adds it.
> - **Line length:** `Scanner` fails on lines over 64 KB. Call `sc.Buffer` if values can be that large.
>
> **Verify** with a table test covering a normal input, a value containing `=`, a line with no `=`, an empty key, and a duplicate key. The error cases are the ones that matter.
>
> **Revisit when** the tool's format gains quoting, escapes, or multiline values. At that point a vetted parser for whatever format it adopts becomes worth it.

---

## Reuse what the project already has

_Existing code or an existing dependency covers the need. The last case is a control: the plan is already right._

### Posting to a Slack webhook from Drupal

**Request:** `/build-vs-buy` Our Drupal 10 site needs to POST a message to a Slack incoming webhook whenever a node is published. Should I add a Slack PHP SDK with Composer?

**Response:**

> **Short answer: no.** A Slack incoming webhook is a single HTTPS POST with a JSON body. Drupal 10 core already includes Guzzle through the `http_client` service, so a Slack SDK would add a dependency and new concepts without giving you anything you need.
>
> I haven't seen your codebase. If you already have a custom module for site hooks, or a Slack module installed, put this there instead of creating a new one.
>
> #### Decision
>
> - **Need:** When a node becomes published (on create, or on a transition from unpublished to published), POST a message to a Slack incoming webhook. Saving the node must still succeed if Slack is down. **Success check:** publishing a node sends exactly one message, re-saving an already-published node sends none, and a Slack failure is logged without breaking the save.
> - **Options:**
>   1. **Core `http_client` (Guzzle) in a small custom hook.** Already installed and fully covers the need.
>   2. **Contrib `slack` module.** Adds a module and configuration UI. Its maintenance status and Drupal 10 support are **unverified**. It would only be worth it if non-developers need to manage several channels or messages.
>   3. **Slack PHP SDK via Composer.** SDKs are aimed at the Web API (OAuth, many endpoints), and incoming webhooks don't need that. Maintenance status is **unverified**. You'd gain a dependency and its transitive packages for one POST.
> - **Choice (recommended, not adopted):** Option 1. Your project owns the publish-detection logic and the message format. Guzzle handles HTTP.
> - **Consequences:** No new dependency, and about 30 lines of code to own. Messages are sent in the request without retries, so a Slack outage means that message is lost (logged). To replace or remove it, delete one hook.
> - **Revisit when:** you need guaranteed delivery (move the sending to a `QueueWorker`), Slack features beyond simple posting (threads, interactive messages, Web API), or editor-managed channels (look at the contrib module then).
>
> #### Sketch
>
> Keep the webhook URL out of config exports, because it's a secret. In `settings.php` (or from an environment variable):
>
> ```php
> $settings['slack_webhook_url'] = getenv('SLACK_WEBHOOK_URL');
> ```
>
> `mymodule.module`:
>
> ```php
> use Drupal\Core\Site\Settings;
> use Drupal\node\NodeInterface;
>
> function mymodule_node_insert(NodeInterface $node) {
>   if ($node->isPublished()) {
>     _mymodule_notify_slack($node);
>   }
> }
>
> function mymodule_node_update(NodeInterface $node) {
>   // D10: $node->original. On 11.2+ use $node->getOriginal().
>   $original = $node->original ?? NULL;
>   if ($node->isPublished() && $original && !$original->isPublished()) {
>     _mymodule_notify_slack($node);
>   }
> }
>
> function _mymodule_notify_slack(NodeInterface $node): void {
>   $url = Settings::get('slack_webhook_url');
>   if (!$url) {
>     return;
>   }
>   try {
>     \Drupal::httpClient()->post($url, [
>       'json' => [
>         'text' => sprintf('Published: <%s|%s>',
>           $node->toUrl('canonical', ['absolute' => TRUE])->toString(),
>           $node->label()),
>       ],
>       'timeout' => 5,
>     ]);
>   }
>   catch (\Throwable $e) {
>     \Drupal::logger('mymodule')->error('Slack notify failed: @m', ['@m' => $e->getMessage()]);
>   }
> }
> ```
>
> #### Strongest objection to this
>
> The SDK or contrib module isn't really "free" to skip. Sending inline means a slow Slack adds up to 5 seconds to editors' saves, and failures are only logged. That's a fair point, but neither the SDK nor the contrib module solves it by itself. Moving the send to a core Queue API worker does. Do that only if lost notifications actually matter to you.
>
> #### Verify
>
> - Publish a new node: one message arrives.
> - Save an unpublished node, then publish it: one message arrives.
> - Re-save the published node: no message.
> - Set the URL to an invalid host: the save still succeeds and an error is logged.
>
> These can be kernel tests with a mocked `http_client`.

---

### Backoff and jitter for an SQS consumer

**Request:** `/build-vs-buy` Our Python service has a homegrown retry decorator in utils/retry.py (fixed delay, max attempts) that we use for HTTP calls. Now our SQS consumer needs retries with exponential backoff and jitter. Should I add tenacity?

**Response:**

> #### Build vs Buy: Retry with Backoff for the SQS Consumer
>
> **Short answer: probably not.** First decide which failure you're retrying. For an SQS consumer, the right tool is often SQS or botocore, not a retry library. If you do need in-process backoff, extending `utils/retry.py` is likely smaller than adding tenacity.
>
> I haven't seen your code, so the points below say what to confirm.
>
> #### 1. Clarify what's being retried
>
> "Retries in the SQS consumer" usually means one of three things, and each has a different best answer:
>
> | What fails | Best rung | Why |
> |---|---|---|
> | **AWS API calls** (`receive_message`, `delete_message`) | **Built-in:** botocore retry config | `Config(retries={"mode": "standard", "max_attempts": N})` already does exponential backoff with jitter. No code needed. |
> | **Processing a message** fails transiently | **Platform:** SQS visibility timeout + redrive policy / DLQ | Don't delete the message. It reappears after the visibility timeout. For backoff, call `ChangeMessageVisibility` with a delay based on `ApproximateReceiveCount`. After `maxReceiveCount` it goes to the DLQ. Retries survive crashes and deploys, and you get a poison-message path. |
> | **A downstream call inside processing** (HTTP, DB) needs a few quick retries | **Reuse locally:** extend `utils/retry.py` | Same need your HTTP calls already have. |
>
> **Watch for this trap:** a long in-process backoff loop can outlast the message's visibility timeout. SQS then redelivers the message while you're still retrying, so it gets processed twice. Keep in-process retry budgets well under the visibility timeout.
>
> #### 2. If you need in-process backoff: extend the decorator
>
> Exponential backoff with full jitter is a few lines on top of a fixed-delay decorator:
>
> ```python
> import random, time, functools
>
> def retry(max_attempts=3, delay=1.0, backoff=1.0, max_delay=None, jitter=False,
>           exceptions=(Exception,)):
>     def deco(fn):
>         @functools.wraps(fn)
>         def wrapper(*args, **kwargs):
>             for attempt in range(max_attempts):
>                 try:
>                     return fn(*args, **kwargs)
>                 except exceptions:
>                     if attempt == max_attempts - 1:
>                         raise
>                     wait = delay * (backoff ** attempt)
>                     if max_delay is not None:
>                         wait = min(wait, max_delay)
>                     if jitter:
>                         wait = random.uniform(0, wait)  # "full jitter"
>                     time.sleep(wait)
>         return wrapper
>     return deco
> ```
>
> The defaults (`backoff=1.0`, `jitter=False`) keep the current fixed-delay behaviour, so existing HTTP call sites don't change. Adapt the signature to whatever `utils/retry.py` actually takes.
>
> **Tests to add:**
> - Delays grow and are capped. Patch `time.sleep` and seed `random` or patch `random.uniform`.
> - The last exception is re-raised after `max_attempts`. This is the failure path.
> - Non-listed exceptions are not retried.
> - Existing callers behave exactly as before.
>
> #### 3. When tenacity is the right call
>
> Tenacity is a reasonable, widely used library. As far as I know it's Apache-2.0 with no runtime deps, but that's **unverified** here, so check PyPI and the repo before adopting. It earns its place if you need several of these:
>
> - Retrying on a *result* (e.g., HTTP 429/503 responses), not just exceptions
> - Async retries
> - Combined stop conditions, such as attempts *or* elapsed time
> - Hooks for logging or metrics around each attempt
> - Many call sites, with the homegrown decorator gaining parameters every quarter
>
> If that's where you're headed, replace `utils/retry.py` entirely rather than running two retry systems side by side.
>
> #### Decision record
>
> - **Need:** SQS consumer retries transient failures with exponential backoff and jitter, without duplicate processing. Success check: backoff grows, is capped and jittered, and exhausted retries end in re-raise or DLQ.
> - **Options:**
>   - botocore retry config, for AWS calls
>   - SQS visibility + redrive, for message-level retries
>   - Extend `utils/retry.py`, for downstream calls
>   - tenacity: capable, but a new dependency and a second retry concept
> - **Choice:** Recommended, not adopted. Use botocore and SQS for what they cover, and extend the local decorator for the rest. The project owns roughly 10 lines of backoff math and its tests. Platform behaviour is delegated to AWS.
> - **Consequences:** No new dependency, and existing call sites are unaffected. You maintain a small decorator. To swap in tenacity later, you change call sites mechanically.
>   - **Strongest objection:** retry logic is a classic "it's just a few lines" trap. Edge cases like result-based retry, async, and observability accumulate, and tenacity has already solved them. That objection holds *if* those needs are real today. Nothing in your description shows they are.
> - **Revisit when:** you need result-based or async retries, retry metrics or hooks, or the decorator grows beyond around three new options.
>
> If you share `utils/retry.py` and the consumer loop, I can check the visibility-timeout interaction and the decorator change against your actual code.

---

### Zod for backend validation

**Request:** `/build-vs-buy` Our Node backend needs to validate JSON request bodies. We already use Zod throughout our TypeScript frontend and share types in a monorepo package. I plan to use Zod on the backend too. Any reason not to?

**Response:**

> Zod on the backend is likely the right call. It's already a project dependency, your shared types already live in it, and one schema can serve as both the runtime validator and the TypeScript type on both sides of the wire. A new validation library would add a second schema language and a dependency with no clear gain. I haven't seen your code, so here are the cases that could change that, roughly by how often they matter.
>
> **Real reasons to reconsider**
>
> 1. **Your framework already validates.** If you're on **Fastify**, it validates with JSON Schema/Ajv built in and uses those schemas for serialization and OpenAPI. Running Zod alongside it means two systems. You can still use Zod with Fastify through `fastify-type-provider-zod`, which keeps one source of truth. Just choose that deliberately rather than layering Zod on top of Ajv. With Express, Hono, Koa or similar, Zod fits cleanly. (Hono and tRPC have first-class Zod support.)
> 2. **You need generated OpenAPI or JSON Schema for external consumers.** Zod can do this through `zod-to-openapi`, or natively in Zod 4 with `z.toJSONSchema`. Check that the output covers your schemas, especially transforms and refinements, which don't map cleanly to JSON Schema.
> 3. **Very high request throughput with large payloads.** Zod 3 is noticeably slower than compiled validators like Ajv or TypeBox. Zod 4 narrowed the gap a lot. For typical API traffic this doesn't matter. If you have a known hot path, measure it before switching anything.
>
> **Things to get right if you proceed**
>
> - **One Zod version across the monorepo.** Pin it at the workspace root. Mixing 3 and 4, or having duplicate installs, causes confusing type and `instanceof` failures. If you're still on 3, this is a good moment to plan the move to 4.
> - **Unknown keys:** `z.object` silently strips them by default. That's usually what you want on the server. Use `.strict()` where unexpected fields should be rejected, such as clients sending fields you think are ignored.
> - **Coercion:** JSON bodies arrive typed, but query strings and path params are strings. Use `z.coerce` there only, not on bodies, or you'll accept `"123"` where a number was required.
> - **Shared vs. server-only rules.** Keep the shared package to the wire contract. Put server-only checks (authorization, DB uniqueness, trusted defaults) in backend-side `.extend()`/`.refine()`. Never rely on validation that only the client runs.
> - **Error responses:** map `ZodError` to your API's error format in one place. Don't return raw issues blindly, because they can echo input and internal paths.
> - **Use `safeParse` at the boundary** and a single middleware or helper, so every route validates the same way.
>
> **Bottom line:** proceed with Zod unless you're on Fastify and want to stay native to JSON Schema, or you have a measured performance problem. Revisit if either of those becomes true.

---

## Hard, standardized problems

_Authentication and cryptography go to vetted implementations or platform features, not hand-written code._

### Verifying JWTs in Node

**Request:** `/build-vs-buy` Our Node API receives JWTs from our identity provider (RS256, keys published at a JWKS URL). Verifying them looks simple. Should I write the verification myself with Node's crypto module or use a library?

**Response:**

> **Short answer: use a vetted library.** Don't hand-write JWT verification with `node:crypto`. Auth falls under the skill's rule for hard, standardized problems: use a vetted implementation and own only the project-specific policy.
>
> I haven't seen your repo, so first check whether you already have a suitable option (step 2 below).
>
> #### Why "looks simple" is misleading
>
> The signature check itself is a few lines. The bugs show up in everything around it:
>
> - **Algorithm confusion.** You must pin `RS256` and never trust the token's `alg` header. Accepting `none` or `HS256` with the public key as the secret are well-known exploits.
> - **JWKS handling.** You need to:
>   - Select the key by `kid`.
>   - Convert the JWK to a `KeyObject`.
>   - Cache keys.
>   - Refetch on an unknown `kid` when keys rotate.
>   - Rate-limit refetches so attackers can't force them with junk `kid`s.
>   - Handle timeouts and outages of the JWKS endpoint.
> - **Claim validation.** You must check `iss`, `aud`, `exp`, and `nbf` with clock-skew tolerance, and reject missing claims. A valid signature on a token meant for another audience is still a reject.
> - **Parsing edge cases.** These include strict base64url, malformed segments, and oversized input.
>
> Each of these is a quiet security hole if done wrong, and you'd own all of them indefinitely.
>
> #### Options, in order
>
> 1. **Existing code or dependency.** Grep your `package.json` and code for any of these:
>    - `jose`
>    - `jsonwebtoken` with `jwks-rsa`
>    - `express-oauth2-jwt-bearer`
>    - `@fastify/jwt`
>    - `passport-jwt`
>    - your IdP's SDK
>
>    If one is present and does strict RS256 plus JWKS, use it.
> 2. **Built-ins.** `node:crypto` can verify RS256 and import JWKs. It provides none of the JWKS caching, rotation, or claim policy listed above, so it doesn't meet the requirement on its own.
> 3. **New library.** The strongest default is **`jose`** (panva/jose). It has zero dependencies and uses runtime crypto. `jwtVerify` handles algorithm pinning, `iss`/`aud`/`exp`/`nbf`, and clock tolerance, and `createRemoteJWKSet` handles caching and rotation. **Unverified here:** current version, maintenance status, and license (MIT as I recall). Confirm these on npm and GitHub before adding it.
> 4. **Build.** Only the glue: config values and mapping verified claims to your user/request context.
>
> #### Recommendation
>
> - **Need:** Verify IdP-issued RS256 JWTs against the JWKS URL. Reject wrong algorithm, issuer, audience, expired, not-yet-valid, unknown-key, and malformed tokens.
> - **Choice (proposed, not adopted):** Use an existing suitable dependency if you have one. Otherwise use `jose` with `createRemoteJWKSet` and `jwtVerify(token, JWKS, { algorithms: ['RS256'], issuer, audience, clockTolerance })`.
> - **What you own:** the issuer and audience config, the claims-to-identity mapping, and the error-to-401 mapping.
> - **Consequences:**
>   - You get one small, focused dependency.
>   - The library is replaceable because it sits behind a single `verifyToken()` function. That function exists for a concrete reason (one call site for middleware), not as speculative abstraction.
>   - **Strongest objection to this choice:** a dependency in your auth path is supply-chain risk. This is mitigated by `jose` having no transitive dependencies and by pinning and auditing its version. Hand-rolled code carries higher risk.
> - **Revisit when:**
>   - Your IdP provides an official verifier you're required to use.
>   - The library becomes unmaintained.
>   - You move to a gateway that verifies tokens before they reach the API.
>
> #### Verify with tests
>
> Include failure paths:
>
> - valid token
> - expired token
> - wrong `aud`
> - wrong `iss`
> - `alg: none`
> - HS256 signed with the public key
> - unknown `kid`
> - tampered payload
> - JWKS endpoint down
>
> You can generate test keys and serve a local JWKS for these.
>
> If you share your `package.json` and framework, I can tell you which rung you're on and sketch the middleware.

---

### Expiring download links for files in S3

**Request:** `/build-vs-buy` Users download private reports from our Express app. Files live in S3. I want expiring download links, so I plan to write HMAC signing with crypto.createHmac; it's about 20 lines. Good idea?

**Response:**

> Probably not. Since the files are already in S3, **S3 presigned URLs** likely do this job, and the project wouldn't have to own any crypto.
>
> #### Why not hand-rolled HMAC
>
> The 20 lines of signing are the easy part. You would also own:
>
> - **Verification details:** constant-time comparison (`crypto.timingSafeEqual`), including the expiry and object key inside the signed payload, canonical encoding of parameters, and secret rotation.
> - **Serving the file:** after validating your own signature, Express still has to get the bytes. It can stream them from S3, which puts every download's bandwidth and memory on your app servers. Or it can redirect, but the bucket is private, so you'd need a presigned URL anyway.
>
> That makes the custom version a second signing scheme layered on the one S3 already provides.
>
> #### Recommended approach
>
> 1. **Check authorization first.** An authenticated Express route confirms the user may access this report.
> 2. **Generate a short-lived presigned GET URL** with `getSignedUrl` from `@aws-sdk/s3-request-presigner`, for example 60 to 300 seconds. You may already have `@aws-sdk/client-s3`. If the presigner isn't installed, it's a small official AWS package, not a new vendor.
> 3. **Redirect (302) or return the URL.** Optionally set `ResponseContentDisposition` so the browser downloads the file with a friendly filename.
>
> What the project owns: the authorization check and the expiry policy. What it delegates: signing, verification, and serving the bytes.
>
> #### Trade-offs
>
> **Presigned URLs are bearer tokens.** Anyone holding the link can use it until it expires, and you can't revoke a single URL. Keep expiry short and generate the link only when the user clicks download, so it doesn't sit in emails or logs.
>
> **Expiry is capped by the signing credentials.** IAM user keys allow up to 7 days. Role or STS credentials, such as on ECS, Lambda, or EC2, allow only as long as the session lasts. That's fine for short links.
>
> **Custom HMAC plus streaming wins only if you need something S3 can't give you:**
> - revoking an individual link
> - an audit entry for every download, not just every link issued
> - links that last longer than your credentials allow
> - not exposing S3 hostnames to users
>
> If any of those is a real requirement, the custom approach is reasonable. Use `timingSafeEqual`, sign both the expiry and the key, and support key rotation.
>
> #### Unverified
>
> I haven't seen your repo. Please check:
> - which AWS SDK version you use
> - whether the presigner package is installed
> - that the bucket blocks public access
>
> #### Verify with
>
> - A valid link downloads the file.
> - An expired link returns 403.
> - A tampered key or signature returns 403.
> - A user who isn't authorized to see a report never receives a URL for it.
>
> **Revisit when:** you need per-link revocation, per-download auditing, or the files move out of S3.

---

## No wrappers for hypothetical futures

_A possible future vendor switch is not a concrete need today._

### A PaymentProvider interface around Stripe

**Request:** `/build-vs-buy` I'm integrating Stripe into our Laravel app. Should I wrap it in a PaymentProvider interface in case we switch processors later?

**Response:**

> **Short answer: no, not as a speculative abstraction.** "In case we switch later" is a hypothetical future need, and that alone doesn't justify an interface. But you should still keep Stripe out of your core logic. That separation is cheap and different from a provider abstraction.
>
> I haven't seen your codebase, so this is based on general reasoning. Please check it against your actual setup.
>
> #### Why a `PaymentProvider` interface usually backfires
>
> - **It's designed with only one implementation to learn from.** The interface ends up shaped like Stripe: PaymentIntents, SetupIntents, webhooks, idempotency keys, SCA flows. When you do switch, Adyen or Braintree won't fit it, and you'll rewrite it anyway.
> - **It lowers everything to the common denominator.** You either hide Stripe features you need or leak them through the "generic" interface.
> - **Switching processors is rarely just a code swap.** Stored payment methods, customer IDs, subscriptions, and webhook semantics all have to be migrated. An interface doesn't remove that cost.
> - **It's more to own now:** extra concepts, extra tests, and indirection for every developer reading payment code.
>
> #### What to do instead
>
> 1. **Check for an existing or built-in fit first.** If this is subscriptions or billing, look at **Laravel Cashier (Stripe)**, the first-party package. It handles webhooks, subscription state, and SCA, so you don't have to build them. If it covers your requirement, that's likely the least-owned-complexity option. If you're doing one-off charges or marketplace/Connect flows, the plain `stripe/stripe-php` SDK may fit better than Cashier.
> 2. **Contain Stripe in one place, without an interface.** Put a concrete `StripePayments` service (or Cashier calls) in one module. Controllers and domain code call it with your own types, like `Order` and `Money`, not Stripe objects. Store Stripe IDs in clearly named columns (`stripe_customer_id`). This gives you most of the "easy to switch" benefit for almost no cost. If you ever add a second processor, extracting an interface from a real second implementation is a small refactor.
> 3. **Own your payment state.** Keep your own record of orders and payment status, updated from verified webhooks. That's the part that actually makes a future migration feasible.
> 4. **Don't simplify away safety.** Verify webhook signatures, use idempotency keys on charge creation, handle failures and async confirmation, and never touch raw card data (use Stripe Elements or Checkout).
>
> #### When an interface *is* justified
>
> - You have a concrete plan for a second processor (regional coverage, failover, a contract decision).
> - You need multiple processors at the same time.
> - You need test doubles your current setup can't provide. Even then, Stripe's test mode plus mocking your concrete service usually covers this.
>
> **Revisit when:** a second processor becomes an actual requirement. Build the interface then, from two real implementations.
>
> If you share whether this is subscriptions, one-off payments, or marketplace payouts, and how your app is structured, I can say more specifically whether Cashier or the raw SDK fits.

---

## Adopting a proven module

_Generic complexity goes to a maintained solution; the project owns only what is specific to it._

### 3,000 redirects for a Drupal migration

**Request:** `/build-vs-buy` We're migrating a site to Drupal 10 and need about 3,000 redirects from old URLs to new ones. Should I write a custom module or use something existing?

**Response:**

> #### Use the existing Redirect module. Don't build a custom one.
>
> This is a solved problem in Drupal. Writing a custom module would mean owning request matching, admin UI, storage, caching, and edge cases that already exist in a maintained contrib module. I haven't seen your codebase, so first check `composer.json`. You may already have `drupal/redirect`, since it's commonly installed alongside Pathauto.
>
> #### Need
> - Map about 3,000 old URLs to new Drupal paths with permanent (301) redirects.
> - **Success check:** a script requests every old URL from your mapping file and asserts a single 301 to the expected target, with no chains and no 404s. Include a few deliberately malformed or unknown URLs to confirm they 404 cleanly.
>
> #### Options considered
>
> | Option | Fit | Notes |
> |---|---|---|
> | **Redirect module** (`drupal/redirect`) | Strong | Standard contrib module. Stores redirects as entities, has an editor UI, handles query-string passthrough, and integrates with Drupal caching. |
> | **Bulk import into Redirect** | Needed for 3,000 rows | Two routes: (a) the Migrate API, writing to the `redirect` entity destination, which is a good fit if you're already using Migrate for content; (b) `path_redirect_import` for a CSV import from the UI or Drush. |
> | **Web server rules** (nginx `map` / Apache `RewriteMap`) | Credible alternative | No Drupal dependency, and redirects happen before Drupal boots. Editors can't manage them, and they live outside the CMS. Good if the list is frozen forever. |
> | **Custom module** | Poor | Reinvents all of the above with no gain. |
>
> #### Recommendation
> **Redirect module, populated through Migrate** if you're already migrating content that way. Otherwise use a CSV import. The project owns only the **mapping data** (old URL to new URL) and the test script. Matching, storage, and the UI are delegated to the module.
>
> Choose web server rules instead only if editors will never need to touch these redirects and you want zero Drupal overhead.
>
> #### Things to decide before importing
> - **Normalization:** trailing slashes, case sensitivity, and URL encoding of old paths. Old sites are often inconsistent here.
> - **Query strings:** decide whether `?id=123`-style legacy URLs need distinct targets. Redirect can match on query strings, but test this specifically.
> - **Chains and loops:** make sure no target is itself a redirect source.
> - **Targets:** point to node paths (`/node/123`) or internal entity URIs where possible, rather than aliases, so redirects survive alias changes.
>
> #### Unverified
> - I haven't confirmed the current D10 compatibility and maintenance status of `path_redirect_import`. Check its project page and release history before relying on it. The Migrate route avoids that dependency entirely.
> - I haven't confirmed whether Redirect is already in your project.
>
> #### Strongest objection
> Web server rules are simpler at runtime and add no Drupal module. That only wins if the redirect list will never change and nobody non-technical needs to manage it, which is rarely true after a migration.
>
> **Revisit when:** the redirect volume grows into the hundreds of thousands, or performance profiling shows redirect lookups matter. At that point, move the static legacy set to web server maps.
>
> I haven't added anything to your project. Want me to look at your `composer.json` and migration setup to pick the import route?

---

## Deep checks

_New services, replacing an established component, or choices that are costly to reverse get the full decision record._

### PDF invoices from Drupal

**Request:** `/build-vs-buy` Our Drupal 10 site needs to generate PDF invoices from order data, around 500 a day. Options I've found: the Entity Print module, a headless Chrome service we'd run ourselves, or a SaaS PDF API. Which should we use?

**Response:**

> #### Build vs Buy: PDF Invoices on Drupal 10
>
> **Recommendation: Entity Print with the Dompdf engine.** If you use Drupal Commerce, look at **Commerce Invoice** first, because it builds on Entity Print. Escalate to headless Chrome only if a real invoice template fails to render acceptably.
>
> I haven't seen your codebase, so some points below depend on facts you'll need to confirm. Items marked **unverified** need checking before you commit.
>
> #### Questions that could change the answer
>
> 1. **Are orders in Drupal Commerce?** If yes, Commerce Invoice may already cover invoice numbering, storage, and PDF generation. That's the cheapest path.
> 2. **How complex is the layout?** Simple invoices with tables, a logo, and totals suit Dompdf. Complex designs using flexbox/grid, web fonts, or pixel-matched brand templates may not.
> 3. **Must an invoice stay unchanged once issued?** In most jurisdictions it must. That means you generate the PDF once, store it as a private file, and serve the stored copy. This requirement matters more than which engine you choose.
> 4. **Is sending customer and financial data to a third party acceptable** under your privacy policy and contracts?
>
> #### Options
>
> | | Entity Print + Dompdf | Self-run headless Chrome | SaaS PDF API |
> |---|---|---|---|
> | Fits Drupal | Native: Twig templates, entity view modes | Custom integration code | Custom integration code |
> | Rendering fidelity | Limited CSS (roughly CSS 2.1 plus some extras; no flexbox/grid) | Full modern CSS | Usually full (Chrome-based) |
> | Ops burden | None beyond PHP | A service to run, patch, monitor, and scale | None, but adds an external outage dependency |
> | Data leaves your infra | No | No | **Yes** (PII and financial data) |
> | Cost | Free | Hosting plus maintenance time | Per-document fees (**unverified** pricing) |
> | Reversibility | High: templates are HTML/Twig and portable | Medium | Lower: vendor API and template lock-in |
>
> At about 500 invoices a day, volume doesn't decide anything. Dompdf on a queue worker handles that easily for simple documents. Run generation in a queue or cron rather than in the request, so a slow render never blocks checkout.
>
> #### Choice (proposed, not adopted)
>
> **The project owns:**
> - the invoice Twig template and print CSS
> - invoice numbering
> - the immutable-storage rule
>
> **The project delegates:**
> - HTML-to-PDF conversion, to Entity Print and Dompdf
>
> Entity Print's PDF engine is pluggable. If Dompdf's CSS support turns out to be inadequate, you can swap the engine and keep the templates. Check which engines are currently maintained for D10 (**unverified**). This avoids designing for headless Chrome before you know you need it.
>
> #### Strongest case for the runner-up (headless Chrome)
>
> Dompdf's CSS limits are real. Teams often discover them late, then rewrite templates in table-based layouts or fight odd page-break bugs. Chrome renders exactly what designers see in the browser.
>
> **My view:** this is a real risk, but it can be tested cheaply. Render your actual invoice design, including a multi-page order, in Dompdf before deciding. If it fails, use headless Chrome. Gotenberg is a common packaged option (**unverified** for your stack). Prefer it over SaaS because the data stays in-house.
>
> **SaaS** only makes sense if you can't run a container and Dompdf fails. Even then, the privacy review in question 4 has to pass first.
>
> #### Verify before adopting
>
> - Render a real invoice with a long order that spans pages, non-ASCII characters (customer names, currency symbols), and your logo and fonts.
> - Test the failure path: confirm that a render error is logged and retried by the queue, and that it never produces a blank or partial stored invoice.
> - Check Entity Print and Dompdf release history and open security advisories on drupal.org and GitHub. Confirm license compatibility (Dompdf is LGPL; Entity Print is GPL; **verify**).
>
> #### Revisit when
>
> - Templates need CSS that Dompdf can't render, or
> - generation time or volume grows enough that PHP-side rendering strains your workers.

---

### Feature flags for a Rails app

**Request:** `/build-vs-buy` Our Rails app needs feature flags with percentage rollouts and per-tenant targeting. Today we toggle features with ENV vars and redeploy. Should we buy LaunchDarkly or build our own?

**Response:**

> #### Build vs Buy: Feature Flags for a Rails App
>
> This is a **deep check** because it adds a dependency or service and is costly to reverse once flag checks spread through the code. I haven't seen your repo, so two things need confirming first:
>
> - Does your `Gemfile` already include a flag gem such as `flipper` or `rollout`? If so, use it.
> - Do you already run Redis? Flipper can store flags in Redis as well as in your existing database.
>
> #### Need
>
> - Toggle features at runtime with no redeploy.
> - Roll out by **percentage**, deterministically, so the same tenant or user gets a stable result.
> - Turn features on or off **per tenant**.
> - **Success check:** enable flag X for tenant A and for 10% of other tenants without deploying. Each tenant's result stays the same across requests. If flag storage is unreachable, the code falls back to a safe default (off).
>
> #### Open questions that change the answer
> 1. Do non-engineers (PM, support) need to flip flags, with approvals or audit trails?
> 2. Do you need flags outside this Rails app, such as a JS frontend, mobile apps, or other services?
> 3. Do you need experimentation (A/B metrics), or only rollout control?
> 4. Do you have compliance requirements for change audit logs?
>
> #### Options
>
> **"Build vs LaunchDarkly" is a false binary.** The likely winner sits between them.
>
> | Option | Fit | Key findings |
> |---|---|---|
> | **Rails built-ins** | ✗ | Rails has no flag system. ENV vars are your status quo and can't do runtime changes or targeting. |
> | **Flipper gem (open source)** | ✓ likely | Supports boolean, per-actor, group, and percentage-of-actors rollouts. Make `Tenant` an actor (`flipper_id`) for per-tenant targeting. The ActiveRecord adapter uses your existing DB. Includes an optional mountable web UI and an optional paid hosted tier (Flipper Cloud). MIT license. *Confirm current feature set and maintenance activity against first-party docs and release history.* |
> | **LaunchDarkly** | ✓ but heavy | Covers everything plus audit logs, approvals, multi-language SDKs, and experimentation. It adds an external runtime service, per-seat or usage pricing (**unverified**, get a quote), and vendor-specific calls in your code. |
> | **Unleash (self-host or SaaS)** | ✓ | A middle ground with multi-language SDKs. You run a separate service or pay for SaaS. Worth considering only if Q2 is "yes" and LD's price is a problem. |
> | **Build from scratch** | ✗ | Looks like a table plus a hash function. In practice you must also own stable hashing, caching per request, a UI, audit, and tests. That means reimplementing Flipper while owning the bugs. No project-specific need justifies it. |
>
> #### Choice (proposed, not adopted)
>
> **Flipper with the ActiveRecord adapter, with `Tenant` registered as an actor.** This is a hybrid:
>
> - **Delegated to the gem:** evaluation, percentage hashing, storage, and the UI.
> - **Owned by you:**
>   - One small `Features.enabled?(:name, tenant)` helper. It is justified by a real need today: it sets the safe default when storage errors and keeps call sites uniform. It is not there for a hypothetical vendor switch.
>   - A naming and cleanup convention for flags.
>   - Protecting the Flipper UI behind admin auth.
>
> Migration path: move each ENV flag to Flipper one at a time. Keep the ENV value as the initial state, then delete the ENV var.
>
> #### Consequences
>
> - **Trade-off:** one gem plus a few DB tables. There is no new external service and no per-seat cost. You don't get approvals, rich audit, or experimentation out of the box.
> - **Maintenance:** gem upgrades, plus discipline about removing stale flags. Stale flags are the real long-term cost with any tool.
> - **Replacement:** checks go through one helper, so moving to LaunchDarkly later means changing the helper and porting flag state. That cost is moderate and bounded.
> - **Strongest surviving objection (case for LaunchDarkly):** if non-engineers will run rollouts, compliance needs an approval and audit trail, or flags must work across several apps or languages, LD provides all of that on day one. Building it around Flipper would make you own exactly the generic complexity you should delegate. If you answer "yes" to Q1, Q2, or Q4, this objection wins and LD (or Unleash) is the better choice.
>
> #### Verification (not yet done)
>
> Smallest meaningful test, in a spike branch:
> 1. Enable a flag for one tenant actor and assert it's on only for that tenant.
> 2. Enable it for 25% of actors. Check that about 25% of 1,000 tenants are on and that repeated calls return identical results.
> 3. **Failure path:** stub the adapter to raise and assert the helper returns `false` and logs the error.
>
> #### Revisit when
>
> - Flags are needed beyond this Rails app.
> - Non-engineers need governed rollouts or audit logs.
> - You want built-in A/B experimentation.
> - Flipper's maintenance stalls.
>
> If you share your `Gemfile` and answers to the four questions, I can firm this up from "proposed" to a concrete plan.

---

### Replacing Elasticsearch with Algolia

**Request:** `/build-vs-buy` Our team wants to replace our self-hosted Elasticsearch cluster with Algolia because ES version upgrades are painful. We index about 2 million products. Should we switch?

**Response:**

> #### Build vs Buy: Elasticsearch to Algolia
>
> **Short answer: Probably not yet.** The problem you named is upgrade pain, which is operational. Algolia fixes it, but it also replaces your query model, indexing pipeline, and cost structure. A managed Elasticsearch or OpenSearch service fixes the same pain with far less change. Compare against that before committing to a migration that is costly to reverse.
>
> This counts as a deep check: it replaces an established component, adds a new paid service, involves a data migration, and is costly to reverse. I haven't seen your codebase, so the findings below are framed as what to verify.
>
> #### Need
>
> - **Stated outcome:** Stop absorbing painful ES version upgrades.
> - **Implicit requirement:** Keep current search behavior for ~2M products, including relevance, filters/facets, latency, and indexing freshness, at acceptable cost.
> - **Success check:** The production query suite returns equivalent or better results, upgrades no longer need team effort, and total monthly cost (service plus engineering time) is at or below today's.
> - **Separate demonstrated needs from guesses.** Are there also real complaints about relevance, typo tolerance, or search UX? If so, that changes the comparison. If the only complaint is upgrades, weigh that alone.
>
> #### Options
>
> | Option | Fixes upgrades? | Migration cost | Lock-in | Key risks / unknowns |
> |---|---|---|---|---|
> | **Status quo + better process** (stay on a supported version, automate rolling upgrades) | Partially | None | None | Pain may be structural (plugins, custom scripts, mapping drift). Find out *why* upgrades hurt. |
> | **Managed ES** (Elastic Cloud) or **managed OpenSearch** (AWS, Aiven, etc.) | Yes, vendor handles upgrades | Low for Elastic Cloud. Moderate for OpenSearch (API divergence since 7.10) | Low to moderate | Cost at your data volume. OpenSearch compatibility with your queries and plugins is **unverified**. |
> | **Algolia** | Yes, fully hosted | High: rewrite indexing, queries, ranking, and possibly UI | High: proprietary API and ranking model | Pricing scales with records and search requests, so 2M records may be costly (**verify with a quote**). Per-record size limits may force restructuring large product docs (**unverified, check current plan limits**). Complex aggregations and custom scoring may not map 1:1. |
> | **Other hosted engines** (e.g., Typesense Cloud, Meilisearch Cloud) | Yes | High (same rewrite as Algolia) | Moderate (open-source cores reduce lock-in) | Feature fit at 2M products is **unverified** |
>
> The decisive questions to answer from your code and usage:
>
> 1. **Why are upgrades painful?** Custom plugins, Painless scripts, deprecated query syntax, index rebuild time, cluster sizing? If it's your own usage, those same features are what make an Algolia migration hard.
> 2. **Which ES features do you rely on?** Complex aggregations, nested docs, custom analyzers, function_score, percolation? List them and check each against Algolia's docs.
> 3. **What's the query volume?** Algolia pricing depends heavily on search requests, not just record count.
> 4. **Do you use ES for anything besides product search** (logs, analytics, internal tools)? If so, Algolia won't replace those uses.
>
> #### Choice
>
> **Deferred, leaning toward managed Elasticsearch.**
>
> - **Recommended next step:** Get quotes from Elastic Cloud and Algolia for your actual record count, document size, and monthly query volume. Then run a small spike that replays your top production queries against Algolia to check relevance and feature parity.
> - **What you'd own vs. delegate:**
>   - With managed ES, you delegate cluster operations and upgrades, and keep your queries, mappings, and indexing code unchanged.
>   - With Algolia, you delegate everything infrastructural, but must rebuild and own a new indexing pipeline, ranking configuration, and query layer.
>
> #### Strongest case for Algolia (adversarial review)
>
> I couldn't spawn an independent reviewer here, so this is my best case for the runner-up.
>
> If your team spends real time tuning relevance, typo tolerance, and merchandising, Algolia's built-in product-search features could deliver a better experience than hand-tuned ES. It also removes search infrastructure from your team's concerns entirely. For a product catalog, that domain fit is real.
>
> **Does it hold?** Only if search quality is also a demonstrated problem, and the quote fits your budget. If the sole driver is upgrades, Algolia is a large migration to fix an ops problem that a managed service solves directly.
>
> #### Consequences
>
> - **Main trade-off:** Algolia trades upgrade pain for vendor lock-in and usage-based cost that grows with traffic. Managed ES trades a hosting bill for keeping your existing investment.
> - **Strongest surviving objection:** Managed ES may still require you to handle major-version API changes in your own query code. That residual effort is real, but much smaller than a full rewrite.
> - **Replaceability:** Whatever you choose, keep search access behind one service module, which you likely already have. Don't add abstraction layers for a hypothetical future switch.
>
> #### Revisit when
>
> - Quotes show managed ES costs significantly more than Algolia at your volume, or
> - Search relevance or UX becomes a stated business problem, not just operations, or
> - The spike shows Algolia handles your top queries and facets with equal or better results.
>
> If you share why upgrades have been painful and which ES features your product search uses, I can narrow this to a concrete recommendation.
