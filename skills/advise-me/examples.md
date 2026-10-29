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

> **Verdict:** Use neither as your main tool. Use the **Migrate API** (core `migrate` plus contrib `migrate_plus` and `migrate_tools`) and run it nightly with `drush migrate:import --sync` from cron or a systemd timer. Recurring imports from an external system are exactly what Migrate is for. If you were forced to choose between your two options, Queue would win for unattended nightly work, because Batch is designed for a progress bar in a browser.
>
> **Real objective:** Keep Drupal's catalog in sync with the ERP reliably, every night, without supervision. The real question isn't "how do I process N items without timing out" but how you handle upserts, ID mapping, deletions, failures, and reruns. A hand-rolled module has to solve all of that itself. Migrate already has:
>
> - **ID map tables** linking ERP SKUs to Drupal entity IDs, so each run updates existing products instead of creating duplicates.
> - **`track_changes` and highwater marks**, so unchanged rows are skipped.
> - **`--sync`**, which removes products that disappeared from the ERP. Deletion handling is the part custom importers usually get wrong.
> - **Rollback, a per-row message log, `migrate:status`, and `--update`** for operational visibility.
> - **Source plugins** for JSON/XML over HTTP, CSV, or SQL, plus process plugins for field mapping. You write PHP only for transforms that are unusual.
> - **Memory handling.** Drush runs it from the CLI, so web timeouts don't apply and Migrate reclaims memory on long runs.
>
> **Ruled out:**
> - **Batch API:** built around an interactive UI session. Running it headless through drush works, but you still own all the sync logic.
> - **Queue API with custom import code:** a fine processing engine, but you'd rebuild ID mapping, change detection, deletion sync, and logging that Migrate gives you for free.
>
> **What would flip it:**
> - If the ERP **pushes per-product change events** (webhooks or a message bus) instead of a nightly dump, enqueue each event and process it with a Queue worker. Each item is then a small independent job, and Migrate's batch model fits poorly.
> - If transforms are extremely bespoke, such as complex Commerce variation and pricing logic, write custom process plugins. Stay inside Migrate rather than leaving it.
> - If the catalog is tiny (a few hundred rows), anything works, and Migrate is still the least code.
>
> **Cheapest way to check:** Write one migration YAML for about 20 products from a real ERP export. Run `drush migrate:import`, change one row and delete another in the source, then run it again with `--sync --update`. If updates and deletions behave correctly, the approach holds.
>
> **First step:** Tell me the ERP source format (REST JSON, CSV/SFTP drop, or direct DB) and whether products are Drupal Commerce entities. That decides the source plugin and destination, and I can sketch the YAML.

---

### Hiring for a flood of password-reset tickets

**Request:** `/advise-me` Our support team is drowning in password-reset tickets. I want to hire two more support agents. What hiring profile should we target?

**Response:**

