# advise-me examples

Real responses from the [`advise-me`](SKILL.md) skill, unedited. Each one was produced from a single run on the current `SKILL.md`, using the default Claude model in the author's agent harness (October 2026). The skill was loaded as the system prompt with no tools, so the responses could not read code or ask follow-up questions first.

Technical details in the responses come from the model and have not been verified. Treat them as examples of how the skill reasons and formats its answer, not as Drupal or infrastructure advice.

This file is for people. `SKILL.md` does not reference it, so agents do not load it.

## Contents

- [Expert-framed questions](#expert-framed-questions)
  - [Batch API or Queue API for a nightly ERP import?](#batch-api-or-queue-api-for-a-nightly-erp-import)
  - [Hiring for a flood of password-reset tickets](#hiring-for-a-flood-of-password-reset-tickets)
  - [Speeding up a 40-minute Behat suite](#speeding-up-a-40-minute-behat-suite)
  - [Cache tags for a slow page for logged-in users](#cache-tags-for-a-slow-page-for-logged-in-users)
  - [Choosing a Redis eviction policy for Drupal cache bins](#choosing-a-redis-eviction-policy-for-drupal-cache-bins)
  - [Moving Drupal search to Solr](#moving-drupal-search-to-solr)
- [No proposal](#no-proposal)
  - [Building a survey in Drupal](#building-a-survey-in-drupal)
  - [Stopping bots from overwhelming a Drupal site](#stopping-bots-from-overwhelming-a-drupal-site)
- [A cheaper path exists](#a-cheaper-path-exists)
  - [A Slack bot to remind an approver who is often traveling](#a-slack-bot-to-remind-an-approver-who-is-often-traveling)
- [Sound plans](#sound-plans)
  - [Sending Drupal mail through SendGrid to stop spam-foldering](#sending-drupal-mail-through-sendgrid-to-stop-spam-foldering)
  - [A Drupal 10 to 11 upgrade plan](#a-drupal-10-to-11-upgrade-plan)
- [Small questions](#small-questions)
  - [Naming a variable](#naming-a-variable)
- [Follow-up pushback](#follow-up-pushback)
  - ["My tech lead says cache tags are the answer"](#my-tech-lead-says-cache-tags-are-the-answer)

---

## Expert-framed questions

_The user is an expert asking a narrow question inside a familiar frame. The skill checks whether the frame is the problem._

### Batch API or Queue API for a nightly ERP import?

**Request:** `/advise-me` I'm writing a custom Drupal module to import our product catalog from the ERP every night. Should I use the Batch API or the Queue API?

**Response:**

> **Real objective:** Get a reliable, repeatable nightly sync of ERP products into Drupal entities, not just pick a job-processing API.
>
> **Verdict:** Use neither as your foundation. Build the import on Drupal's **Migrate API** (core `migrate` plus contrib `migrate_plus` and, if needed, `migrate_tools`). Write a source plugin for your ERP feed and a migration YAML, then run `drush migrate:import products --update` from system cron each night. Migrate already gives you what you would otherwise hand-build on top of Batch or Queue: a source-to-destination ID map (so reruns update instead of duplicate), change detection via `track_changes` or a high-water mark, field mapping and process plugins, error messages per row, rollback, and memory-safe chunking. Unless your ERP integration is truly exotic, this is less code you have to own.
>
> **Ruled out:**
> - **Batch API:** It is designed for browser-driven, user-watched operations. Running it unattended at night means `drush_backend_batch_process` workarounds, and you still hand-write idempotency and mapping.
> - **Queue API (custom QueueWorker on cron):** It suits unattended work but is limited by cron time budgets. You would also reinvent ID mapping, update detection, and failure tracking.
>
> **What would flip the verdict:**
> - If the ERP **pushes** item-level change events (webhooks) rather than offering a pullable feed, enqueue each event with the Queue API and process it with a QueueWorker. Migrate's pull model fits that poorly.
> - If each product requires slow, rate-limited per-item API calls to the ERP, consider Queue for that fan-out. You can still let Migrate own the entity writes.
> - If you need to remove products that disappear from the ERP, Migrate handles this with `--sync` in recent `migrate_tools`. Confirm your version supports it.
>
> **Cheapest check:** Pull a sample export (a few hundred rows) and map it with a `migrate_plus` URL/JSON or CSV source, with no custom PHP. If `drush migrate:import` and a rerun with `--update` behave correctly, the approach holds.
>
> **First step:** Find out what the ERP exposes: a file drop, a REST/JSON endpoint, or a DB view. That determines whether an off-the-shelf `migrate_plus` source plugin works or you write a small custom one.

---

### Hiring for a flood of password-reset tickets

**Request:** `/advise-me` Our support team is drowning in password-reset tickets. I want to hire two more support agents. What hiring profile should we target?

**Response:**

> **Real objective:** Make password-reset tickets mostly stop arriving, not staff up to absorb them.
>
> **Verdict:** Don't hire for this yet. Password resets are usually among the most automatable ticket types. A working self-service reset flow typically removes most of them for the cost of a few engineering days, not two ongoing salaries. Hire only if, after fixing the cause, the remaining volume genuinely needs human judgment (e.g., identity verification for locked or compromised accounts). The answer changes if one fact is true: **your users already have self-service reset and still file tickets.** Then the problem is a broken flow, and you fix that flow.
>
> **Likely root causes to check (each has a non-hiring fix):**
> - No self-service reset, or it's hard to find → add a "Forgot password" link on the login page and in the help widget.
> - Reset emails not arriving (spam, deliverability, expired links) → fix email authentication (SPF/DKIM/DMARC), lengthen link expiry, add a "resend" option.
> - Users forget because they log in rarely → offer magic-link or passwordless login, or SSO (Google/Microsoft) for business customers.
> - Aggressive lockout policies → loosen thresholds and add automatic unlock after a set time, without weakening security below your requirements.
> - Users don't know which email or username they used → add account recovery by alternate email or phone.
> - Agents must verify identity manually by policy → automate verification (email/SMS code, ID check vendor) where your compliance rules allow it.
>
> **Ruled out:**
> - Hiring two agents first: this costs salary plus onboarding, scales linearly with growth, and leaves the cause in place.
> - Canned macros alone: they speed up handling but don't cut volume.
>
> **What would flip the verdict:** Resets require human identity checks you're legally barred from automating, or the rest of the queue is also overloaded independent of resets. In that case, hire high-volume tier-1 agents with strong written communication and comfort with verification and security procedures (spotting social-engineering attempts matters more than product depth).
>
> **Cheapest check:** Pull the last 100–200 reset tickets and tag each with why the user couldn't self-serve. Half a day of work tells you which fix applies.
>
> **First step:** Do that ticket tagging this week. Then put a deflection link or auto-reply ("Reset your password here") on any ticket matching "password." It's a same-day change that cuts volume while the real fix ships.

---

### Speeding up a 40-minute Behat suite

**Request:** `/advise-me` Our Behat suite takes 40 minutes in CI. Should I parallelize it within one job or shard it across multiple CI runners?

**Response:**

> **Real objective:** Cut CI feedback time, which may depend more on where the 40 minutes go than on how you split them.
>
> **Verdict:** First spend an hour profiling. Behat suites that take 40 minutes are often dominated by a few slow scenarios: browser/JS scenarios, full database reloads per scenario, or `sleep`/fixed waits. Fixing those can halve the time without adding parallel infrastructure. If you still need parallelism after that, **shard across runners** rather than parallelizing within one job. The hard part of parallel Behat is state isolation: database, cache, sessions, uploaded files, browser. Separate runners give you that for free, because each one has its own service containers. Within one job, you have to build per-worker databases and ports yourself, and the resulting flaky cross-talk failures are expensive to debug.
>
> **Ruled out:**
> - **In-job parallelism as the default.** Behat has no native parallel runner. Third-party tools like `liuggio/fastest` and parallel extensions work, but you still have to isolate shared state per worker.
> - **Sharding before profiling.** This multiplies runner cost, including setup time on every shard, without removing the waste.
>
> **What would flip it:**
> - Per-job setup is long (for example, 8 or more minutes of build, `composer install`, and DB seeding), so it would be repeated on every shard.
> - CI runner minutes or concurrency are scarce or expensive.
> - Your app already supports per-worker DB names (for example, through an env var like `DB_NAME=test_${WORKER}`).
>
> If any of these hold, in-job parallelism becomes the better choice.
>
> **Cheapest checks:**
> 1. Run with a per-scenario timing output. Use a timing formatter, or parse JUnit output (`--format junit`), then sort by duration. Check whether the top 10% of scenarios account for most of the time.
> 2. Grep for `sleep(` and fixed waits, and replace them with `spin`/wait-until helpers.
> 3. Check whether every scenario rebuilds the DB. Wrapping each scenario in a transaction rollback, or restoring a DB snapshot, is often a big win.
> 4. Measure your CI setup time separately from test time.
>
> **First step:** Generate JUnit output on the next CI run and sort the scenarios by duration.
>
> If you shard afterward, split by recorded timings rather than by file count, so the shards finish at about the same time. Tags or suite splits can drive this. GitLab `parallel:` and GitHub matrix both make it easy.

---

### Cache tags for a slow page for logged-in users

**Request:** `/advise-me` I want to improve performance on a slow Drupal page for authenticated users. Should I try cache tags?

**Response:**

> **Real objective:** Find what makes the page slow for logged-in users, then cache or fix that. Cache tags handle invalidation; they don't speed anything up by themselves.
>
> **Verdict:** Don't start with cache tags. Start by checking whether Dynamic Page Cache is working on this page, then profile. In Drupal, slow authenticated pages usually come from one of two causes. One is a render element with `max-age: 0` or a high-cardinality context like `user` or `session` that bubbles up and makes the whole page uncacheable. The other is a slow query or a slow uncached block. Cache tags only matter once you're caching something and need it to invalidate correctly. That's needed for correctness, but it won't fix the slowness.
>
> **Ruled out:**
> - *Leading with cache tags:* tags without working caching do nothing. Missing tags make content stale. They don't make pages slow.
> - *Internal Page Cache:* it only serves anonymous users.
> - *Reverse proxy or full-page cache for authenticated users:* complex and risky with personalized content. Only worth it at high scale after the basics are fixed.
>
> **What would flip the verdict:** Suppose you have a custom block or service doing expensive work, and you've already added caching with `max-age: 0` or a short TTL because you couldn't invalidate it properly. Then proper cache tags, often custom ones, are exactly the fix. They let you cache it permanently and invalidate it on change.
>
> **Cheapest checks:**
> 1. Enable `http.response.debug_cacheability_headers: true` in `services.yml`. Load the page as an authenticated user and look at `X-Drupal-Dynamic-Cache`.
>    - `UNCACHEABLE`: look at `X-Drupal-Cache-Contexts` and `X-Drupal-Cache-Max-Age` to find the culprit.
>    - `MISS` on every load: something is invalidating too often, for example a broad tag like `node_list`.
>    - `HIT` but still slow: the time is in placeholders, lazy builders, or the theme layer.
> 2. Profile one request with Webprofiler (Devel), XHProf, or Blackfire to see where the time actually goes.
>
> **Likely fixes, depending on what you find:**
> - Move personalized bits (user name, cart, "hello X") into **lazy builders**, and make sure **BigPipe** is enabled. The rest of the page then stays cached.
> - Remove stray `max-age: 0` settings or `user` contexts. Common sources are Views with per-user caching off, and custom code calling `\Drupal::currentUser()` without proper metadata.
> - Fix the slow query or Views config the profiler points to.
>
> **First step:** Turn on the debug cacheability headers and read `X-Drupal-Dynamic-Cache` for this page. That one header tells you which of the paths above you're on.

---

### Choosing a Redis eviction policy for Drupal cache bins

**Request:** `/advise-me` Our Drupal site's Redis keeps running out of memory. Which eviction policy should I set for the cache bins: allkeys-lru or volatile-lru?

**Response:**

> **Verdict:** Use **`allkeys-lru`**, provided this Redis instance holds only Drupal cache bins. Every key is then disposable, so Redis should be free to evict any of them. `volatile-lru` only evicts keys that have a TTL. If anything lacks one, Redis can still fill up and return OOM errors on writes, which is the failure you're seeing. Set an explicit `maxmemory` too. Without it, or with the default `noeviction` policy, the eviction policy never applies.
>
> **Condition that flips it:** If the same Redis also stores things you can't lose, such as the lock backend, queues, sessions, or anything that isn't cache, `allkeys-lru` can silently evict them. In that case, give the cache its own Redis instance or database with `allkeys-lru`. Don't fall back to `volatile-lru`, which only protects keys by accident of whether they have a TTL.
>
> **Ruled out:**
> - `volatile-lru`: whether it protects anything depends on which keys get TTLs (the Drupal redis module's permanent-item TTL behavior varies by version and settings). It's fragile, and it's the usual way people still hit OOM.
> - `noeviction` (often the default): this is what produces "out of memory" write errors.
> - `allkeys-lfu`: can beat LRU on hit rate, but the difference is marginal. Try it later if hit rate matters.
>
> **Check the cause, not just the policy:** "Keeps running out of memory" often means one bin is bloating, not that Redis is undersized. Common culprits:
> - `page` cache bin filled by bots and crawlers hitting URLs with random query strings. Each variant gets cached.
> - `render`/`dynamic_page_cache` with high-cardinality cache contexts (`url.query_args`, `user`).
>
> Cheapest check: `redis-cli --bigkeys`, or `SCAN` with a count per bin prefix (e.g. `*:page:*` vs `*:render:*`). If one bin dominates, fix it there. For example, strip junk query params at the CDN or proxy, or reduce the cache context. A bounded cache with eviction is fine, but one that's mostly junk entries just lowers your hit rate.
>
> **First step:**
> ```
> CONFIG GET maxmemory*
> CONFIG SET maxmemory <~75% of available RAM>
> CONFIG SET maxmemory-policy allkeys-lru
> ```
> Persist both settings in `redis.conf`. Then run `INFO stats` and watch `evicted_keys` and `keyspace_hits/misses` for a day.

---

### Moving Drupal search to Solr

**Request:** `/advise-me` Should we move our Drupal site search from the core database backend to Solr?

**Response:**

> **Real objective:** Fix a specific search shortfall (relevance, speed, facets, fuzzy matching) at the lowest ongoing ops cost, not "run Solr."
>
> **Verdict:** Move to Solr only if you've hit a limit the database backend can't solve **and** your host offers managed Solr (Acquia, Pantheon, Platform.sh). If you'd have to run Solr yourself, first move to or tune the **Search API module with its Database backend**. It covers most sites up to tens of thousands of nodes and adds no new infrastructure. Which problem are you seeing, and who hosts you? Those two facts decide this.
>
> **Ruled out / deprioritized:**
> - **Staying on core Search module as-is:** its relevance tuning is weak and it has no facets. If you're on it, moving to Search API is worthwhile even without Solr.
> - **Self-hosted Solr by default:** this adds a JVM service, schema and config syncing, upgrades, and monitoring. That ongoing cost isn't justified for a modest content site.
> - **Elasticsearch/OpenSearch:** it's comparable to Solr, but Drupal's Solr integration (search_api_solr) is more mature, and managed Drupal hosts bundle Solr, not ES.
>
> **What would flip it toward Solr now:**
> - You need typo tolerance, stemming or synonyms per language, highlighting, "more like this," or good relevance boosting.
> - Search queries are slowing the database at your content volume (roughly 50k+ items or heavy faceting).
> - Your host includes Solr at no extra ops cost. Then it's nearly free, so take it.
>
> **Cheapest checks:**
> 1. Write down the 3–5 searches users complain about. Test whether Search API DB with field boosts and the "Ignore case," "Tokenizer," and "Stemmer" processors fixes them.
> 2. Check your host's plan for a bundled Solr index.
> 3. Look at slow-query logs for `search_api_db_*` tables under real traffic.
>
> **First step:** List the concrete search failures you're trying to fix and confirm what your host offers. If you share those, I'll give you a firm call.

---

## No proposal

_The user brings only a need or a symptom. The skill tests its own first idea instead of the user's._

### Building a survey in Drupal

**Request:** `/advise-me` I need to build a survey in Drupal. What should I do?

**Response:**

> **Verdict:** Use the **Webform** module (drupal.org/project/webform). It is the standard Drupal tool for surveys and very likely the right choice. It gives you a drag-and-drop form builder, conditional logic, multi-page wizards, survey-style elements (Likert scales, ratings, matrices), stored submissions, CSV export, email notifications, access controls, and basic result summaries. Don't build a custom form or a content type for this.
>
> **One question that could change this:** Do responses need to live in your Drupal site? That matters if you need data residency or compliance, logged-in user tracking, site branding, or integration with other site data.
> - **If yes, or you're unsure:** use Webform.
> - **If no, and it's a one-off survey:** an external tool like Google Forms or Microsoft Forms is less to maintain. Embed it or link to it. You also get better charts without adding a module to your site's update and security burden.
>
> **Ruled out:**
> - *Custom module with the Form API:* reinvents Webform, so you'd write and maintain code for no gain.
> - *Content type plus fields:* submissions become nodes, which is messy and has no survey features.
> - *Older poll or survey modules:* narrow in scope and less maintained than Webform.
>
> **Watch for:**
> - **Anonymous surveys:** Webform records IP addresses and user IDs by default. Turn on "anonymize submissions" in the form's settings.
> - **Personal data:** set a retention period and purge old submissions (Webform has a setting for this).
> - **Spam on public forms:** add the Antibot or CAPTCHA module.
> - **Advanced analytics:** if you need deeper analysis than Webform's summaries, export to CSV, or try the Webform Analysis submodule.
>
> **First step:**
> 1. Run `composer require drupal/webform`, then `drush en webform webform_ui`.
> 2. Go to Structure → Webforms → Add webform.
> 3. Build the survey using Likert and radio elements, and set "Anonymize submissions" if needed.
> 4. Submit a test response and check the Results tab before you share the link.

---

### Stopping bots from overwhelming a Drupal site

**Request:** `/advise-me` Help me prevent bots from overwhelming my Drupal site.

**Response:**

> **Real objective:** Keep the site fast for real visitors. That usually means bot requests should never reach PHP and the database, not that every bot gets blocked.
>
> **Verdict:** Put the site behind a CDN/edge proxy (Cloudflare is the cheapest, fastest option) and handle bots there. Before writing any rules, look at the logs to find which URLs the bots hit. In most Drupal cases the real damage comes from crawlers walking *uncacheable* URLs, such as faceted search, Views exposed filters, `?page=` and sort parameters, and `/search`. Every one of those requests forces a full Drupal bootstrap. Fixing those crawler traps at the edge removes most of the load. If you are already behind a CDN, the answer depends on what your logs show (see "What would flip it").
>
> **Ruled out:**
> - **Drupal modules (Honeypot, Perimeter, Ban, rate-limit modules):** They run *after* Drupal bootstraps, so each blocked request still costs you most of what serving it would. Honeypot targets form spam, not load.
> - **Blocking by user agent alone:** Bad bots spoof their user agents. Good bots, like Google, you usually want to keep.
> - **robots.txt alone:** Polite crawlers obey it, and those aren't the ones overwhelming you. It is still worth adding `Disallow` lines for facet and search paths.
> - **Scaling up servers:** This pays for the bots' traffic instead of stopping it.
> - **CAPTCHA on everything:** It hurts real users and SEO. Keep it as a targeted challenge only.
>
> **What would flip it:**
> - **Traffic concentrated on login, register, or form endpoints:** Then the problem is credential stuffing or spam. Add rate limits on those paths plus Honeypot/Antibot.
> - **Mostly legitimate cached pages from a few IPs:** Simple per-IP rate limiting at nginx or the CDN is enough.
> - **A host that forbids an external CDN (some managed Drupal hosts):** Use your host's built-in edge, such as Pantheon/Acquia's Fastly/Varnish, and its WAF rules instead.
>
> **Cheapest check:** Pull a day of access logs and count the top paths and top user agents, for example `awk '{print $7}' access.log | cut -d? -f1 | sort | uniq -c | sort -rn | head`, and run the same with query strings included. If most requests carry `f[0]=`, `?search`, or similar parameters, you've found your problem.
>
> **First step:**
> 1. Put Cloudflare (free plan) in front of the site and turn on Bot Fight Mode plus "Block AI Scrapers."
> 2. Add one WAF rule that challenges or blocks requests to facet and search URLs from non-verified bots.
> 3. Confirm Drupal's Page Cache and Dynamic Page Cache are on, so anonymous requests that do get through are served from cache.

---

## A cheaper path exists

_The user's plan works, but removing the cause beats it._

### A Slack bot to remind an approver who is often traveling

**Request:** `/advise-me` Every content change on our Drupal site needs approval from one manager, who is often traveling, so publishing takes days. I want to build a Slack bot that reminds her about pending approvals. Thoughts?

**Response:**

> **Real objective:** Get content published within hours, not days. The bottleneck is a single approver, not her awareness of pending items.
>
> **Verdict:** Don't build the bot. A reminder can't make a traveling manager available, and she probably already knows things are waiting. Remove the single point of failure instead: in Drupal's core Content Moderation, give a second trusted person the approve/publish permission, and route low-risk edits (typos, date changes, minor copy) through a workflow that skips manager review. If you still want pings after that, the contributed Content Moderation Notifications module can email or post to Slack with no custom code. **One fact would change this:** if a legal or regulatory rule requires *her specifically* to sign off on *every* change, keep her as the approver. In that case, make approving fast for her (mobile-friendly review links, one daily batched digest) rather than building a reminder bot.
>
> **Ruled out**
> - *Custom Slack bot:* new code to maintain, and it doesn't address her availability.
> - *Status quo plus nagging:* adds pressure but not capacity.
> - *Removing approval entirely:* gives up a quality and risk control you may legitimately need.
>
> **What would flip it**
> - Approval is a compliance requirement tied to her role. Then optimize her speed, not who approves.
> - She actually misses notifications rather than lacking time. Then the off-the-shelf notification module is enough.
>
> **Cheapest check**
> - Ask her and whoever set the policy: "Does every change need *your* approval, or does every change need *an* approval?"
> - Pull the last 30 publishes and tag each as minor or substantive. If most are minor, the tiered workflow removes most of the delay.
>
> **First step:** Have that 10-minute conversation with her. She likely wants to stop being the bottleneck too, and she can name a delegate and define what counts as "minor."

---

## Sound plans

_Controls. The plan is right, so the skill should agree and add only what is missing._

### Sending Drupal mail through SendGrid to stop spam-foldering

**Request:** `/advise-me` Our Drupal 10 site sends password reset emails with PHP mail() and they land in spam. I plan to switch to the Symfony Mailer module and send through our company's existing SendGrid account. Good plan?

**Response:**

> **Verdict:** Yes, with one condition. The fix that matters is SendGrid **domain authentication** (SPF/DKIM aligned with your From domain, plus a DMARC record), not the Drupal module. If you route through SendGrid without authenticating the domain, or with a From address on a domain SendGrid isn't authenticated for, you can still land in spam. With authentication in place, this plan is the standard, correct fix.
>
> **Why mail() lands in spam:** your web server sends unsigned mail from an IP your domain's SPF probably doesn't list, so DMARC fails. SendGrid fixes this only once DKIM signs as your domain.
>
> **Ruled out**
> - *Fixing PHP mail() / local Postfix with DKIM:* this is possible, but you'd have to own IP reputation, signing keys, and bounce handling. SendGrid already does all of that.
> - *Full Symfony Mailer module (symfony_mailer):* it's fine, but it replaces Drupal's mail policy and templating layer. If you only need a transport, **Symfony Mailer Lite** is the smaller change. Use the full module only if you also want its per-email policies and theming.
>
> **Watch out for**
> - **Shared reputation:** if the company account also sends marketing email, spam complaints on that mail can hurt delivery of your password resets. Ask for a dedicated subuser, or a separate IP pool if the account has dedicated IPs.
> - **Security:** create an API key scoped to *Mail Send only*. Inject it through `settings.php` or an environment variable, not exported config, so it stays out of git.
> - **Click tracking:** turn off link/click tracking for these emails. Rewritten reset links can break one-time tokens and look phishy to filters.
>
> **What would flip the verdict:** if the company account can't authenticate your site's sending domain (for example, a different team owns the DNS and won't add the CNAMEs), use a separate SendGrid account or another provider you can authenticate.
>
> **Cheapest check:** send a reset email to a Gmail address and choose "Show original." You want `SPF: PASS`, `DKIM: PASS` with your domain (not sendgrid.net), and `DMARC: PASS`. Mail-tester.com also gives a quick score.
>
> **First step:** in SendGrid, go to Settings → Sender Authentication → Authenticate a Domain for your From domain and get the DNS records added. Then install the mailer module and configure the SendGrid transport.

---

### A Drupal 10 to 11 upgrade plan

**Request:** `/advise-me` I'm upgrading a Drupal 10 site to 11. Plan: run Upgrade Status, fix deprecated code in our custom modules, move contrib to D11-compatible releases, then bump core with Composer and run updb. Anything wrong with that?

**Response:**

> **Verdict:** Your plan is the standard, correct path. It has four gaps that commonly break D10→D11 upgrades: the starting core version, platform requirements, modules removed from core, and contrib with no D11 release.
>
> **Gaps to add:**
>
> 1. **Get onto the latest D10 minor first.** D11 only supports updating from 10.3 or later. Update core to the latest 10.x, run `updb`, and deploy that as its own release. If you jump from an older minor, the update hooks fail.
> 2. **Platform requirements.** D11 needs PHP 8.3+, MySQL 8.0+ or MariaDB 10.6+ (or PostgreSQL 16 / SQLite 3.45), Drush 13, and Composer 2.7+. Upgrade Status reports some of these, but hosting and CI images often lag. Upgrade those before the core bump.
> 3. **Modules removed from core.** Actions UI, Book, Forum, Statistics, Tracker, and the Activity Tracker/History dependents moved to contrib. If any are enabled, `composer require` their contrib versions while you're still on D10. Otherwise they vanish when core is bumped and the site breaks. The same applies to deprecated core themes you may still use.
> 4. **Contrib with no D11 release.** Some modules will lag. Your options:
>    - Use `mglaman/composer-drupal-lenient` to install the module anyway, plus a patch from its issue queue (often just a `core_version_requirement` change).
>    - Replace the module.
>    - Drop the module.
>
>    Decide this before the bump, not during it.
>
> **Smaller points:**
> - Set `core_version_requirement: ^10.3 || ^11` in your custom `.info.yml` files so the same code runs on both versions while you transition.
> - Update contrib on D10 to releases that support both versions. That keeps the core bump a small, isolated step.
> - Run Upgrade Status against custom themes too, not just modules.
> - Do the core bump with `composer require 'drupal/core-recommended:^11' 'drupal/core-composer-scaffold:^11' 'drupal/core-project-message:^11' -W`. Also update `drush/drush` to `^13` in the same command. Then run `drush updb`, `drush cr`, and `drush cex` to capture config changes.
>
> **Cheapest check:** Run `composer why-not drupal/core 11` on a branch. It lists every package blocking the upgrade in one shot.
>
> **First step:** Update to the latest 10.x and confirm the PHP 8.3 / database versions on every environment, including CI and production.

---

## Small questions

_Low stakes get a short answer._

### Naming a variable

**Request:** `/advise-me` Should I name this PHP variable $userCount or $numUsers?

**Response:**

> **Verdict:** Use `$userCount`, unless your codebase already uses the `$numX` pattern. In that case match it, because consistency matters more than either name. `$userCount` is the more common PHP idiom (Laravel, Symfony, and PSR-style code favor `$xCount`). It reads naturally, groups alphabetically with other `$user*` variables, and avoids the ambiguity of `num`, which can mean "number of" or "a number identifying."
>
> One more check: if the variable only holds `count($users)` and is used once, you may not need it at all. Inline the call.
>
> **First step:** Grep the repo for `Count =` vs `$num` and follow whichever already dominates.

---

## Follow-up pushback

_The user disagrees without new facts. The skill keeps its position and says what would change it._

### "My tech lead says cache tags are the answer"

_This simulates a second turn: the earlier exchange is summarized inside the request._

**Request:** `/advise-me` Earlier I asked: 'Should I try cache tags to speed up a slow Drupal page for logged-in users?' You said cache tags only control invalidation and I should first check the X-Drupal-Dynamic-Cache header and profile the request. My tech lead says cache tags are the answer. I'm going with cache tags; just confirm that's fine.

**Response:**

> **Verdict:** I can't confirm it as stated. Your tech lead's view doesn't change what cache tags do: they control *when* cached items are invalidated, not *whether* something gets cached. Adding tags alone won't make a slow page fast. Your lead may still be right about the *fix*, though, and a two-minute check will tell you.
>
> **Where your lead is likely right:** A common cause of slow logged-in pages is a block or element that sets `max-age: 0` or the `user` cache context. Developers often do this because they don't know how to invalidate the item when its data changes. That one element makes Dynamic Page Cache skip the whole page. The fix is to give it proper **cache tags** (e.g., `node:123`, `config:system.site`) so it can be cached and invalidated correctly. If that's what's happening, "use cache tags" is the right answer and you should go ahead.
>
> **Where it wouldn't help:** If the page is already cached, or the time is spent elsewhere (slow queries, external API calls, heavy preprocess functions), tags won't help, and you'll have spent effort for no gain.
>
> **What would flip this:** If the header shows `X-Drupal-Dynamic-Cache: UNCACHEABLE`, or if render debugging shows `max-age=0` from an element whose data could be tagged, then the cache-tags approach is correct.
>
> **Cheapest check (do this first, about 2 minutes):**
> 1. Load the page as a logged-in user and check the response headers:
>    - `UNCACHEABLE`: find the element that is setting `max-age=0` or a high-cardinality context. Enable `renderer.config: debug: true` in `services.yml` to see this. Then fix that element with tags. Your lead's plan applies.
>    - `HIT` but still slow: the problem isn't caching at all. Profile the request with Webprofiler, XHProf, or Blackfire.
>    - `MISS` on every load: something is invalidating the cache too often. That can come from overly broad tags like `node_list`, so look at which tags are on the page.
> 2. Share the result with your lead. It will settle the question with evidence instead of opinion.

