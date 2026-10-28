# Portfolio Update Model

This site should stay alive without becoming another project to manage.

Use this model to decide what to update, when to update it, and how much detail each update needs.

## The Rule

Update the site when something changed in your thinking, not only when something shipped.

Good updates include:

- a feature shipped
- a bug or constraint changed the plan
- a project was paused or restarted
- a design/architecture decision became clearer
- you learned something reusable
- your current focus changed

## The Four Surfaces

### 1. Now

File:

```text
content/now.md
```

Purpose:

Show what is currently taking your attention.

Update when:

- your main focus changes
- a project enters a new phase
- you are reading/studying something relevant
- you intentionally pause something

Keep it short. This page is a snapshot, not a journal.

Model:

```md
- label: "Building"
  meta: "Project name · phase"
  title: "What I am focused on right now."
  text: "One or two sentences about the current work, constraint, or goal."
  accent: "green"
  progress: "Optional rough progress"
```

### 2. Project Journal

Files:

```text
content/projects/*.md
```

Purpose:

Show the real development history behind each project.

Update when:

- you build a meaningful feature
- you make a technical decision
- something breaks
- you change direction
- you learn something project-specific

Each entry should answer:

- What changed?
- Why did it matter?
- What was difficult?
- What did I learn?
- What would I do differently?

Model:

```yaml
entries:
  - date: "May 12, 2026"
    sortDate: "2026-05-12"
    title: "Short, specific entry title"
    summary: "One sentence that explains the change or lesson."
    sections:
      - label: "What I built"
        text: |
          What changed in the product or code.
      - label: "What I was trying to learn"
        text: |
          The question behind the work.
      - label: "What went wrong"
        text: |
          The constraint, bug, or mistake.
      - label: "What I would do differently"
        text: |
          The lesson for next time.
```

### 3. Journal

Files:

```text
content/blog/*.md
```

Purpose:

Capture broader reflections that are not only about one project.

Write a journal post when:

- the same lesson appears in more than one project
- you changed your opinion about a tool or architecture
- you want to explain a struggle honestly
- a project entry feels too important to stay buried

Use this shape:

```md
---
title: "What this lesson is really about"
date: 2026-05-12
summary: "One sentence preview."
---

Start with the situation.

Explain what changed.

Name the lesson.

End with what you will do next.
```

### 4. Guides

Files:

```text
content/guides/*.md
```

Purpose:

Turn repeated lessons into reusable advice.

Write a guide when:

- you have repeated the same process more than once
- a journal post becomes practical advice
- future you would benefit from a checklist
- someone else could follow the same steps

Journal is reflection.

Guide is instruction.

## Weekly Ritual

Once per week, spend 20 minutes.

1. Update `content/now.md`
2. Add one project journal entry if anything meaningful happened
3. Decide if the week produced a broader journal post
4. Do not update guides unless a lesson has become reusable

## Monthly Ritual

Once per month, spend 45 minutes.

1. Review all active project pages
2. Make sure statuses are still true
3. Promote one strong project entry into a journal post if useful
4. Turn one repeated lesson into a guide idea
5. Check homepage featured project still reflects your current direction

## Decision Tree

Ask:

```text
Did my current focus change?
→ Update Now

Did something meaningful happen inside a project?
→ Add a project journal entry

Is the lesson bigger than one project?
→ Write a journal post

Could someone follow this as advice?
→ Write or update a guide

Is this just a tiny implementation detail?
→ Keep it out of the site
```

## Content Quality Bar

A good update is:

- specific
- dated
- honest
- connected to a real project
- clear about what changed

Avoid:

- vague progress notes
- pretending everything was smooth
- writing only about tools
- publishing every tiny change
- turning the site into a changelog

## Default Update Prompt

Use this when you want help updating the site:

```text
I want to update my portfolio.

Here is what happened:
- Project:
- Date:
- What changed:
- What went wrong:
- What I learned:
- What is next:

Decide whether this belongs in Now, a project journal entry, a journal post, or a guide.
Then update the site.
```

## Fill-In Templates

Use these when you want to update the site manually or give raw notes to an agent.

### Now Template

Use this when your current focus changes.

```yaml
items:
  - label: "Building"
    meta: "Project name · phase"
    title: "What I am focused on right now."
    text: "One or two sentences about the current work, constraint, or goal."
    accent: "green"
    progress: "Optional progress note"

  - label: "Polishing"
    meta: "Project name · version or area"
    title: "The small improvement I am making."
    text: "Explain what is being tightened and why it matters."
    accent: "orange"

  - label: "Reading"
    title: "Book, article, course, or topic."
    text: "Why it is relevant to what I am building."
    accent: "gray"
```

Example:

```yaml
items:
  - label: "Building"
    meta: "Oportuno · MVP"
    title: "Preparing Oportuno for first real users."
    text: "The main work is making the async pipeline easier to recover when enrichment fails or times out."
    accent: "green"
    progress: "Private testing next"
```

### Project Journal Entry Template

Use this inside a project markdown file when something meaningful happens.

```yaml
entries:
  - date: "Month Day, Year"
    sortDate: "YYYY-MM-DD"
    title: "Specific title about the change"
    summary: "One sentence explaining what changed and why it mattered."
    sections:
      - label: "What I built"
        text: |
          Describe the feature, fix, refactor, or product change.

      - label: "What I was trying to learn"
        text: |
          Explain the question behind the work.

      - label: "What went wrong"
        text: |
          Describe the bug, uncertainty, constraint, or mistake.

      - label: "What I learned"
        text: |
          Name the lesson clearly.

      - label: "What I would do differently"
        text: |
          Describe the next improvement or future decision.
```

Shorter version:

```yaml
entries:
  - date: "Month Day, Year"
    sortDate: "YYYY-MM-DD"
    title: "What changed"
    summary: "Why it mattered."
```

### Journal Post Template

Use this when a lesson is bigger than one project.

```md
---
title: "The lesson in plain language"
date: YYYY-MM-DD
summary: "One sentence preview of the post."
---

## The situation

What was happening? Which project or decision exposed the issue?

## What changed

What did I try, notice, fix, or rethink?

## What was harder than expected

What broke, slowed me down, or changed my assumptions?

## The lesson

What do I believe now that I did not believe before?

## What I will do next

How will this affect the next project or decision?
```

### Guide Template

Use this when a journal lesson becomes reusable advice.

```md
---
title: "Practical guide title"
date: YYYY-MM-DD
summary: "What this guide helps someone do."
part: 1
---

## Who this is for

Describe the person or situation this guide helps.

## The problem

What usually goes wrong?

## The approach

Explain the method in simple steps.

## Steps

1. First step
2. Second step
3. Third step

## Mistakes to avoid

- Common mistake one
- Common mistake two
- Common mistake three

## What good looks like

Describe the outcome someone should aim for.
```

### Project Page Front Matter Template

Use this when adding a new project.

```yaml
---
title: "Project Name"
date: YYYY-MM-DD
lastmod: YYYY-MM-DD
status: "In progress"
featured: false
summary: "One sentence explaining the project and why it exists."
stack:
  - Tool one
  - Tool two
  - Tool three
repo: "https://github.com/..."
live: ""
entries: []
---

Longer explanation of the project, the problem it solves, and what you are trying to learn from it.
```

### Raw Notes Template

Use this when you do not know where an update belongs yet.

```text
Date:
Project:
What happened:
Why it mattered:
What broke or felt hard:
What I learned:
What I want to do next:
Should this be public?
```

After filling this, decide:

- current focus -> Now
- project-specific -> Project journal
- broader lesson -> Journal
- repeatable advice -> Guide