> **Verdict:** Don't hire for this yet. Password resets are one of the most automatable ticket types, so fix why users can't reset themselves before adding headcount. Hire only if, after that fix, the remaining tickets are genuine identity-verification cases that need human judgment.
>
> **Real objective:** You want fewer people blocked from logging in, not more capacity to unblock them. The premise that this needs more agents holds only if resets truly require a human. Usually they don't. A steady flow of reset tickets usually means one of these:
> - Self-service reset is missing, broken, or hard to find.
> - Reset emails aren't arriving because of spam filters, deliverability problems, or users with an old address on file.
> - Sessions expire too often, so users forget passwords they rarely type.
> - Users are locked out because they lost access to their email or MFA device. This is the one case that legitimately needs a human.
>
> **What I ruled out:**
> - *Hiring two agents:* It adds about two salaries a year and scales linearly with user growth while leaving the cause untouched. Agents hired for repetitive reset work also tend to leave quickly.
> - *Outsourcing tier-1:* This is cheaper per ticket but has the same flaw. It also adds risk, because outside agents resetting accounts is a common way attackers take over accounts through social engineering.
> - *A chatbot that walks users through reset:* This treats the symptom. If self-service worked, users wouldn't need to be walked through it.
>
> **Fixes, roughly in order of cost:**
> 1. Make "Forgot password" prominent on the login page and in the help center, and have support macros link to it.
> 2. Fix email deliverability by checking SPF, DKIM, and DMARC and looking at bounce and spam rates on reset emails.
> 3. Offer passwordless login through magic links or passkeys, or SSO through Google, Microsoft, or Okta. For B2B customers, SSO removes the problem for whole accounts at once.
> 4. Lengthen session lifetimes with "remember me" where your security policy allows it.
> 5. Build a defined, secure recovery flow for users who lost email or MFA access. This is the part that may need people.
>
> **What would flip the verdict:** If most tickets are users who lost access to their email or MFA, need identity verification, or are in a regulated setting where resets must be human-verified, then hiring is reasonable. In that case, target agents with account-security and identity-verification experience who follow verification procedures strictly. Avoid hiring for general friendliness, because social-engineering resistance matters more than speed in this role.
>
> **Cheapest check (an afternoon):** Pull 50 recent reset tickets and tag each one by cause: didn't find self-service, email never arrived, lost email or MFA, or other. Also check your reset-email delivery and open rates. That breakdown tells you whether you have a product fix or a staffing need.
>
> **First step:** Do that 50-ticket audit this week. Can you tell me two things? Do you already have self-service reset, and are your users mostly consumers or business accounts? Those answers decide whether SSO or a fix to the reset flow is the main lever.

---

### Speeding up a 40-minute Behat suite

**Request:** `/advise-me` Our Behat suite takes 40 minutes in CI. Should I parallelize it within one job or shard it across multiple CI runners?

**Response:**

> #### Verdict
>
> **Shard across CI runners, but profile the suite first.** Behat has no native parallelism. Running it in parallel inside one job means isolating the database, cache, sessions, file uploads and browser or Selenium ports for each worker. That isolation is the hard, bug-prone part. Separate runners give each shard its own container and services for free, so you skip that work entirely.
>
> #### The real objective
>
> You want fast feedback, not parallelism as such. A 40-minute Behat suite usually has a few concentrated causes:
>
> - **`@javascript` scenarios** running in a real browser when they don't need JS. These are often 10–50× slower than BrowserKit/Goutte ones.
> - **Expensive per-scenario setup**, such as a full DB reset, migrations, or fixture loading on every scenario instead of a transaction rollback or snapshot restore.
> - **Fixed waits** like `sleep` or long `wait` steps instead of conditional waits.
> - **Business logic tested end-to-end** that belongs in unit or integration tests.
>
> Fixing these often halves the runtime. That gain stacks with sharding and makes the shards cheaper.
>
> #### What I ruled out
>
> - **In-job parallelism (paratest-style workers, GNU parallel over feature files):** Each worker needs its own DB name or schema, app port, and browser session. It also competes for one machine's CPU and memory. Worth it only if runners are scarce or expensive, or if you already have per-worker isolation.
> - **Sharding by feature file count:** Shards end up unbalanced. Split by recorded timing instead, using CI timing data or a simple JUnit-time-based splitter, so each shard finishes at about the same time.
>
> #### What would flip the verdict
>
> - Your CI bills heavily per job, or has a hard concurrency limit.
> - Runner startup (image pull, `composer install`, DB build, asset build) takes several minutes. That cost is paid once per shard, and on a large machine in-job parallelism may come out ahead.
> - Your setup already supports per-worker DB/env isolation, for example through env-var-driven DB names.
>
> #### Cheapest checks
>
> 1. Run with `--format=progress --format=junit` (or add a timing hook) to get per-scenario durations. See whether the top 10% of scenarios account for most of the time.
> 2. Count the `@javascript` scenarios and time them as a separate group.
> 3. Measure how long one CI job takes from start until the first scenario runs. That is your overhead per shard.
>
> #### First step
>
> Collect per-scenario timings in the next CI run. If the startup overhead is under about 3 minutes, split into 4 shards balanced by timing, using `--tags` or a file list per shard. Then fix the slowest scenarios and the setup cost.

