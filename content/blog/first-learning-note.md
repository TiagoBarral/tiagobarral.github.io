---
title: "What Oportuno Is <em>Teaching Me</em>."
date: 2026-04-25
summary: "Early lessons from turning an AI-assisted prototype into a product-shaped system."
tags: ["Oportuno", "Learning", "Production"]
---

Oportuno started as a direct workflow: search for local businesses, classify their digital presence, scrape public contact details, generate an outreach email, and send it. That first version was useful because it made the idea visible. It was also useful because it showed me, very quickly, where the idea stopped being a demo.

The early prototype could get away with being optimistic. A product cannot. Once the workflow touched slow scraping, uncertain email discovery, AI scoring, and delivery state, the interesting work stopped being "can this generate a good draft?" and became "can this system explain what happened when something goes wrong?"

## The prototype was _too honest_.

The first version did everything in one line of thought. The user clicked search, the server fetched companies, the scraper tried to find signals, Claude scored the opportunity, and the UI waited for the whole story to finish. It was refreshingly simple. It also had no memory.

{{< pullquote cite="Oportuno note, April 25" >}}
The moment a prototype touches slow work, it stops being a demo and starts asking for architecture.
{{< /pullquote >}}

The uncomfortable part was that nothing looked broken at first. Locally, requests completed. The database filled. The UI displayed results. But the moment the same flow had to behave like a real product, the missing pieces became obvious: durable state, retry boundaries, partial progress, and a way for the user to trust that the system was still working.

{{< compare label="Prototype / Product" title="Direct workflow<br>→ recoverable system." body="The same product idea, but with state, visibility, and a user who can leave the page." >}}
Click search
→ scrape immediately
→ generate draft
→ hope request survives

---

Create job
→ persist company state
→ poll progress
→ recover failed work
{{< /compare >}}

## What changed when it became _real_.

The biggest shift was not a specific library or API. It was accepting that the product needed to model uncertainty. A company might have no website. A website might block scraping. A contact email might be absent. Claude might return an answer that needs validation. Email delivery might fail after everything else succeeded.

That changed the shape of the code:

- Background work became a first-class part of the product instead of an implementation detail.
- UI states became product language: pending, searching, enriching, failed, retrying, complete.
- Tests started to matter because failed enrichment could overwrite useful company data.
- Naming boundaries got sharper because "company persistence" and "discovery" were not the same concern.
- The database became a record of decisions, not just a place to dump results.

## The lesson.

The hard part is rarely just connecting APIs. The hard part is choosing the right boundaries: where validation lives, what gets persisted, how failures are recovered, and what the user sees while the system is doing slow work.

```txt
Useful prototype:
  prove the workflow exists

Useful product:
  prove the workflow can survive reality
```

The best notes from this project are the ones that explain those decisions, because they show more than the final UI ever could. They capture the moment when a small project starts behaving like a real system — and the moment I have to become a little more honest about what I am actually building.
