---
title: "The day scraping became a <em>queue</em>."
date: 2026-05-02
summary: "There's a quiet moment in most side projects when a piece of code you thought was a small detail turns out to be the architecture. Here's the one I had two weeks ago, and what it cost me to admit it."
tags: ["Systems", "Architecture", "Production"]
---

I had been building Oportuno for about three weeks. The search worked. The Google Places integration worked. The Claude pass that scored each company's web presence worked, at least most of the time. And every single one of those steps was sitting inside a `POST /search` request, holding the user's browser open for forty seconds at a time while the server pretended this was fine.

It was, of course, not fine. The first time a request took longer than Vercel's serverless timeout, the whole thing fell over silently. The user saw a spinner, then a blank page. No error. No retry. Nothing in the database. Just a request that had been quietly euthanized somewhere between the API gateway and the function runtime.

## The real _shape_ of the work.

Here's the thing I had been avoiding: scraping is not a request. Scraping is a job. The user types "Lisbon, restaurants" and presses enter, and what they're actually starting is a small distributed system — concurrent HTTP fetches, retries, parsing, an LLM scoring pass, and finally a write to Postgres. That's not the same shape as "respond to a button click."

{{< pullquote cite="Field note, April 18" >}}
The hard part is rarely just connecting APIs. The hard part is choosing the right boundaries: where validation lives, what gets persisted, how failures recover.
{{< /pullquote >}}

For the first two weeks I had been pretending it was a request because that was the shape I knew. I had a frontend that posted to an endpoint, and an endpoint that returned data, and a database that stored the results, and that pattern works for ninety percent of side projects. It just doesn't work for this one.

{{< compare label="Before / After" title="Synchronous request<br>→ durable job." body="A fundamental architectural shift, hidden inside what looked like a small performance fix." >}}
POST /search
→ fetch places
→ scrape sites
→ score with Claude
→ write to DB
→ return 200 (timeout)

---

POST /search
→ enqueue job
→ return 202 immediately

worker (background)
→ poll queue
→ process job
→ write results

GET /jobs/:id
→ return status + results
{{< /compare >}}

## What it _cost_.

The migration was small in code and large in mental shift. The database grew a `jobs` table. The frontend grew a polling loop. The API gateway grew the idea that a `POST` doesn't have to return data — sometimes it just returns a job id, and the work happens later, somewhere else.

What I had not appreciated was how many other things have to change once that one thing changes:

- Error handling stops being "show a toast" and becomes "persist a failure, retry up to N, surface it on the job page."
- UI states multiply — pending, running, partial, complete, failed, failed-but-retrying.
- The database stops being a snapshot and starts being an event log.
- The user's model of "submit and wait" stops working; they need to be allowed to leave and come back.

### The smallest workable version

I resisted, for about a week, building a full job system. What shipped was the smallest version that fit my actual constraints: a single Postgres table, a worker endpoint, a polling UI, and enough status fields to know whether the system was alive or stuck.

It was not elegant. It was more reliable than pretending slow work was fast.

## What changed after that.

Once the work had a durable place to live, the rest of the product became easier to reason about. The UI could show progress instead of apology. Failed companies could be retried without rerunning the entire search. Partial results could be useful instead of being treated as failure.

More importantly, the code started telling the truth. The architecture finally matched the shape of the work: slow, fallible, resumable, and valuable enough to deserve state.

## The lesson I keep relearning.

Every production-minded side project has a moment like this. A small implementation detail turns into a boundary. A helper function becomes a service. A route becomes a workflow. The trick is noticing when the system is already asking for that shape, instead of forcing it to keep wearing the prototype's clothes.

The queue was not polish. It was the first honest version of the product.