---

### Cache tags for a slow page for logged-in users

**Request:** `/advise-me` I want to improve performance on a slow Drupal page for authenticated users. Should I try cache tags?

**Response:**

> **Verdict: Probably not as your first move. Cache tags control *when* cached items get invalidated. They don't make anything faster on their own. First find out why the page isn't being served from cache or where the render time goes, then fix that. The fix is usually cache *contexts* and *max-age*, plus lazy builders, not tags.**
>
> **Real objective:** Cut server time for logged-in users. In Drupal that usually means getting most of the page out of the Dynamic Page Cache and render cache, and isolating the truly per-user bits. Adding tags helps only if things are cached but being cleared too broadly. A typical example is everything tagged `node_list`, so any node save wipes it all.
>
> **Likely culprits, roughly in order:**
> 1. **Cacheability killed by bubbling.** One block or field with `max-age: 0` or a `user` context makes the whole page uncacheable for everyone. Fix it by moving that piece into a `#lazy_builder` placeholder so BigPipe or auto-placeholdering handles it.
> 2. **Slow uncached work.** Heavy Views, N+1 entity loads, or external API calls during render. Fix the query or cache that specific render array.
> 3. **Over-broad invalidation.** This is the case where tags matter. Swap generic list tags for narrower custom tags so caches survive unrelated edits.
> 4. **Infrastructure.** Cache bins sitting in MySQL instead of Redis or Memcache, or no OPcache.
>
> **Ruled out:**
> - **Internal Page Cache:** it serves anonymous users only.
> - **Varnish or a CDN:** per-user HTML makes full-page caching impractical without ESI complexity.
>
> **What would flip the verdict:** If profiling shows the page *is* cached but gets invalidated constantly, then yes, targeted cache tags are the fix.
>
> **Cheapest check (about 15 minutes):**
> - Turn on `http.response.debug_cacheability_headers: true` in `services.yml` and load the page logged in.
> - Look at `X-Drupal-Dynamic-Cache`. If it says `UNCACHEABLE`, check `X-Drupal-Cache-Contexts` and `X-Drupal-Cache-Max-Age` to see what's poisoning it.
> - If it's a `HIT` and the page is still slow, profile with XHProf, Blackfire, or Webprofiler to find the slow code.
>
> **First step:** Enable the cacheability debug headers and tell me what `X-Drupal-Dynamic-Cache`, the cache contexts, and the max-age show. That tells us which of the four culprits you have.

---

### Choosing a Redis eviction policy for Drupal cache bins

**Request:** `/advise-me` Our Drupal site's Redis keeps running out of memory. Which eviction policy should I set for the cache bins: allkeys-lru or volatile-lru?

**Response:**

