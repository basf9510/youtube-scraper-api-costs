# YouTube Scraper API: What You Actually Get, What 1,000 Videos Cost, and Whether a General Scraper API Is Enough (ScraperAPI Plans Compared)

No single YouTube scraper API returns keyword search results, video details, channel history, comments and transcripts in one call. Pick by data layer. SerpApi covers keyword search and video pages with typed fields, and routes comments and transcripts to separate requests. Bright Data runs separate videos, channels and comments datasets, with 5,000 free credits every month for new accounts. Apify charges $1.00 per 1,000 videos and leaves likes, comments and transcripts out.

The billing units differ enough to make a “cheapest per 1,000 videos” comparison meaningless. Apify bills per stored record, Bright Data per credit, SerpApi per search with cached hits given away free. Three different meters, three different totals.

ScraperAPI sits in another category: a general-purpose scraping API with no published YouTube endpoint. Its credits buy page requests, not parsed records. That changes both the cost math and the amount of code you write before the first row lands in your database.

> **Short answer for most readers:** if you need typed YouTube fields this week, use a YouTube-specific API and budget per record. If YouTube is one site inside a wider crawl and you already parse HTML or JSON, route those pages through ScraperAPI. The trial runs 7 days with 5,000 credits and no credit card, and a standard page request costs 1 credit.
> 👉 [Start the ScraperAPI 7-day trial with 5,000 credits, no card required](https://www.scraperapi.com/pricing?fp_ref=coupons)

## ScraperAPI’s plans: credits, concurrency and price

ScraperAPI sells request volume. Every plan includes the same core feature set: JS rendering, premium proxies, rotating proxy pools, JSON auto-parsing, CAPTCHA and anti-bot handling, custom sessions and headers, automatic retries, unlimited bandwidth and a 99.9% uptime guarantee. What changes between tiers is the monthly credit allowance, concurrency, geotargeting scope and analytics retention.

| Plan | API credits / month | Concurrent threads | Price | Start |
| --- | --- | --- | --- | --- |
| Free | 1,000 | 5 | $0 | [ Sign up](https://www.scraperapi.com/pricing?fp_ref=coupons) |
| Hobby | 100,000 | 20 | Shown on the live pricing page | [ Start](https://www.scraperapi.com/pricing?fp_ref=coupons) |
| Startup | 1,000,000 | 50 | $149/mo, or $134.10/mo billed annually ($1,609.20/yr) | [ Start](https://www.scraperapi.com/pricing?fp_ref=coupons) |
| Business | 3,000,000 | 100 | Shown on the live pricing page | [ Start](https://www.scraperapi.com/pricing?fp_ref=coupons) |
| Scaling (most popular) | 5,000,000 | 200 | $475/mo, or $427.50/mo billed annually ($5,130/yr) | [ Start](https://www.scraperapi.com/pricing?fp_ref=coupons) |
| Professional | 10,500,000 (+250K bonus, limited offer) | 300 | $975/mo, or $877.50/mo billed annually ($10,530/yr) | [ Start](https://www.scraperapi.com/pricing?fp_ref=coupons) |
| Advanced | 21,500,000 | 500 | Shown on the live pricing page | [ Start](https://www.scraperapi.com/pricing?fp_ref=coupons) |
| Enterprise | 22,000,000+ | 500+ | Custom quote | [ Start](https://www.scraperapi.com/pricing?fp_ref=coupons) |

Three things worth knowing before you pick a row. Prices for Hobby, Business and Advanced appear only on the live page, not in the published pricing data I could verify, so confirm those figures before you commit. Annual billing takes 10% off across paid plans. And credits reset at renewal: there is no rollover on any tier, so an oversized plan is wasted spend rather than a savings account.

Geotargeting is the difference that bites YouTube research. Free, Hobby and Startup cover US and EU locations only. Country-level targeting starts at Business, which matters if you need search results or video pages as seen from Japan, Brazil or India. Geotargeting itself adds no credits; access to it is a plan feature.

| Tier | Geotargeting | Analytics history | Pay-as-you-go | Auto-upgrade |
| --- | --- | --- | --- | --- |
| Free, Hobby, Startup | US and EU only | 30 days | Not available | Opt-in (paid tiers) |
| Business | Country-level | Unlimited | Not available | Opt-in |
| Scaling, Professional | Country-level | Unlimited | Available | Opt-in |
| Advanced, Enterprise | Country-level | Unlimited | Available | Not available |

### What a credit actually buys on a YouTube request

ScraperAPI prices each request from a published credit table, and the multipliers are where budgets go wrong.

| Request type | Credit cost |
| --- | --- |
| Standard page | 1 |
| Amazon, Walmart, eBay | 5 |
| Google, Bing (including subdomains) | 25 |
| LinkedIn | 30 |
| Bot-protection bypass (Cloudflare, DataDome, PerimeterX, Akamai) | 10 |
| JS rendering (`render=true`) | 10 |
| Premium proxies (`premium=true`) | 10 |
| Screenshot | 10 |
| Premium proxies + JS rendering (published combination rate) | 25 |
| Ultra Premium (paid plans only) | 30 |
| Ultra Premium + JS rendering (paid plans only) | 75 |
| Geotargeting, sessions, device type, output format, auto parsing | No extra cost |

YouTube is not on the multiplier list. A YouTube page request should therefore bill as a standard page at 1 credit, plus the published add-on rates if you turn on rendering or premium proxies. Do not add those rows together yourself: ScraperAPI publishes specific combination rates (premium proxies plus rendering is 25, not 20), so budget from the published line, not from arithmetic. Every response carries its own cost in the `sa-credit-cost` header, the dashboard’s Domain Cost Estimator prices any URL before you send it, and `max_cost` caps what a single request can burn.

👉 [See the current plan prices and credit allowances on ScraperAPI’s pricing page](https://www.scraperapi.com/pricing?fp_ref=coupons)

## Which layer of YouTube data do you need?

Four layers get lumped together under “YouTube scraping”, and tools cover them unevenly. Search results, video details, channel profiles and upload lists, comments, and transcripts each need a different endpoint or dataset.

| Data layer | SerpApi | Bright Data | Apify | ScraperAPI |
| --- | --- | --- | --- | --- |
| Keyword search results | Yes, `engine=youtube` | Yes, videos by keyword or hashtag | Yes, first page only (~20 per term) | No typed endpoint |
| Video details (title, views, likes) | Yes, `engine=youtube_video` | Yes, videos dataset | Yes, for direct video URLs | No typed endpoint |
| Channel profile (subscribers, video count) | Yes, channel results array | Yes, channels dataset | Partial, no subscriber count | No typed endpoint |
| Comments | Yes, separate request with its own token | Yes, comments dataset | Not included | No typed endpoint |
| Transcripts | Yes, separate transcript engine | Not in the three datasets | Not included | No typed endpoint |

**Search results.** SerpApi’s `youtube` engine returns `video_results` with title, link, `video_id`, channel object (name, link, verified flag, thumbnail), `published_date`, parsed views, duration label, description snippet and badge extensions, alongside `shorts_results`, `channel_results`, `playlist_results` and `ads_results` as separate arrays. Apify’s search source returns roughly the first 20 videos per term and stops there. If your keyword monitor needs the long tail, that cap decides the architecture: more specific terms, not more pages.

**Video pages.** SerpApi’s `youtube_video` engine takes a video ID and returns title, channel with subscriber counts, views, likes, live status, publication date, description links, chapters, related videos, end-screen videos, shopping results and comment counts. Chapters arrive in seconds.

**Channels.** Bright Data’s channels dataset returns subscriber counts, `videos_count`, description, verification status and creation date; its example record for a large creator shows the shape, and those numbers are documentation samples, not live figures. Apify’s channel source returns the latest uploads from the public feed, up to 15 per channel, with ISO `publishedAt` but no subscriber count.

**Comments and transcripts.** These are the two layers general scrapers and video-URL enrichment tools skip. Apify states plainly that likes, comments and transcripts are not included. SerpApi serves comments through the video endpoint with a dedicated pagination token, and transcripts through a separate transcript engine that reports timestamps in milliseconds while the video endpoint reports chapters in seconds. Bright Data keeps comments in their own dataset.

ScraperAPI appears in the last column for one reason: nothing in its published product or pricing material describes a YouTube endpoint, a YouTube dataset or a typed YouTube schema with fields like `video_results` or `comments`. You send a URL and get the response body back. Everything above that layer is your parser.

## Why “cost per 1,000 videos” is not a fair fight

Run the same 1,000-video job through three vendors and you are billed three different ways.

Apify charges **$1.00 per 1,000 videos** stored, plus $0.00005 per Actor start per GB of memory. Duplicates removed by dedupe are not stored and not billed, so overlapping search terms cost nothing extra. Bright Data gives new accounts **5,000 free credits a month, no card**, described as up to 5,000 YouTube records, and does not publish a per-credit price in the material I could verify, which means you cannot compute a marginal cost per record from public pages alone. SerpApi bills per search, and identical requests that hit its one-hour cache are **free and do not count toward monthly searches**, which quietly rewards cron jobs that re-run the same query.

ScraperAPI bills per request. At 1 credit for a standard page, the 1,000,000-credit Startup plan covers up to a million page fetches a month for $149. On credit count alone that is the cheapest per page of anything here. The catch sits outside the meter: a raw YouTube response is not a row. You pay for that difference in engineering time, in parser maintenance, and in the validation pass you owe any pipeline that depends on an undocumented response shape.

That is the honest trade. Per-record pricing buys you a schema. Per-request pricing buys you volume.

## Five things that break a YouTube scraping pipeline

1. **Search coverage caps.** Apify returns the first page per search term and the latest 15 uploads per channel. Keyword monitoring built on one broad term will silently miss most uploads.
2. **Continuation tokens in the wrong parameter.** SerpApi’s search endpoint expects the next-page token in `sp`, replacing the previous value. The `next_page_token` from a channel API is a different token and will not work there. Wrong parameter, infinite page one.
3. **Four token types on one video endpoint.** Related videos, comments, comment sorting and replies each have their own token key, and you send one per request. Mixing comment and reply pagination state produces comment threads attached to the wrong parent.
4. **Synchronous requests that quietly go asynchronous.** Bright Data’s synchronous endpoint waits one minute, then returns HTTP 202 with a `snapshot_id` while the job keeps running. Code that treats anything other than 200 as a failure throws away work that actually completed.
5. **Field semantics that look like clean data.** Publication labels can be relative strings such as “3 days ago” rather than timestamps, missing fields are not zeros, `position_on_page` is a position on that page and not a global rank, and public view counts are rounded or delayed compared with YouTube Studio. Dashboards built on those four assumptions show fake zeros and rank charts that reset on every page.

## Where ScraperAPI fits, and where it does not

It fits when YouTube is one target among many. A single credit system covering standard pages at 1 credit, Google and Bing at 25 and bot-protected sites at 10 handles a mixed crawl of SERPs, competitor sites and YouTube pages on one bill, with one set of retries and one proxy pool. It fits when you want your own schema, because a parser you own can carry fields no vendor exposes. It fits when you need concurrent threads: 200 on Scaling and 300 on Professional is a lot of parallel fetching for the money.

It does not fit when a typed YouTube record is the deliverable. There is no published endpoint returning `video_results`, no comment dataset, no transcript engine. It also does not fit small teams without parsing capacity: a raw response per video is not an analysis.

Two operational limits to plan around. Exceeding your concurrency returns HTTP 429 and the request is turned away rather than queued, with no overage charge. Pay-as-you-go, which keeps requests flowing past your credit limit at a fixed per-credit rate up to a cap you set, starts at Scaling and is unavailable on Free, Hobby, Startup and Business.

## Which one to pick

Match the tool to the layer, not to the brand:

- **Keyword monitoring with typed video metadata** → a search engine endpoint. SerpApi’s one-hour cache makes repeated queries cheap; Apify at $1 per 1,000 videos works if the first page per term is enough.
- **Comments plus transcripts for semantic or sentiment work** → SerpApi’s video and transcript engines, or Bright Data’s comments dataset. Apify will not do it.
- **Bulk channel profiles and subscriber history** → Bright Data’s channels dataset, and use the 5,000 monthly free credits to size the job before paying.
- **Enriching a known list of video and Shorts URLs at volume** → per-record pricing, where Apify’s $1.00 per 1,000 is easy to forecast.
- **Mixed-site crawling, custom schema, country-level geotargeting** → ScraperAPI. Startup at $149/month covers up to a million standard page requests, which is more YouTube volume than most teams parse in a month. Move up only when you need non-US/EU geotargeting (Business) or concurrency past 50.

Before you enter a card number anywhere on this list, confirm three things for ScraperAPI specifically: the trial gives you 5,000 credits over 7 days with no card, so you can price your real workload first; credits do not roll over at renewal; and there is a 7-day, no-questions-asked refund policy if the request volume does not translate into usable data.

👉 [Compare ScraperAPI plans and start the 7-day trial with 5,000 credits](https://www.scraperapi.com/pricing?fp_ref=coupons)
