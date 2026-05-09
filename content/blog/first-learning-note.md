---
title: "What Oportuno Is Teaching Me"
date: 2026-04-25
summary: "Early lessons from turning an AI-assisted prototype into a product-shaped system."
---

Oportuno started as a direct workflow: search for local businesses, classify their digital presence, scrape public contact details, generate an outreach email, and send it.

That first version worked, but the more interesting learning started when the product needed to behave reliably. Long-running scraping does not belong inside a synchronous request. A production system needs durable job state, polling, retry boundaries, and visible progress.

## What I learned

The hard part is rarely just connecting APIs. The hard part is choosing the right boundaries: where validation lives, what gets persisted, how failures are recovered, and what the user sees while the system is doing slow work.

The best notes from this project are the ones that explain those decisions, because they show more than the final UI ever could.