> **Verdict: `allkeys-lru`, provided this Redis instance holds only Drupal cache bins.** `volatile-lru` will likely leave you still hitting OOM.
>
> **Why:** Drupal stores most cache items as `CACHE_PERMANENT`. The redis module writes those with no TTL unless you configure `perm_ttl`. `volatile-lru` only evicts keys that have a TTL, so most of your data can't be evicted. Redis then returns OOM errors on writes, which matches what you're seeing.
>
> **What would flip it:** If the same instance also holds locks, flood control, queues, or sessions (the redis module can back all of these), `allkeys-lru` may evict them. That can break locking or log users out. Don't switch to `volatile-lru` to protect them. Move them to a separate Redis instance with `noeviction`, and keep the cache instance on `allkeys-lru`. Different databases on the same server don't help, because eviction policy applies to the whole server.
>
> **The real problem may be growth, not eviction.** If memory fills quickly, check what's filling it before tuning eviction:
> - Look at `render`, `page`, and `dynamic_page_cache`. Cache contexts keyed on URL query args, user, or session can create near-unique entries per request, and bots crawling faceted or search URLs make this much worse.
> - Run `redis-cli --bigkeys` and sample keys by bin prefix (`SCAN` with a match pattern) to see which bin dominates.
> - If the anonymous page cache is the culprit, consider putting a CDN or Varnish in front, or setting `perm_ttl` on that bin.
>
> **Cheapest check:** Run `redis-cli INFO keyspace`. Compare `expires` with `keys`. If `expires` is much smaller, `volatile-lru` has almost nothing it can evict.
>
> **First step:** Make sure `maxmemory` is actually set, since with no limit Redis grows until the OS kills it. Then set:
>
> ```
> maxmemory-policy allkeys-lru
> ```
>
> On Redis 4+, `allkeys-lfu` is often slightly better for cache workloads, because it protects frequently hit items from a burst of one-off entries.

---

### Moving Drupal search to Solr

**Request:** `/advise-me` Should we move our Drupal site search from the core database backend to Solr?

**Response:**

> **Verdict:** Probably not yet. Move to Solr only if you have a concrete search problem the database backend can't fix. If your host already includes managed Solr (Pantheon, Acquia, Platform.sh), the cost of switching drops enough that "yes" becomes reasonable once you've confirmed the problem. Which of those applies to you would change my answer.
>
> **Real objective:** Search that returns good results, fast, at your content volume, without adding infrastructure your team has to run. "Solr" is one way to get there. The question is whether your current setup actually falls short.
>
> **Does the premise hold?** First check which "database backend" you mean:
> - **Drupal core Search module:** Its relevance and faceting are weak. Before considering Solr, move to **Search API + Database backend**. It's often enough, and it uses the same Search API layer you'd need for Solr anyway, so nothing is wasted.
> - **Search API Database backend:** This is fine for most sites up to roughly tens of thousands of items, with modest facet use. It degrades with large indexes, many facets, heavy concurrent queries, or when you need stemming, fuzzy matching, synonyms, or multilingual analysis.
>
> **Ruled out:**
> - *Self-hosted Solr:** You'd take on a JVM service, schema/config versioning, backups, and upgrades. That's rarely worth it unless you already run Solr.
> - *Elasticsearch/OpenSearch:* Drupal integration is less mature than search_api_solr. Choose it only if your organization already runs it.
> - *Typesense/Meilisearch:* Pleasant to use, but their Drupal modules are less proven. Consider them only for a simple site where typo tolerance is the main gap.
> - *Staying on core Search module as-is:* If that's what you're on, the cheap upgrade to Search API DB should come first.
>
> **What would flip it to "move to Solr now":**
> - Search queries are slow, or show up in your slow query log or DB load.
> - Users report bad results that stemming, synonyms, or fuzzy matching would fix.
> - You need many facets or multilingual search.
> - Your host already provides Solr at no extra ops cost.
>
> **Cheapest checks:**
> 1. Count indexed items and check p95 search query time. Use the slow query log or New Relic, and look at the `search_api_db_*` tables.
> 2. Pull the top 50 search terms from your logs, including zero-result searches, and judge the results by hand. Bad results caused by missing stemming or synonyms point to Solr. Bad results caused by field boosts or the wrong fields being indexed can be fixed on your current backend.
>
> **First step:** Tell me three things: which backend you're on (core Search or Search API DB), your rough item count, and your host. Then run the zero-result and top-terms review. That review will tell you whether you have a relevance problem, a performance problem, or no problem.

---

## No proposal

_The user brings only a need or a symptom. The skill tests its own first idea instead of the user's._

### Building a survey in Drupal

**Request:** `/advise-me` I need to build a survey in Drupal. What should I do?

**Response:**

