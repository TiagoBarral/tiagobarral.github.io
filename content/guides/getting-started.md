---
title: "The 60-minute first commit"
date: 2026-05-02
lastmod: 2026-05-02
summary: "A simple, slightly stubborn rule that quietly fixes the most common reason side projects die before they even begin."
part: 1
series: "getting-started"
series_title: "Getting Started"
chapter: 4
chapterCount: 6
words: "1,420"
chapters:
  - "The smallest possible setup"
  - "Picking a stack that won't move"
  - "Writing the first README"
  - "The 60-minute first commit"
  - "Shipping anything, even rough"
  - "What to do when you're stuck"
---

Most side projects don't die from bad ideas. They die in the gap between deciding to build them and the first commit hitting `main`. The longer that gap is, the more time the project has to grow in your head — and a project that lives in your head is a project that already costs too much.

## The *rule*.

Sixty minutes from the moment you've committed to an idea to the moment something exists in version control. Not something polished. Not something correct. Not even something that runs. Just *something*, named, committed, pushed.

That's it. That's the whole rule.

> The hardest commit on any project is the first one. Every commit after it is just an edit.

## Why *sixty*.

Sixty is short enough that you can't fit a stack debate into it. You can't research three database options. You can't design a logo. You can't pick a name you're proud of. Sixty minutes forces you to do the one thing the project actually needs, which is exist.

### What goes in the first commit

- A folder with the project name
- A README that says, in two lines, what the thing is
- A `.gitignore`
- The smallest scaffold your framework will give you — `create-next-app`, `hugo new site`, whatever
- Nothing else

{{< callout label="Field note" >}}
If you find yourself opening a "best CSS framework 2026" tab in the first sixty minutes, close it. The first commit is not where opinions go. The first commit is where momentum goes.
{{< /callout >}}

## What this *buys* you.

Three things, all underrated.

**One.** The project is now real. There's a folder. There's a URL. You can show someone. You can name it in a sentence. None of that was true sixty-one minutes ago.

**Two.** The shape of the work is now visible. The first commit reveals what's actually hard — which is almost never what you predicted. You'll often find that the thing you were dreading is trivial, and a thing you didn't think about is the real problem.

**Three.** You've broken the symmetry. Before the first commit, every decision feels equally heavy. After it, decisions are downstream of an existing thing, and downstream decisions are always smaller.

## A *concrete* example.

When I started Oportuno, the first commit was this:

```txt
oportuno/
├── .gitignore
├── README.md      # "Find SMEs with weak digital presence."
└── app/
    └── page.tsx   # <h1>Oportuno</h1>
```

Twenty-eight minutes. No database. No API integration. No design. No name validation. Just the rough shape, on disk, committed.

Everything else — the Postgres schema, the Places API integration, the LLM scoring pass, the outreach workflow — got built downstream of that first commit, one small edit at a time.

## The *shortcut*.

If sixty minutes still feels like too much, halve it. Thirty minutes. Twenty. The number isn't sacred. What's sacred is the principle: the project has to exist before the project can be good.
