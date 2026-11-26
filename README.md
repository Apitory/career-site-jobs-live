# How to get live job listings from company career sites via API

You need current job postings from a specific set of companies — for a niche job board, competitor hiring monitoring, or market research — and you need them as they are *now*, not as an aggregator indexed them hours or days ago. This guide shows the usual routes, where they grind, and the direct route.

## The usual ways, and their friction

- **Checking boards by hand** works for three companies, not thirty. Every ATS (Greenhouse, Ashby, Lever, SmartRecruiters, Workable) has its own board layout, and postings open and close daily.
- **Indexed job APIs** serve a pre-built database: jobs are crawled, indexed, and served from a snapshot that can lag behind the real board, with values derived along the way — sometimes wrongly. You can get postings that closed yesterday and miss ones that opened this morning, because the crawler has not visited yet.

## The Actor way

The [Career Site Jobs Actor](https://apify.com/apitory/career-site-jobs-live) on Apify skips the index entirely. Every run calls the companies' own ATS boards directly — via the official public job-board APIs of Greenhouse, Ashby, Lever, SmartRecruiters, and Workable — seconds before the data lands in your dataset. No HTML scraping, no proxies, no login. One normalized schema across all five sources, and every record carries a `fetchedAt` timestamp, so diffing consecutive runs shows you roles opening and closing in real time.

Quickstart input (Console or Apify API, same JSON):

```json
{
  "companies": [
    { "ats": "greenhouse", "companyId": "stripe" }
  ],
  "maxResults": 100
}
```

That is all a first run needs — one company on one ATS. The `companyId` is the board slug: `boards.greenhouse.io/<slug>`, `jobs.ashbyhq.com/<slug>`, `jobs.lever.co/<slug>`, `jobs.smartrecruiters.com/<Company>`, `apply.workable.com/<slug>`. Add any mix of companies, plus optional keyword (title / department / team), location, and description-text filters.

Two source quirks, stated plainly: Ashby and Lever feeds do not carry the company display name (use `companyId` there), and SmartRecruiters and Workable list feeds do not carry description text, so those records return `null` for `descriptionText`.

## Sample output

One real record from a live run — the full record is in [`sample-output.json`](sample-output.json) (`descriptionText` trimmed, as in the listing):

```json
{
  "ats": "greenhouse",
  "companyId": "stripe",
  "company": "Stripe",
  "jobTitle": "Abuse Investigator",
  "url": "https://stripe.com/jobs/search?gh_jid=8172510",
  "location": "Seattle, San Francisco, New York City",
  "department": "8611 Security Analytics",
  "postedAt": "2026-09-09T10:50:29-04:00",
  "fetchedAt": "2026-11-23T09:37:53.216Z"
}
```

## What people use it for

- **Competitor hiring monitoring** — watch a fixed list of companies; diff consecutive runs to see roles open and close in real time.
- **Niche job boards** — power a board for one industry or role family from live source data instead of a stale aggregator feed.
- **Lead generation and market research** — who is hiring, for what, and where, straight from the source.

## Pricing

Pay per result: **$3.00 per 1,000 jobs** ($0.003 per record), plus a minimal Actor start fee. The run summary is not charged — it goes to the Key-Value store, not your dataset.

## Run it

**[Career Site Jobs — Live ATS API — Apify Store](https://apify.com/apitory/career-site-jobs-live)**

Support: apitoryhq@gmail.com.

---

## More from Apitory

Same family, different cut:

- [Greenhouse Jobs — Live API](https://github.com/Apitory/greenhouse-jobs-live-api) — run it on Apify: https://apify.com/apitory/greenhouse-jobs-live-api

Full catalogue: https://apitory.com · All Actors: https://apify.com/apitory · All projects: https://github.com/Apitory