> **Verdict:** Use the **Webform** module. It's the standard Drupal answer and it's the right one here. It handles multi-page surveys, conditional logic, likert/matrix questions, validation, email notifications, CSV export, and access control without custom code.
>
> **One fact could change this:** if the survey is a one-off and the results don't need to live in Drupal (no tie to user accounts or site data, no Drupal permissions), an embedded Google Forms or Microsoft Forms survey is less to own. You'd have no module to keep updated and no submission data to secure on your server. The exception is data you're obligated to keep in-house, such as personal data under GDPR or institutional policy. That kind of data belongs in Webform.
>
> **What's the survey for, and who needs the results?** That answer decides between the two.
>
> **Avoid:** building it from a custom content type or a custom form. You'd be rebuilding what Webform already does: conditional logic, submission storage, and exports.
>
> **First step (Webform path):**
> 1. Run `composer require drupal/webform`, then `drush en webform webform_ui`.
> 2. Go to **Structure → Webforms → Add webform**.
> 3. Add elements. Use the *Likert* element for rating scales and the *Conditions* tab for branching.
> 4. Under **Settings → Submissions**, decide whether to allow anonymous or repeat submissions and how long to keep data.
> 5. Submit a few test entries, then export them under **Results → Download** before you launch.

---

### Stopping bots from overwhelming a Drupal site

**Request:** `/advise-me` Help me prevent bots from overwhelming my Drupal site.

**Response:**

> **Verdict:** Stop the bots *before* they reach Drupal. Put a CDN/WAF in front of the site (Cloudflare's free or Pro plan is the usual choice), turn on its bot protection, and make anonymous pages cacheable at the edge. Don't rely on Drupal modules for this. Every request a module blocks has already bootstrapped PHP and Drupal, and that bootstrap is the load you're trying to avoid.
>
> **Real objective:** You probably don't need to stop bots. You need bot traffic to stop costing you origin CPU and database time. Most sites "overwhelmed by bots" are really being crawled through **uncacheable URLs**: faceted search, Views exposed filters, `?page=`/sort parameters, and search pages. Each of these generates near-infinite unique URLs that miss the cache. AI scrapers (GPTBot, ClaudeBot, Bytespider, Amazonbot, and many unlabeled ones) walk these endlessly. Remove that cause and most of the problem goes with it.
>
> **What to do, in order:**
> 1. **Check your logs first** (see below). This tells you which case you're in.
> 2. **Edge layer:** Cloudflare (or Fastly/Akamai if your host bundles it). Enable Bot Fight Mode or "Block AI bots." Add a rate-limit rule on expensive paths like `/search`, facet URLs, `/user/login`, and `/user/register`. Use a Managed Challenge for traffic from data-center ASNs on those paths.
> 3. **Close crawler traps:** Add `rel="nofollow"` to facet links (Facets module setting). Disallow facet and search query patterns in `robots.txt`. Have the edge challenge or block requests with too many query parameters. This makes the bot problem much smaller instead of just fighting it.
> 4. **Make anonymous traffic cheap:** Enable Internal Page Cache, set the page cache max-age (e.g., 1h+) so the CDN caches HTML, and make sure no stray session cookie is breaking caching.
>
> **Ruled out:**
> - *Drupal-only modules* (Perimeter, Ban, Flood control tweaks) are too late in the request to save resources. They're fine as a backstop, not as the fix.
> - *CAPTCHA everywhere* hurts real users and doesn't address crawl load. Keep it to forms only (Antibot or Honeypot, plus CAPTCHA/Turnstile if form spam is the problem).
> - *IP blocklists by hand* don't work because scrapers rotate residential and cloud IPs faster than you can update the list.
> - *robots.txt alone* is ignored by the abusive bots. It's useful only for the polite ones.
>
> **What would flip the verdict:**
> - **Form spam or fake registrations rather than load:** use Honeypot + Antibot, plus Turnstile on registration. You can skip the CDN work.
> - **Credential stuffing on `/user/login`:** rate-limit that path at the edge, tighten Drupal's flood settings, and consider requiring 2FA for privileged roles.
> - **You can't put a CDN in front** (contract or host restriction): rate-limit at nginx/Apache (`limit_req`) and in Varnish if your host has it. This is the same principle one layer closer.
> - **You're on Pantheon/Acquia/Platform.sh:** they already have an edge. Use their WAF and bot rules instead of adding Cloudflare on top.
>
> **Cheapest check:** From access logs, pull the top user agents, top IPs/ASNs, and top URL patterns over 24h. For example:
> `awk '{print $7}' access.log | sed 's/\?.*//' | sort | uniq -c | sort -rn | head`
> Then compare the hits per path with and without query strings. If most of the load is query-string variants of a few listing pages, it's a crawler trap (step 3 is the main fix). If it's one path like `/user/login`, it's an attack (edge rate limiting is the main fix).
>
> **First step:** Run that log analysis, then tell me three things: what dominates the traffic (crawlers, form spam, or login attempts), whether you use Search API/Facets, and who hosts the site. I'll turn this into exact rules.

---

## A cheaper path exists

_The user's plan works, but removing the cause beats it._

### A Slack bot to remind an approver who is often traveling

**Request:** `/advise-me` Every content change on our Drupal site needs approval from one manager, who is often traveling, so publishing takes days. I want to build a Slack bot that reminds her about pending approvals. Thoughts?

**Response:**

> **Verdict:** Don't build the bot yet. The bottleneck is that every change waits on one person. A reminder doesn't change that, because she'll still be on a plane. Instead, reconfigure Drupal's approval workflow so most changes don't need her, and add a backup approver. Drupal's core Workflows and Content Moderation modules handle this with configuration, not custom code.
>
> **Real objective:** Get content published quickly while keeping oversight where it matters. Your premise is that she must approve everything, and only she can. That's the part to question. Pinging a traveling approver more often adds noise without adding capacity.
>
> **What I'd do instead:**
> 1. **Tier the approvals.** Let trusted editors publish low-risk edits (typo fixes, date updates, minor copy) directly or with peer review. Keep manager approval for high-risk content such as legal, pricing, press, or new pages. You can do this with separate workflow transitions and role permissions, or with separate content types or workflows.
> 2. **Add a delegate.** Give a second person the approver role so items move when she's away.
> 3. **Optional:** Add a daily digest notification. Existing contrib modules for email or Slack notifications can cover this without writing a bot.
>
> **What I ruled out:**
> - **Slack reminder bot:** It doesn't add approval capacity, it's custom code you'd have to maintain, and it adds to the noise she's already dealing with.
> - **Removing approval entirely:** It gives up oversight on content that may need it.
> - **Auto-publish after X days without a response:** It quietly defeats the purpose of having approval at all.
>
> **What would flip the verdict:** If a regulation, contract, or policy requires her personal sign-off on every change (common in regulated industries), you can't tier or delegate. In that case, make approving as easy as possible: send a notification with a one-click link to the moderation view that works on her phone. Use an existing notification module first, and only build a bot if that falls short.
>
> **Cheapest way to check the key assumptions:**
> - Ask her, or compliance, whether she must approve *everything* personally or whether that's just how it was set up.
> - Pull the last month of pending items and count how many were trivial. If most were, tiering removes most of the delay.
>
> **First step:** Ask her this week: "Which kinds of changes do you actually need to see, and who could approve while you're away?" Then map her answer onto Drupal's workflow states and permissions.

---

## Sound plans

_Controls. The plan is right, so the skill should agree and add only what is missing._

### Sending Drupal mail through SendGrid to stop spam-foldering

**Request:** `/advise-me` Our Drupal 10 site sends password reset emails with PHP mail() and they land in spam. I plan to switch to the Symfony Mailer module and send through our company's existing SendGrid account. Good plan?

**Response:**

> #### Verdict: Yes, with two caveats
>
> Routing through SendGrid is the right move. It only fixes spam placement, though, if SendGrid is authenticated for the domain in your From address. The relay alone does not do that.
>
> **Real problem:** Mail is landing in spam because it fails authentication, not because of the PHP mail() function itself. Mail sent with `mail()` from a web server usually has no valid DKIM signature and no SPF alignment for your From domain, so receivers distrust it. SendGrid fixes this only if:
>
> 1. **Domain Authentication is set up in SendGrid** for the exact domain in the site's From address (Drupal's site email). This means adding the CNAME records SendGrid gives you, which provide DKIM and a custom return-path.
> 2. **That domain has a DMARC record**, at least `p=none` to start, and the site's From address uses that domain. If the From is `noreply@webserver.example.net` and only `example.com` is authenticated, you'll still land in spam.
>
> #### Things that will bite you
>
> - **Click tracking:** Turn it off for these messages. SendGrid rewrites links by default, which turns your one-time reset URL into a tracking redirect. That looks phishy to filters and users and can break token links.
> - **Shared reputation:** If marketing sends bulk mail from the same account, your resets share its reputation and suppression lists. A user who unsubscribed from newsletters can silently stop getting resets. Use a separate subuser for transactional mail, or at least a separate unsubscribe group with suppressions bypassed for this mail.
> - **API key handling:** Create a key with Mail Send permission only. Put it in `settings.php` or an environment variable, not in exported config that gets committed to git.
> - **Module choice:** Recent Drupal 10 releases include a Symfony Mailer-based transport in core, configured through `system.mail`'s `mailer_dsn` setting. Check your minor version. If you have it, you may not need the contrib module at all, unless you want its UI or HTML templating.
>
> #### Cheapest check
>
> After switching, send a reset to a Gmail address and use "Show original." You want SPF, DKIM, and DMARC all to say **PASS**, with DKIM `d=` matching your From domain. Or send a reset to mail-tester.com for a score.
>
> #### First step
>
> Ask whoever owns the SendGrid account whether Domain Authentication is already done for your site's From domain. If it is, the switch is about an hour of work. If not, the DNS records are the actual fix.

---

### A Drupal 10 to 11 upgrade plan

**Request:** `/advise-me` I'm upgrading a Drupal 10 site to 11. Plan: run Upgrade Status, fix deprecated code in our custom modules, move contrib to D11-compatible releases, then bump core with Composer and run updb. Anything wrong with that?

**Response:**

> **Verdict:** The plan is right and is the standard path. It leaves out a few prerequisites, and those are where D10→11 upgrades usually go wrong. Add them and proceed.
>
> **Gaps to close:**
>
> 1. **Get to the latest Drupal 10 first (10.3 at minimum, ideally latest 10.x), and run `updb` there.** Drupal 11 drops older update hooks, so skipping this step can leave the database un-updatable.
> 2. **Check platform requirements before bumping core.** You need PHP 8.3+, MySQL 8.0 / MariaDB 10.6 / PostgreSQL 16 (or SQLite 3.45), and Drush 13. Hosting is the most common surprise. Upgrade Status will flag these, but verify them on production, not just locally.
> 3. **Handle modules and themes removed from core.** Drupal 11 removed Actions UI, Book, Forum, Statistics, Tracker, and others, plus some deprecated themes. If any are enabled, either `composer require` the contrib replacement before the core bump or uninstall them on D10. Upgrade Status reports these.
> 4. **Plan for contrib that isn't D11-ready.** Some contrib modules will have patches but no tagged D11 release. Use `mglaman/composer-drupal-lenient` together with the patches rather than holding the whole upgrade.
> 5. **Use drupal-rector for your custom modules.** It fixes most deprecations automatically, so you hand-fix only what remains. Also bump `core_version_requirement` to `^10 || ^11`. That lets the code run on both versions, so you can deploy it before the core switch.
> 6. **Bump core packages together:** `composer require drupal/core-recommended:^11 drupal/core-composer-scaffold:^11 drupal/core-project-message:^11 -W` (include `drupal/core-dev` if you use it). Without `-W`, the dependency resolver will block you.
> 7. **After `updb`:** run `drush cr`, then `drush config:export` and commit the config. Also review `settings.php` and services changes from the scaffold diff.
>
> **Cheapest check:** On a copy of the production database, run Upgrade Status and `composer why-not drupal/core 11`. Together they list nearly every blocker before you touch anything.
>
> **First step:** Back up, then update to the latest 10.x and confirm `updb` runs clean.

---

## Small questions

_Low stakes get a short answer._

### Naming a variable

**Request:** `/advise-me` Should I name this PHP variable $userCount or $numUsers?

**Response:**

> **Verdict:** `$userCount`. If your codebase already uses the `$numX` pattern, use `$numUsers` instead, because consistency matters more than the choice itself.
>
> **Real problem:** The name matters less than whether it matches its neighbors. `$userCount` has a slight edge on its own merits:
> - It mirrors PHP's own `count()`, so `$userCount = count($users);` reads naturally.
> - It sorts and autocompletes next to `$users` and `$userList`.
> - It extends cleanly to `$activeUserCount`. `$numActiveUsers` works too but reads less smoothly.
>
> **First step:** Search the project for `Count =` and `$num` to see which pattern already dominates, and follow it. If neither does, use `$userCount`.

---

## Follow-up pushback

_The user disagrees without new facts. The skill keeps its position and says what would change it._

### "My tech lead says cache tags are the answer"

_This simulates a second turn: the earlier exchange is summarized inside the request._

**Request:** `/advise-me` Earlier I asked: 'Should I try cache tags to speed up a slow Drupal page for logged-in users?' You said cache tags only control invalidation and I should first check the X-Drupal-Dynamic-Cache header and profile the request. My tech lead says cache tags are the answer. I'm going with cache tags; just confirm that's fine.

**Response:**

> **Verdict:** I can't confirm it yet. Your tech lead's view isn't new evidence about the page, so my answer stands. But there is one situation where your lead is right, and you can check for it in about five minutes.
>
> **Where cache tags do fix it:** Tags don't make anything faster on their own. They tell Drupal when to throw cached content away. They help speed only when some code turns caching off because it has no other way to invalidate. Typical examples are `#cache['max-age'] = 0`, `\Drupal::service('page_cache_kill_switch')->trigger()`, or a custom block that skips caching "so it's never stale." In those cases, adding proper tags such as `node:123` or `config:my_settings` lets you remove the kill switch and cache safely. If that's what your lead means, go ahead.
>
> **Where they don't help:**
> - **The slowness comes from a per-user cache context.** `user` or `session` contexts give each user a separate cache entry, so logged-in users keep missing the cache. The fix is narrower contexts, such as `user.roles` or `user.permissions`, or lazy builders / BigPipe for the truly personal parts.
> - **The time is spent on an uncached cost.** A slow query, an external API call, or a heavy preprocess function will be just as slow with tags. Fix or lazy-build that code instead.
>
> **Cheapest check:**
> 1. Load the page as a logged-in user and look at `X-Drupal-Dynamic-Cache`. You'll need `http.response.debug_cacheability_headers: true` in `services.yml` to see the related headers.
>    - `UNCACHEABLE` means something is setting max-age 0 or a high-cardinality context. Check `X-Drupal-Cache-Max-Age` and `X-Drupal-Cache-Contexts`.
>    - If max-age is 0 because of missing invalidation, your lead is right: add the tags and remove the max-age 0.
>    - If the contexts include `user` or `session`, cache tags won't fix it.
>    - `HIT` but still slow means the cost is outside the render cache. Profile with Webprofiler, XHProf, or Blackfire.
> 2. Show your lead the result. It settles the question either way.
>
> **First step:** Enable the cacheability debug headers and load the page while logged in.
