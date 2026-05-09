---
title: "Oportuno"
date: 2026-04-25
summary: "An AI-assisted outreach product for finding Portuguese SMEs with weak digital presence and turning that signal into a focused sales workflow."
featured: true
slug: "oportuno"
status: "MVP in progress"
stack:
  - Next.js App Router
  - TypeScript
  - PostgreSQL
  - Prisma
  - Google Places API
  - Claude
  - Resend
lesson: "Production constraints change the architecture: slow scraping work needs durable jobs, polling, and recovery instead of a synchronous request path."
github: "https://github.com/TiagoBarral"
entries:
  - date: "May 7, 2026"
    sortDate: "2026-05-07"
    title: "Grid Search and Synonym Hints"
    summary: >-
      Three interconnected improvements to grow the company database past the 60-result ceiling.
    sections:
      - label: "What I built / changed"
        text: |-
          Three interconnected improvements to grow the company database past the 60-result ceiling.
          
          **Grid search:** instead of one Google Places Text Search centred on the city, the pipeline now runs five concurrent searches — city centre plus north/south/east/west offsets at 8km. Results are merged and deduplicated by placeId. A single search for "bar em Lisboa" can now return up to 300 unique results instead of 60. The grid points use a cosine-corrected longitude offset so the coverage is actually square at Portugal's latitudes (~40°N, where cos(40°) ≈ 0.77 — without the correction the east/west points drift).
          
          **Skip-known enrichment:** before the per-company enrichment loop, the worker now bulk-queries which placeIds already exist in the Company table. Companies we've already processed skip the Place Details API call and the website scrape entirely — the pipeline just marks them done. This dramatically speeds up re-runs and avoids redundant API costs. The Google Text Search calls still run (you have to ask Google to discover placeIds), but the expensive per-company work is skipped.
          
          **Synonym hints in Descobrir:** the search form now shows how many companies already exist for the current industry+location (debounced 500ms count fetch) and suggests related search terms as clickable chips. Clicking "pub" pre-fills the industry field so the user can immediately search for a related term. The synonym map covers ~30 common Portuguese SME categories.
      - label: "What I was trying to learn / decide"
        text: |-
          The debate started from a user question: "if there are 200 bars in Lisbon, how do I get all 200?" The investigation confirmed the new Google Places API has exactly the same 60-result limit — switching APIs wouldn't help. The grid approach is the only documented way to exceed 60 for a single search term.
          
          The concurrent vs sequential debate for grid searches was important. Sequential execution would have meant up to 20 seconds of sleeping (5 grid points × up to 4 seconds of inter-page delays each). Using `Promise.all` makes the page-delay sleeps overlap across grid points, cutting wall-clock time significantly.
      - label: "What went wrong"
        text: |-
          Nothing broke, but the code reviewer caught the sequential issue before it shipped — 30 seconds of wall-clock latency would have made the pipeline appear hung and would have hit serverless timeout limits. The fix was one line (sequential loop → `Promise.all`).
          
          The `wasCreated` heuristic (`createdAt.getTime() === updatedAt.getTime()`) was also flagged as fragile. It's existing behaviour that's been working and wasn't introduced by this feature, so left as-is with a note.
      - label: "What I learned"
        text: |-
          The cosine correction for longitude offsets matters at non-equatorial latitudes. At 40°N (Portugal), cos(40°) ≈ 0.77, so 1° of longitude is only ~85km east-west, not 111km. Without the correction, the east/west grid points would be 30% further from centre than intended. This is one of those geographic facts you either know or silently get wrong.
          
          Concurrent API calls within a single pipeline job are safe as long as each call is independently paginated. The page-delay sleeps inside each `searchPlaces` call are per-call, so running 5 calls concurrently means 5 independent sleep sequences, not 5 sequential ones.
  - date: "May 7, 2026"
    sortDate: "2026-05-07"
    title: "Templates Expanded UI Polish"
    summary: >-
      Two small fixes to the expanded template view.
    sections:
      - label: "What I built / changed"
        text: |-
          Two small fixes to the expanded template view. The background changed from flat `bg-gray-50` to a `bg-gradient-to-br from-blue-50 to-slate-100` — this creates enough contrast that the white inner cards pop against the background. Added `shadow-sm` to each inner card. The context file chip now has a "Ficheiro de contexto" label above it, matching the style of "O que ofereço" and "Instruções adicionais".
      - label: "What I learned"
        text: |-
          White cards on a near-white background need either a tinted background, a shadow, or a border to read as distinct elements. The gradient approach is the lightest touch — it adds depth without introducing a new color that clashes with the card content.
  - date: "May 6, 2026"
    sortDate: "2026-05-06"
    title: "Email Templates"
    summary: >-
      A full template system: EmailTemplate model (name, subject, body, oferta, tom, instrucoesAdicionais), GET/POST API, DELETE by id, a save form on the send page, a load dropdown...
    sections:
      - label: "What I built / changed"
        text: |-
          A full template system: `EmailTemplate` model (name, subject, body, oferta, tom, instrucoesAdicionais), GET/POST API, DELETE by id, a save form on the send page, a load dropdown on the send page, and a templates management page replacing the placeholder.
          
          The workflow is save-first: users compose an email in the send flow, get it right, then save it as a named template. Loading brings back both the brief fields (oferta, tom, additional instructions) and the subject/body. The templates page is a management surface — list, preview subject, delete.
      - label: "What I was trying to learn / decide"
        text: |-
          Whether templates should store just the email text or the full brief too. The brief won because it's what lets you regenerate a variation — if you want to adapt "Web para PMEs" for a different tone, the brief fields pre-fill and you can tweak and regenerate. Storing only the text is a dead end.
          
          The "save from send page, not from a template editor" approach was the key UX decision. Creating templates in isolation (type a name, write a subject, write a body) is unnatural. The right moment is after you've already composed something you're happy with — exactly when the "Guardar template" button appears.
      - label: "What went wrong"
        text: |-
          The DELETE dynamic route handler had the wrong signature for Next.js 15. The `params` object in dynamic routes is now a `Promise` that must be awaited — `{ params }: { params: Promise<{ id: string }> }` and then `const { id } = await params`. The old synchronous signature compiled but failed the Next.js validator type check in `.next/dev/types/validator.ts`. A clean error that pointed directly at the line.
          
          The save button's disabled condition initially only checked subject and body — not `brief.oferta`. The API would reject with a 400 when the user had written a nice email but left the offer field blank. The code reviewer caught this.
      - label: "What I learned"
        text: |-
          Next.js 15 made route handler `params` async. Any dynamic route that destructures `params` synchronously will fail the internal type validator even if it compiles. The fix is one line (`await params`) but it's not obvious from the type error message. This is now a known pattern to apply to all future dynamic routes.
          
          The reviewer also caught a silent failure in the load dropdown — a network error would show "Nenhum template guardado" instead of an error message, making it impossible to distinguish from an empty list. Added a `loadError` flag and a distinct error message in the dropdown. Small detail, big difference in debuggability.
  - date: "May 6, 2026"
    sortDate: "2026-05-06"
    title: "AI Cost Analysis and Workflow Rules"
    summary: >-
      Added a "AI Dev Workflow" section to TODO.md with six actionable items drawn from session cost analysis: $100 for a 4-day session, 93% at >150k context, 92% from subagent-heavy...
    sections:
      - label: "What I built / changed"
        text: |-
          Added a "AI Dev Workflow" section to TODO.md with six actionable items drawn from session cost analysis: $100 for a 4-day session, 93% at >150k context, 92% from subagent-heavy flows. Key items: use `/compact` mid-session, use `/clear` between unrelated tasks, cap session length, be deliberate about spawning subagents, configure cheaper models for simpler agents, batch small changes instead of running full `/feature` flows. Also strengthened the journal update rule in CLAUDE.md to trigger on every commit via the PostToolUse hook.
      - label: "What I learned"
        text: |-
          The cost breakdown was clarifying. The Sonnet model at high context is what drives cost — not the number of features or the number of API calls. A single 4-day session at >150k context is fundamentally more expensive than four 1-day sessions at <50k context each, even if the total work is identical. The `/compact` command exists exactly for this case and wasn't being used.
  - date: "May 6, 2026"
    sortDate: "2026-05-06"
    title: "Template Context File and Expanded UI Redesign"
    summary: >-
      Added contextFileName to the EmailTemplate model so the name of any PDF/TXT context file used during AI generation is preserved with the template.
    sections:
      - label: "What I built / changed"
        text: |-
          Added `contextFileName` to the `EmailTemplate` model so the name of any PDF/TXT context file used during AI generation is preserved with the template. When saving, the send page now passes `contextFile?.filename`. In the expanded view it appears as a blue document chip with the filename and a note "Ficheiro de contexto usado na geração."
          
          The expanded UI was redesigned: replaced the flat blue-50 two-column layout with white inner cards — one for the briefing (oferta, instruções, context file) and one for the email body. The tom field became a coloured badge (blue/green/orange). The table was replaced with individual card components that have a shadow and proper expand animation via chevron rotation.
      - label: "What I was trying to learn / decide"
        text: |-
          The original table-in-expanded-row approach was minimal but visually blank. The fix was to treat each section of the expanded content as its own card — the same pattern used everywhere else in the app. Adding inner white cards on a gray-50 background creates the depth the user was missing.
      - label: "What I learned"
        text: |-
          Context files used for AI generation need to be persisted with the template, not just used transiently. A user who comes back to a template a week later has no way to know which PDF they used without this. Storing only the filename (not the content) is the right trade-off — the content can be re-uploaded if needed, but the name tells the user which file to look for.
  - date: "May 6, 2026"
    sortDate: "2026-05-06"
    title: "Template UX Improvements"
    summary: >-
      Two small UX fixes.
    sections:
      - label: "What I built / changed"
        text: |-
          Two small UX fixes. The "Guardar template" button got a blue-50 background, blue border, and a download icon — it now reads as an action rather than a disabled placeholder. On the templates page, rows became clickable: a chevron rotates on expand, revealing the oferta, instruções adicionais, assunto, and full email body in a two-column inline panel.
      - label: "What I was trying to learn / decide"
        text: |-
          The save button was invisible because gray-on-gray outlined buttons have no affordance — they look like disabled states even when enabled. The fix was simple: use the blue-50/blue-300 tonal pattern already established elsewhere in the app for secondary actions.
          
          For the templates page, the original design was just a management surface (list + delete). A list of template names with no way to see what's inside has limited value. Making rows expandable adds the preview without navigating away or opening a separate page.
      - label: "What I learned"
        text: |-
          The delete button needed `e.stopPropagation()` to prevent the row click handler from toggling the expand state when the user is trying to delete. A detail that's easy to miss — clicking delete would both delete and expand/collapse the row simultaneously without it.
  - date: "May 6, 2026"
    sortDate: "2026-05-06"
    title: "Homepage Color Hierarchy"
    summary: >-
      Refined the homepage color treatment across two sections.
    sections:
      - label: "What I built / changed"
        text: |-
          Refined the homepage color treatment across two sections. "Como funciona" step icons moved from `blue-50`/`blue-600` to `blue-100`/`blue-700`, and the step number badges from `blue-600` to `blue-800`. This creates a visible weight hierarchy within the blue family — the icon background is mid-tone, the icon itself is darker, and the number badge is darkest. "Ferramentas" icons moved from blue entirely to `gray-100`/`gray-500`, signalling that those cards are secondary utility links rather than primary actions. The hero cards (Explorar in blue, Descobrir in purple) were left untouched.
      - label: "What I was trying to learn / decide"
        text: |-
          Whether a single flat blue across the whole homepage felt intentional or just monotonous. The answer was monotonous — when every accent is the same shade of `blue-600`, nothing has weight and nothing draws the eye. The debate was whether to introduce a new color or use tonal variation within blue. Tonal variation won because it keeps the palette coherent while still creating differentiation. Grey for Ferramentas was the right call because those are tools, not entry points — they should feel like a utility shelf, not a call to action.
      - label: "What went wrong"
        text: |-
          The branch was not created before making the changes — again. An attempt to retroactively isolate the changes onto a `chore/homepage-color-hierarchy` branch from main failed because main has a completely different homepage structure. The color changes were based on the `feat/email-templates` version of `page.tsx`, which diverged significantly from main. The stash/restore process during the failed branch isolation also wiped the changes, requiring a full reapplication.
      - label: "Biggest challenge / bug"
        text: |-
          The branch isolation attempt created a merge conflict because the stash was built against `feat/email-templates`'s version of `page.tsx` but was being popped onto main's version. These are structurally different files — the conflict was unresolvable automatically. The resolution was to abort, delete the dead branch, return to `feat/email-templates`, and reapply the changes manually before committing them as an isolated commit on the feature branch.
      - label: "What I learned"
        text: |-
          When a feature branch has diverged significantly from main, cosmetic changes that touch the same file cannot be cleanly isolated onto a separate branch without first understanding which version of the file each branch holds. The right process is: check the current branch first, decide whether isolation is feasible, and if not, commit as a clean isolated commit on the current branch with an honest commit message.
      - label: "What I would do differently"
        text: |-
          Create the branch before opening the file. Not after the change is made, not during the debate — before the first edit. The retroactive isolation attempt cost more time than the original change.
  - date: "May 6, 2026"
    sortDate: "2026-05-06"
    title: "User Settings, Sender Identity, and Branch Debt"
    summary: >-
      A UserSettings table (singleton pattern, id: "singleton") with senderName and senderEmail.
    sections:
      - label: "What I built / changed"
        text: |-
          A `UserSettings` table (singleton pattern, `id: "singleton"`) with `senderName` and `senderEmail`. A `/definições` settings page where the user configures their sending identity once. The email service layer was refactored so `from` is a required parameter passed from the route handler rather than read from an env variable inside the service — this removes a hidden ambient dependency and makes the service testable in isolation. Both the bulk and single-email send paths now fetch `UserSettings` and build the `from` string as `"Name <email>"` with an env fallback.
          
          On the send page, a "De:" row was added at the top of the email editor — the same pattern every email client uses (De / Assunto / Mensagem). The earlier attempt to put the sender info in the page header failed because it blended in with navigation and had no clear edit affordance. The "De:" field is immediately recognisable and shows an "Alterar" link that goes to `/definições`.
          
          Also fixed the "Contactado" badge to only trigger on `status: "SENT"` — previously any EmailLog entry including FAILED sends would show the badge, which was misleading.
      - label: "What I was trying to learn / decide"
        text: |-
          The core debate was where sender identity belongs in the product. Three options were considered: Reply-To only (doesn't fix the sender appearance), a free-form From field on the send page (requires Resend verification, but correct long-term), or a settings page with a pre-filled field on the send page. The settings page approach won because the sender identity doesn't change per send — it's a one-time configuration, not a per-send decision.
          
          The From field debate also surfaced a product architecture question: should the app allow any email address as the sender, or only verified ones? Answer: allow any input, surface Resend's rejection as a clear error. The verification happens in Resend's dashboard, not in Oportuno.
      - label: "What went wrong"
        text: |-
          The Prisma client regeneration issue blocked the UserSettings upsert with "Cannot read properties of undefined (reading 'upsert')". The `prisma db push` step applies schema changes to the DB but the `prisma generate` step (which updates the TypeScript client) failed on Windows because the dev server was holding the DLL open. The symptom looks like a code bug but the cause is environmental. Fix: stop dev server, run `prisma generate`, restart. This same EPERM issue has now appeared four times in this project.
          
          The initial placement of the sender info in the page header was wrong. Subtle gray text below the subtitle doesn't register as actionable information. The user noticed it felt wrong immediately. The "De:" field in the editor is the right pattern — it mirrors email client UX that users already know.
      - label: "Biggest challenge / bug"
        text: |-
          The `from` parameter refactor broke TypeScript at every `sendEmail` call site where `from` wasn't supplied. This is exactly what you want from a required parameter — the compiler catches every missed call site at commit time via the pre-commit hook. The breakage was intentional and the fix was mechanical. Making `from` optional with a service-level fallback would have been safer short-term but less correct — the route handler is the right place to decide what the from address is, not the service.
          
          The argument order in `sendBulkEmails` needed changing: `attachments` is optional and can't precede the now-required `from`. The code reviewer caught the type inconsistency (optional before required), which also forced the call site in the route to update its argument order.
      - label: "What I learned"
        text: |-
          A "De:" field in the email editor is more intuitive than any banner, badge, or header line, because every email client in the world uses that pattern. When the UX problem is "users don't know what address they're sending from," the solution isn't to add more metadata to the header — it's to show the from address where emails are composed.
          
          The singleton Prisma pattern (`id: "singleton"` with upsert) is clean for single-user configuration tables. It's auth-ready: when users are added, a `userId` foreign key can be added to the model and the upsert logic at the route level changes trivially. No migration of data needed — just a schema change.
      - label: "What I would do differently"
        text: |-
          Add `@default("singleton")` to the `id` field from the start. Without it, any future `create` call that forgets to supply the id fails at runtime rather than at schema parse time. A one-word addition that closes a whole class of future bugs.
  - date: "May 6, 2026"
    sortDate: "2026-05-06"
    title: "Branch Debt and Layered Commit Recovery"
    summary: >-
      Nothing new was shipped.
    sections:
      - label: "What I built / changed"
        text: |-
          Nothing new was shipped. The entire session was about paying down six weeks of branch debt — all the work since the CSV export commit had accumulated on `feat/csv-export` without branching per feature. Six layered commits were created to give the git history a meaningful shape, and a mandatory branch rule was added to CLAUDE.md.
      - label: "What I was trying to learn / decide"
        text: |-
          How much damage accumulation does to a git history and whether retroactive layering is worth the effort. The answer: yes, because even if the commits don't isolate changes perfectly (some files span multiple features), the commit messages give future readers a narrative. "feat: bulk email send with AI template generation" tells a story that "feat: misc changes" doesn't.
      - label: "What went wrong"
        text: |-
          The branch rule in CLAUDE.md existed from the beginning of the project. It was clear. It was ignored repeatedly. The failure mode is specific: during an active session building something interesting, the friction of creating a branch before writing code feels unnecessary. By the time the feature is done, there are three more in-progress and the branch name is already wrong. This has happened at least three times in this project.
          
          The rule was strengthened: before a single line of code, create the branch. At the end of every `/development` flow, run TSC and commit before proceeding to the next feature. The end-of-flow commit requirement is new — previously the rule only covered the start.
      - label: "What I learned"
        text: |-
          Rules in a CLAUDE.md file only work if they're written to be impossible to overlook. "Branch for any change" is too easy to read as "branch for important changes." The new rule uses "before a single line of code" and "no exceptions" language. It also specifies exactly when to commit (end of /development flow), which removes ambiguity.
          
          The retroactive layered commits approach is a reasonable recovery when you can't use `git add -p`. Assign changed files to the most significant feature that touched them, write honest commit messages that describe what the feature does rather than what files changed, and accept that some commits will be imperfect bundles. Clean history is better than accurate history — a readable lie is more useful than an accurate mess.
  - date: "May 6, 2026"
    sortDate: "2026-05-06"
    title: "Blocking Send Without Sender Identity"
    summary: >-
      The send button on /networking/send is now disabled when no sender identity is configured.
    sections:
      - label: "What I built / changed"
        text: |-
          The send button on `/networking/send` is now disabled when no sender identity is configured. Previously, a user with no `/definições` setup could still click send — the email would go out from `noreply@oportuno.pt` silently. The footer message changes from "Não é possível desfazer esta ação" to "Configure o remetente em Definições antes de enviar." when the block is active.
      - label: "What I was trying to learn / decide"
        text: |-
          Whether to warn or block. Allowing a send without a configured sender fails silently in the worst possible way: the user thinks they sent a professional outreach from their own address, but the recipient sees `noreply@oportuno.pt`. That's a trust issue with the product. Blocking is correct — it's a one-time setup and the "Configurar →" link in the De: field is right there.
      - label: "What I learned"
        text: |-
          When a missing configuration produces a silently degraded outcome rather than an error, blocking is always better than warning. Warnings get ignored. A disabled button with an explanation tells the user exactly what to do.
  - date: "May 6, 2026"
    sortDate: "2026-05-06"
    title: "Homepage Color Consistency"
    summary: >-
      Changed the homepage to use blue consistently across all icon accents.
    sections:
      - label: "What I built / changed"
        text: |-
          Changed the homepage to use blue consistently across all icon accents. Previously the "Como funciona" steps and the stats panel used green and purple for visual variety — step 2 had a green icon, step 3 was purple, the phone and email stat rows used green and purple respectively, and the "Criar template" tool card had a purple icon. All of those are now blue. The two hero cards ("Explorar base de empresas" in blue, "Descobrir novas empresas" in purple) were left untouched intentionally — the purple there is part of the card's identity, not decoration.
      - label: "What I was trying to learn / decide"
        text: |-
          Whether a multi-color homepage felt intentional or noisy. Looking at it against a reference design, the answer was noisy. The colors weren't communicating anything meaningful — they were just variation for variation's sake. Blue is already the product's identity color, so having green and purple scattered across "Como funciona" and the stats panel pulled attention without reason.
      - label: "What went wrong"
        text: |-
          The change was made directly without creating a branch first, and the journal wasn't updated immediately. Both are required by the project rules. The color change ended up mixed in with uncommitted `feat/csv-export` work on the active branch, which creates a bundling problem.
      - label: "Biggest challenge / bug"
        text: |-
          Nothing technically difficult — five className swaps. The challenge is process: working on a branch that already has a lot of uncommitted scope, it's easy to drop small UI changes in without thinking about isolation.
      - label: "What I learned"
        text: |-
          Even tiny visual changes that affect UI should go on their own branch or be committed as a clean isolated commit before any other work. The rules exist for this reason — to make it obvious later what changed and why.
      - label: "What I would do differently"
        text: |-
          Create or switch to a `chore/homepage-blue-colors` branch before touching `page.tsx`, make the change, commit, merge, then return to the feature branch. Thirty seconds of discipline that keeps the history readable.
  - date: "May 5, 2026"
    sortDate: "2026-05-05"
    title: "Homepage Redesign: Hero, Stats, and Feature Sections"
    summary: >-
      A complete homepage rewrite to match a reference design.
    sections:
      - label: "What I built / changed"
        text: |-
          A complete homepage rewrite to match a reference design. Hero section with a gradient background, a floating 🚀 emoji with a sparkle decoration, and two CTA cards (Explorar and Descobrir) with coloured icons, bold titles, gradient card backgrounds, and filled circle arrow buttons. A right-side stats panel ("A sua base de dados") showing live counts of companies with email, website, and phone — loaded from an extended `/api/stats` endpoint. A "Como funciona" section with three numbered steps, each in a white card with the step number badge overlapping the icon box. A "Ferramentas" section with two cards linking to Modelos de email and Histórico de emails.
          
          The stats API was extended from just `totalCompanies` to include `withEmail`, `withWebsite`, `withPhone` — all computed with parallel Prisma count queries. Added `@@index([email])` and `@@index([phoneNumber])` to the Company model and `@@index([createdAt])` to EmailLog.
          
          A new `/historico` page was built showing all sent emails with basic table and pagination.
      - label: "What I was trying to learn / decide"
        text: |-
          The "Explorar vs Descobrir" button debate was the most interesting design discussion. We went through several iterations: strong filled blue + green (too heavy, competed), outlined + filled (too subtle), soft tinted (Option B — better). Then we debated whether different colors even made sense. The answer was yes — they represent fundamentally different actions (use existing data vs discover new companies) and the color should reflect that, but neither should dominate.
          
          The Descobrir card style debate produced the insight: two equally heavy filled buttons in competing colors create visual tension. The eye doesn't know where to go. The solution is to give them the same visual weight but different hues, or make one primary and one secondary. We landed on gradient card backgrounds (from-white to-blue-50, from-white to-purple-50) with matching bordered outlines — same weight, distinct personalities.
          
          The "Como funciona" section went through three iterations of icon size and card layout. The final version uses the number badge overlapping the top-left of the icon box — same pattern as the reference — with `w-16 h-16 rounded-2xl` icon boxes and `w-9 h-9` icons. Each step in its own white card. The number sits `absolute -top-2 -left-2` on a `relative` wrapper around the icon box.
      - label: "What went wrong"
        text: |-
          Several UI decisions required seeing the rendered result to evaluate — you can't reason about them in Tailwind classes. The `justify-between` fix for the stats card created unnatural spacing. The `divide-y` on stats rows looked better than expected. The "Contactado" badge on the "Descobrir novas empresas" panel title was identical to the CTA card title, which the user caught. Renamed to "A sua base de dados."
          
          The decorative search bar in the stats panel had a cursor that suggested it was interactive when hovered. Fixed with `pointer-events-none select-none`.
      - label: "What I learned"
        text: |-
          UI iteration is conversation, not engineering. The user's aesthetic instinct — "i dont like the strong blue and green buttons/card" — is valid information that doesn't have a technical root cause. The fix is to understand what "strong" means visually (full saturation, equal weight, competing for attention) and find the alternative that reduces those properties while keeping the distinction. Knowing the principle makes the iteration faster.
          
          The right title for a stats panel is the thing it actually shows, not the thing you copied from the reference. "Descobrir novas empresas" was on the reference as a label because that page had a discovery search bar. Our panel shows database stats. It should say "A sua base de dados."
      - label: "What I would do differently"
        text: |-
          Settle the visual style of the hero CTAs before building. We changed them four times: strong filled, outlined, soft tinted, card style. Each required a rewrite of the button JSX. If we'd agreed on the card pattern from the first debate, we'd have written it once. Visual debate should happen with mockups or at least a verbal description of the full design, not iteratively through shipped code.
  - date: "May 5–6, 2026"
    sortDate: "2026-05-05"
    title: "Email History: Full Feature Set"
    summary: >-
      The email history page grew from a simple paginated table into a full-featured work tool.
    sections:
      - label: "What I built / changed"
        text: |-
          The email history page grew from a simple paginated table into a full-featured work tool. Added: a stats bar at the top (total enviados, falhados, este mês — each from dedicated Prisma count queries), status filter pills (Todos/Enviados/Falhados, client-side), inline body expand (click any row to see the full email content), relative timestamps ("há 3 dias") with absolute date on hover, a "Reenviar" button on failed rows that writes the company back to sessionStorage and navigates to `/networking/send`, and a "Exportar histórico completo" CSV button that pages through all email logs and downloads.
          
          On the networking view, a "Contactado" badge now appears next to any company name that has at least one EmailLog entry. This is loaded from a new `/api/contacted` endpoint on mount, stored as a `Set<string>`, and used for O(1) badge lookup on each row render.
      - label: "What I was trying to learn / decide"
        text: |-
          The feature set debate was straightforward: all of the proposed features directly prevent real user mistakes or give real operational information. "Já contactado" in Networking prevents sending duplicate emails. The body preview removes the uncertainty of "what did I actually send?" Failed email retry closes a loop that was previously abandoned. None of these felt like nice-to-haves.
          
          The retry implementation had an interesting constraint: the send page expects a full `Company` object in sessionStorage. An `EmailLogEntry` doesn't carry all the fields (`placeId`, `industry`, `location`). Two options: fetch the full company record from the API before navigating, or build a minimal stub. We chose the stub approach because the fields the send page actually uses for template generation (`name`, `email`, `category`, `municipality`) are all available in the log entry. The cosmetic fields (`placeId`, `industry`) are stubbed as empty strings and don't affect the send.
      - label: "What went wrong"
        text: |-
          `totalSent = total - totalFailed` was the first implementation. The code reviewer flagged it: `total` includes all statuses. If a third EmailStatus value is ever added (e.g., `QUEUED`), the math silently breaks. Added a dedicated `prisma.emailLog.count({ where: { status: "SENT" } })` to the `Promise.all`. The cost is negligible — it runs in parallel with the other queries — and the intent is explicit.
          
          The filter count label in the pill bar showed `filteredItems.length` — the count of matching items on the current page. With 47 failed emails across 3 pages, the "Falhados" pill would show "3 nesta página" when it looked like it was claiming "3 total." Changed to: show total count from meta when filter is ALL, show "X nesta página" when a status filter is active.
      - label: "What I learned"
        text: |-
          The `contactedIds` Set is a good pattern for cross-referencing two data sources without coupling them. The networking view doesn't know anything about email history — it just knows "this company ID has been contacted." The history page doesn't know about the networking view. The `/api/contacted` endpoint is a simple distinct query. Each piece is independent and the connection is a Set lookup in the render function.
          
          The `formatRelative` helper is computed at render time and doesn't refresh. A row showing "há 3 min" when the email was sent 40 minutes ago isn't a functional bug, but it's a trust issue. For MVP it's fine — the page typically loads fresh. But it's the kind of thing that erodes confidence if a user notices. Worth noting.
  - date: "May 4, 2026"
    sortDate: "2026-05-04"
    title: "Networking View Redesign: Instant Filters, Chips, and Selection Sidebar"
    summary: >-
      A full rewrite of the networking view.
    sections:
      - label: "What I built / changed"
        text: |-
          A full rewrite of the networking view. The filter bar changed from a submit-based form to instant-apply: every filter fires a fetch immediately on change, no "Aplicar filtros" button. Active filters appear as removable chips below the bar. The "has website" and "has email" filters became toggle button groups (Todos/Sim/Não) instead of dropdowns. The left sidebar was redesigned to show a "Resumo da seleção" panel — when companies are selected, it breaks down the selection by email/website presence and shows a green notice "Só serão contactadas empresas com email válido." This directly fixes the UX problem where users had to pre-filter by email before contacting companies, with no feedback about how many of their selections would actually receive emails.
          
          A race condition fix was added: rapid filter changes now use an `AbortController` so previous in-flight requests are cancelled when a new filter fires. Without this, fast filter changes could deliver results out of order — an old response landing after a newer one, showing stale data.
      - label: "What I was trying to learn / decide"
        text: |-
          The core UX problem was that the send flow silently excludes companies without email. Users would select 20 companies, click "Contactar", land on the send page, and see "2 empresas excluídas" — with no way to know before clicking. The resumo panel solves this at selection time, not after navigation.
          
          We also debated removing the "Oportunidade" filter. The argument: users can filter by website and email directly, which is the underlying signal oportunidade was derived from. Having a filter for the derived signal when you can filter the source signals is redundant. Agreed and removed.
      - label: "What went wrong"
        text: |-
          Instant filters mean every dropdown change fires a fetch. With 6 filters, a user rapidly changing multiple filters could trigger 4-5 near-simultaneous requests. The last response wins — which is correct most of the time, but if requests arrive out of order, the user sees stale results. The `AbortController` fix handles this: each new fetch cancels the previous one before starting. The code reviewer flagged this risk. The fix was 10 lines.
          
          The export handler in `handleExport` was reading `filters` from the closure rather than as a parameter. If a filter change fired and React hadn't committed the new state yet, the export would use the pre-update filters. This is the same class of bug as the stale closure issue in `fetchCompanies` — fixed there by passing filters as a parameter, but the export function was missed. Caught in review, fixed with `const currentFilters = filters` captured at the top of the function.
      - label: "What I learned"
        text: |-
          The "Resumo da seleção" pattern — show a breakdown of what will actually happen before the user commits — is the right fix for any flow where the result depends on a subset of the input. "You selected 15 companies: 12 have email (will be contacted), 3 do not (will be excluded)" is better than discovering the exclusion after the fact.
          
          Instant filter apply is only safe when you handle the race condition. The submit-button model gives you natural debouncing — users click once when ready. Instant apply means every change fires. The `AbortController` pattern is the standard solution and should be the default for any instant-filter component.
  - date: "May 4–5, 2026"
    sortDate: "2026-05-04"
    title: "Complete Parish and District Location Filter"
    summary: >-
      A cascading three-level location filter in the networking view: Distrito → Concelho → Freguesia.
    sections:
      - label: "What I built / changed"
        text: |-
          A cascading three-level location filter in the networking view: Distrito → Concelho → Freguesia. Each level narrows the next. The user can filter at any granularity — just a district (all councils in it), a specific council (municipality), or down to a specific parish like Rinchoa.
          
          The underlying data — all parishes for all Portuguese municipalities, grouped by district — came from a massive data-entry session where the user pasted the official parish lists for all 18 continental districts plus Açores and Madeira. Hundreds of lines in `Distrito | Concelho | Freguesia` format. The final data lives in `lib/parishes.ts`.
          
          The critical insight: parishes in the DB are stored at municipality level (Rinchoa → Sintra). The district and parish dropdowns are navigation aids, not additional filter dimensions. Selecting Rinchoa sets `filters.municipality = "Sintra"` — same API query, just a better way to find the right concelho.
      - label: "What I was trying to learn / decide"
        text: |-
          The original implementation was a single dropdown with 400+ entries — every municipality and every parish listed alphabetically with the municipality in parentheses. The user rejected it immediately: "thats not very user friendly." A dropdown with 400 entries is worse than the 29-municipality dropdown it replaced. The correct pattern is progressive disclosure — narrow first, then pick from a small list.
          
          The district-only filter debate: if a user picks "Lisboa" district but doesn't pick a concelho, should it filter results? The DB stores municipality, not district. Filtering by district alone would require the API to accept multiple municipalities. We chose Option A: district-only = no filter yet. The district just narrows the concelho list. Accepted limitation for MVP.
      - label: "What went wrong"
        text: |-
          Nothing broke, but the data entry session was long. The user provided the full official parish data for all 308 municipalities across all Portuguese districts. Most of this data is never used (parishes for Bragança, Beja, etc. don't appear in CITY_NAMES), but having the full dataset means adding a new municipality later is just updating CITY_NAMES and PORTUGUESE_CITIES — the parish data is already there.
      - label: "What I learned"
        text: |-
          The user's instinct — "users should be able to pinpoint what they want" — was right, and the right solution required understanding the actual data model. Parishes map to municipalities which map to the DB filter value. The UI adds two extra navigation levels but the query stays at municipality level. Knowing that constraint early would have saved time exploring solutions that required backend changes.
  - date: "May 3–4, 2026"
    sortDate: "2026-05-03"
    title: "AppShell Redesign: Sidebar Navigation and Breadcrumbs"
    summary: >-
      Replaced the top tab bar in AppShell with a left sidebar.
    sections:
      - label: "What I built / changed"
        text: |-
          Replaced the top tab bar in AppShell with a left sidebar. The sidebar has the logo at the top, navigation items (Networking, Descobrir, Modelos de email, Histórico) with icons and active state, and a user block at the bottom. A breadcrumb bar renders at the top of the content area on every page, derived from the current pathname. The `/networking/send` page's own breadcrumb row was removed since AppShell now handles it.
          
          Also removed the filter sidebar from the homepage — it was duplicating filters that already exist on the networking page, pushing everything into a cramped column, and confusing users about where filtering actually happens.
      - label: "What I was trying to learn / decide"
        text: |-
          The sidebar debate was about what the homepage filter sidebar was actually doing. It wasn't filtering the homepage — it was pre-filling filters and navigating to `/networking`. That's a shortcut to a page one click away, occupying 256px of prime real estate. Remove it, let the content breathe, and make it clear that filtering happens in Networking where the table is.
          
          The sidebar navigation debate was about what to show when features don't exist yet. The reference design had eight nav items. We have two real features (Networking, Descobrir). Solution: add two placeholder items (Modelos de email, Histórico) that point to real destinations, but treat them as feature promises rather than current capabilities. "Modelos de email" routes to `/templates` which shows an "em breve" placeholder with a link to the existing email generation flow.
      - label: "What went wrong"
        text: |-
          The `h-screen` vs `h-full` issue on the AppShell root container. I used `h-screen` (height: 100vh) which works visually on desktop Chromium but bypasses the flex relationship with `<body>`. The `<body>` was `min-h-full flex flex-col`, which means AppShell is a flex child — but `h-screen` doesn't fill the parent, it just hard-codes 100vh independently. Code review caught this. Fixed by changing `<body>` to `h-full` and the AppShell root to `h-full`, so the height chain is complete from `<html>` down.
      - label: "What I learned"
        text: |-
          `justify-between` on a container creates unnatural spacing when you just want content to flow naturally. I tried to fix empty space in the Descobrir stats card by using `justify-between`, which pushed the title block to the top and the stats rows to the bottom with a large gap in between. That's not natural — it looks like the content is being stretched. The right fix was to accept that some space exists at the bottom, use consistent `gap`, and add a bit more padding. Natural flow always wins over forced distribution.
  - date: "May 3, 2026"
    sortDate: "2026-05-03"
    title: "Clarifying Where Environment Variables Actually Live"
    summary: >-
      Updated CLAUDE.md and TODO.md to make the environment variable rules explicit.
    sections:
      - label: "What I built / changed"
        text: |-
          Updated CLAUDE.md and TODO.md to make the environment variable rules explicit. Local development values live in `app/.env.local` because the Next.js app runs from the `app/` folder. Future production values belong in the chosen hosting provider dashboard, not in git. `app/.env.example` stays committed with empty placeholder keys only. The TODO now says to verify the existing local env file safely instead of saying the keys need to be filled from scratch.
      - label: "What I was trying to learn / decide"
        text: |-
          The confusion was simple but important: `.env.local` already exists, so the real question was not "where do we create it?" but "where should each type of env value live?" Local, git, and production each have a different role. Mixing those roles is how secrets end up in commits or production silently misses required keys.
      - label: "What went wrong"
        text: |-
          The old TODO wording implied the env file did not exist yet. That made the setup checklist feel stale. The rule needed to distinguish between verifying local secrets, keeping public placeholders synced, and configuring production secrets later.
      - label: "Biggest challenge / bug"
        text: |-
          The main risk is accidental exposure. Even checking env setup can become unsafe if a tool prints secret values into chat or logs. The updated rule says to verify key presence without displaying the values.
      - label: "What I learned"
        text: |-
          Environment setup needs three explicit lanes: local `.env.local`, committed `.env.example`, and production dashboard variables. Once that is written down, future setup work becomes much less ambiguous.
      - label: "What I would do differently"
        text: |-
          Add env location rules as soon as a project gets its first external API key. It is easier to prevent secret confusion early than clean it up later.
      - label: "Follow-up: the full env list"
        text: |-
          The first env rule listed only the obvious external service keys: Anthropic, Resend, and Google. A quick check of the actual source showed that was incomplete. Prisma needs `DATABASE_URL`, the scheduled pipeline worker uses `CRON_SECRET` outside development, and local AI mocking is controlled by `USE_MOCK_AI`. This is exactly why env documentation should be generated from code reality, not memory.
  - date: "May 2, 2026"
    sortDate: "2026-05-02"
    title: "Softening AIAssist Without Losing Discipline"
    summary: >-
      Updated CLAUDE.md so Oportuno no longer treats AIAssist as mandatory ceremony for every tiny change.
    sections:
      - label: "What I built / changed"
        text: |-
          Updated CLAUDE.md so Oportuno no longer treats AIAssist as mandatory ceremony for every tiny change. The external AI dev framework is still the source of truth for serious work, but the routing rule is now risk-based: use `/development` for non-trivial or unclear features, and `/bugfix` for confusing, recurring, production-impacting, data, email, extraction, privacy, Prisma, or external API bugs. Direct implementation is allowed for docs-only edits, copy tweaks, small CSS polish, comments, formatting, obvious one-file fixes, and no-behavior refactors.
      - label: "What I was trying to learn / decide"
        text: |-
          The debate started from comparing ProjectDesk's lighter AGENTS.md workflow with Oportuno's heavier CLAUDE.md workflow. ProjectDesk benefits from a simple solo-dev rhythm because the app is mostly personal task management. Oportuno is different: it touches company discovery, AI classification, web extraction, outreach email generation, Resend sends, Prisma data, and GDPR-sensitive workflows. The question was whether to copy ProjectDesk's lighter rules wholesale or keep Oportuno's extra process.
          
          The answer was a middle path. Oportuno still needs more guardrails than ProjectDesk, but the old "always run `/development` for any feature and `/bugfix` for any bug" rule was too blunt. It protected important work, but it also added friction to harmless edits. The better rule is: add process when it protects the app, skip process when it only adds ceremony.
      - label: "What went wrong"
        text: |-
          The old wording made the tooling feel like a gate instead of support. A typo, a tiny CSS adjustment, or a markdown clarification technically needed the same workflow as an email-sending feature. That creates a bad incentive: either overuse the process until it feels heavy, or quietly bypass the rules because they are unrealistic.
      - label: "Biggest challenge / bug"
        text: |-
          The hard part is drawing the line clearly enough that future work does not become subjective chaos. "Use judgment" alone is too vague. The updated CLAUDE.md now names the risky domains explicitly: data correctness, email behavior, scraping/extraction, privacy, Prisma, external APIs, scoring, company discovery, and core business flows. Those are the areas where AIAssist should slow us down on purpose.
      - label: "What I learned"
        text: |-
          Process should be proportional to risk. Oportuno's complexity is real, so it should keep a stronger steering file than ProjectDesk. But discipline is not the same as friction. The strongest workflow is the one that developers will actually follow when tired, moving fast, or doing something small.
      - label: "What I would do differently"
        text: |-
          I would have written the rule this way from the beginning: AIAssist is mandatory for unclear or high-impact work, optional for tiny safe edits. That keeps the framework valuable instead of turning it into background noise.
  - date: "May 2–3, 2026"
    sortDate: "2026-05-02"
    title: "Bulk Email Send: Architecture, Security, and the 3-Column Page"
    summary: >-
      The full bulk email send flow.
    sections:
      - label: "What I built / changed"
        text: |-
          The full bulk email send flow. From the networking view, users select companies, click "Contactar (X)", and land on `/networking/send` — a three-column page with an AI brief panel on the left, an email template editor in the center, and a recipients panel on the right. The AI generates a template from a structured brief using Claude Haiku. Users can upload a PDF or TXT as context for the AI (helps it write a better email) and attach a PDF that gets sent with every outgoing email. The actual send calls Resend, logs to EmailLog, and shows per-company results with success/failure status.
          
          The session started from a computer crash in the middle of implementation. I picked up from the transcript context and verified which of the 8 planned files had already been written before committing anything new.
      - label: "What I was trying to learn / decide"
        text: |-
          Several design decisions came out of debate before we wrote a line of code. The most important: where to put the bulk send path in the existing `/api/email/send` route. Two options — branch inside the existing route or create a new route. We chose to branch inside the existing route because the single-send path already had validation, EmailLog handling, and error patterns that would be identical. The bulk path early-returns at the top when it detects `recipients: []` in the body.
          
          The AI context file question took a while. The brief form had five fields (what I offer, objective, tone, recipient context, additional instructions). We removed "objective" and "recipient context" after debating UX — users can add those details in "additional instructions" if they want them, and fewer fields means faster flow.
          
          A security review flagged things I hadn't considered: no server-side file type validation beyond the browser's extension check, prompt injection risk via TXT context files, filename injection into Resend MIME headers, and no cap on the attachment array. We fixed all of them: PDF magic bytes validation, `</documento>` stripping from TXT content before it goes into the prompt, filename sanitisation, and a hard cap of 1 attachment per request.
      - label: "What went wrong"
        text: |-
          The `max_tokens: 512` limit on the template generator was too low when a large PDF was included as context. The model tried to process the document, ran out of output budget, and returned something that couldn't be parsed as JSON — which surfaced as "Resposta inesperada do modelo." Raised to 1024 and added a JSON extraction fallback: try direct parse first, then try to find a JSON object within the response using a regex. That handles cases where the model wraps the JSON in a brief explanation.
          
          The content length limit we added in the security pass was also too aggressive — 100,000 characters. A typical PDF base64-encodes to much more than that. The right limit is 6.9M characters (matching the 5MB client-side cap). That fix took a user error report to catch.
          
          The Next.js `bodySizeLimit` config only affects Server Actions, not Route Handlers. We added it to `next.config.ts` thinking it would help with large PDF attachments in the send route, but it doesn't. The actual limit for Route Handlers is the deployment platform's default (Vercel: 4.5MB). Documented in TODO.md.
      - label: "Biggest challenge / bug"
        text: |-
          The 300ms delay between sends in `bulkEmailService.ts` was applied unconditionally — including after the last recipient. For 100 recipients, that's 30 seconds of unnecessary wait at the end. Simple fix (index check in the loop) but easy to miss. The code reviewer caught it.
      - label: "What I learned"
        text: |-
          Client-side file type validation is UX, not security. An extension check in the browser (.pdf) is a hint to the user, not a gate. The gate is server-side magic byte inspection. Adding `Buffer.from(content, "base64").slice(0, 4).toString() !== "%PDF"` takes five lines and removes an entire attack surface.
          
          `max_tokens` on the AI call is about output, not context. You can send a very large input (the full PDF) and still get a short output (a 120-word email). The failure mode isn't "input too large" — it's "model starts answering, hits the output cap mid-JSON, returns malformed response." The JSON extraction fallback handles this gracefully.
      - label: "What I would do differently"
        text: |-
          Define the security surface in Phase 1 of the feature flow, not after code review. We caught everything eventually but a security review after implementation is more expensive than thinking about it at definition time. File uploads, AI prompt construction with user content, and external API calls are all signals that the security auditor should run.
  - date: "May 2, 2026"
    sortDate: "2026-05-02"
    title: "Making the Oportuno Rules Match the Product Risk"
    summary: >-
      Updated CLAUDE.md with explicit Oportuno danger zones: email sending, AI outreach copy, company discovery, contact extraction, Prisma/data changes, privacy/GDPR behavior, bulk...
    sections:
      - label: "What I built / changed"
        text: |-
          Updated CLAUDE.md with explicit Oportuno danger zones: email sending, AI outreach copy, company discovery, contact extraction, Prisma/data changes, privacy/GDPR behavior, bulk actions, scoring, imports/exports, and production configuration. Added a hard rule that real emails, bulk outreach, or external company contact must never happen without explicit approval. Also added privacy-safe logging rules, testing expectations by feature type, stronger branch scope guidance, and safer database migration notes.
      - label: "What I was trying to learn / decide"
        text: |-
          After softening the AIAssist rules, the next question was whether that made Oportuno too loose. The answer was no, as long as the risky parts are named clearly. Oportuno is not just a UI app. It can discover companies, extract contact data, generate outreach, send emails, write to a database, and affect real businesses. The rules needed to reflect that specific product surface.
      - label: "What went wrong"
        text: |-
          The previous CLAUDE.md had good broad principles, but some important risks were implied instead of explicit. "Use AIAssist for risky work" is correct, but future me needs to know what risky means inside this app. Email sends, contact extraction, Prisma changes, and GDPR behavior should not depend on memory or mood.
      - label: "Biggest challenge / bug"
        text: |-
          The challenge was not adding too much process after just agreeing to reduce process. The fix was to make the rules sharper, not heavier. Tiny edits stay lightweight. Dangerous areas get stronger guardrails. That keeps the workflow practical while making accidental real-world side effects much less likely.
      - label: "What I learned"
        text: |-
          Good project instructions are not generic. ProjectDesk needs sync, mobile UX, and PWA rules. Oportuno needs email, data, privacy, AI output, and Prisma safety rules. The best CLAUDE.md is shaped by what can actually go wrong in that specific app.
      - label: "What I would do differently"
        text: |-
          I would define danger zones as soon as a project touches the outside world. The moment an app can send email, store contact data, scrape public websites, or mutate production data, those actions need explicit safety rules before they become muscle memory.
  - date: "May 1, 2026"
    sortDate: "2026-05-01"
    title: "Company Enrichment: Category, Municipality, and Specialty"
    summary: >-
      Three new classification fields on every company: category (one of 15 canonical categories), specialty (one of 5–8 subtypes per category), and municipality (canonical name from...
    sections:
      - label: "What I built / changed"
        text: |-
          Three new classification fields on every company: `category` (one of 15 canonical categories), `specialty` (one of 5–8 subtypes per category), and `municipality` (canonical name from CITY_NAMES, or null if unmatched) plus `rawMunicipality` for future backfill. Category and specialty are classified by Claude Haiku once per pipeline job — not per company — so the cost is low. Municipality is derived from `administrative_area_level_2` in Google's address components and canonicalised against the CITY_NAMES list. The Networking filter bar now has category and specialty cascade dropdowns, with specialty disabled until a category is selected.
      - label: "What I was trying to learn / decide"
        text: |-
          The problem that drove all of this: the user searched "Barbeiro Oeiras" in Descobrir. The company got stored with `industry = "barbeiro"`. In Networking, they typed "barbearia" in the Setor filter. Zero results. "Barbeiro" and "barbearia" are the same thing and the filter couldn't see it.
          
          The core question was how to make companies findable regardless of which synonym was used during discovery. Three options were on the table: normalise the industry text at write time (handles casing, not synonyms), AI-classify a specialty from a predefined list (handles synonyms because "barbeiro" and "barbearia" both map to "Barbearia"), or control the Descobrir input with a dropdown (consistent but kills flexibility). The AI classification option won — same pattern as the opportunity classifier, and the predefined list is what makes it reliable. Without the list, the AI can hallucinate any value and you can't filter on it.
      - label: "What went wrong"
        text: |-
          The taxonomy design was the hardest part. "Clínica Estética" appeared under both Saúde and Beleza. A medical aesthetics clinic is healthcare. A beauty salon doing facials is beauty. The distinction is real and Claude can handle it, but only if the name is precise enough. "Clínica Estética" under Saúde was ambiguous. Renamed it to "Medicina Estética" — the name itself is now the signal.
          
          The municipality problem was longer. The early assumption was: store the raw search location, deduplicate via placeId. That's correct for deduplication but means `location` on the record ends up as whichever search ran last, and it's never canonical. You can't filter by municipality from a dropdown when the stored value is "rinchoa" for some companies and "Sintra" for others that are in the same place.
          
          The fix required understanding what Google Places actually returns. The Details API has `address_components` with `administrative_area_level_2` — which in Portugal gives the municipality name directly. "Rinchoa, 2635, Sintra" → `administrative_area_level_2: "Sintra"`. Then canonicalise against CITY_NAMES. If it matches, store "Setúbal". If not, store null and log the raw value for future backfill.
      - label: "Biggest challenge / bug"
        text: |-
          The Prisma DLL lock — again. Every time `prisma db push` ran, the dev server was holding the query engine DLL open, and the rename at the end of generate failed with EPERM. The TypeScript type definitions had already been written before the rename attempt, so `bun tsc --noEmit` passed even though the runtime binary wasn't updated. Symptom: pipeline runs crashed with "Unknown argument `category`" because the runtime client was stale while the compiler was happy. Fix: always stop the dev server before changing the schema.
      - label: "The Setor filter debate arc"
        text: |-
          The user reported the Barbeiro/barbearia mismatch. The first instinct was to fix the API filter from `equals` to `contains`. That helps for partial substring matches — "barb" would match both. But "barbearia" typed in full doesn't contain "barbeiro" and vice versa. `contains` would fix some cases and silently fail others.
          
          The real question was: what is the Setor filter actually filtering? The `industry` field stores the Descobrir search term verbatim. It's a pipeline artifact, not a semantic label. With specialty now in place, the right solution is to remove the Setor free-text input entirely and rely on category + specialty dropdowns. The `industry` field stays in the DB for provenance but stops being a user-facing filter.
      - label: "What I learned"
        text: |-
          The predefined taxonomy is load-bearing. Without it, the AI can return anything. With it, classification becomes a bounded choice and "Outros" as a fallback means you never get an invalid value. The classification log (`[specialtyService] Saúde / "clinica dentaria" → Clínica Dentária`) is the mechanism for refining the taxonomy — wherever "Outros" accumulates is where new options are needed.
          
          The `rawMunicipality` field was a deliberate choice not to throw away data. When a municipality doesn't match CITY_NAMES, store null as the filterable value and the raw value for later. When CITY_NAMES expands, backfill from `rawMunicipality` without re-running the pipeline.
      - label: "What I would do differently"
        text: |-
          Design the taxonomy before writing any code. I started implementing `categoryService` before the specialty taxonomy was locked, which meant circling back to add the specialty layer. The taxonomy is a product decision. It should come before the code.
  - date: "May 1, 2026"
    sortDate: "2026-05-01"
    title: "Networking as Primary Feature: Routing Refactor and Homepage"
    summary: >-
      Replaced the single tab-switching page with proper Next.js App Router routes: / (homepage control center), /networking (main workspace), /discover (pipeline).
    sections:
      - label: "What I built / changed"
        text: |-
          Replaced the single tab-switching page with proper Next.js App Router routes: `/` (homepage control center), `/networking` (main workspace), `/discover` (pipeline). The homepage is a dashboard with a filter sidebar, hero section, quick action cards, and recent pipeline searches. Networking is now the primary entry point — where users browse, filter, and select companies. Discover is secondary, used only to add new companies to the database. Homepage filters carry state into Networking via URL params when the user navigates.
      - label: "What I was trying to learn / decide"
        text: |-
          The product reframe happened through a debate about what the app is actually for. The original design led with Descobrir — type a search, run the pipeline, see results. That makes sense if the app is primarily a discovery tool. But the real value is the accumulated database of enriched companies that builds over time. Networking is where you actually work with that collection.
          
          Several debates shaped the final design. Whether homepage filters should be lightweight (category + municipality only) or full (all filters) — full sidebar won, no dead UI. Whether filters should auto-navigate or wait for explicit action — explicit action won, users control when things happen. Whether Networking should auto-load on mount or wait for "Aplicar filtros" — auto-load won, an empty table until the user acts is worse than showing all companies.
      - label: "What went wrong"
        text: |-
          The UI iterations were painful. The first version looked right in the code but cramped on screen — everything squeezed into a max-w-4xl container that was too narrow. I removed the max-width constraint entirely. That fixed cramped but introduced a new problem: on wide screens everything stretched to fill the full viewport. The fix was max-w-5xl (1024px) instead of max-w-4xl (896px). That 128px difference was the difference between "looks cramped" and "looks right."
          
          The hero illustration, step card sizes, and filter icon sizes all required seeing the rendered result to know they were wrong. I kept trying to reason about them in Tailwind class names — "max-w-4xl is 896px, sidebar is 256px, that leaves 640px, should be enough." It wasn't. Numbers don't tell you what looks right.
          
          The Suspense boundary for `useSearchParams` in the Networking page is worth documenting. Next.js App Router requires any component using `useSearchParams()` to be wrapped in `<Suspense>` — otherwise the build fails. The pattern is: outer page component exports with `<Suspense>`, inner component calls `useSearchParams`. Easy to get wrong; invisible when correct. I got it right because the architect plan had flagged it explicitly.
      - label: "Biggest challenge / bug"
        text: |-
          Stale filter state after back-navigation. User clicks a recent search on the homepage, navigates to `/networking?category=Saúde&municipality=Lisboa`. Networking reads params, initialises `NetworkingView` with those filters, the right companies appear. User presses back, clicks a different recent search — `/networking?category=Automóvel&municipality=Setúbal`. Networking re-renders. But `NetworkingView` is already mounted from the previous navigation — React doesn't remount just because the URL changed. `useState` initial value runs once. The component shows Saúde/Lisboa results while the URL says Automóvel/Setúbal.
          
          Fix: pass a `key` prop to `NetworkingView` derived from the URL params. When the key changes, React unmounts and remounts from scratch. It's a hammer, but it's the right hammer — the component's internal state is mount-time-only by design, so forcing a remount on param change is exactly correct.
      - label: "Code review findings — 2 blocking bugs caught post-implementation"
        text: |-
          The routing refactor went through a code review that came back NEEDS CHANGES. Two blocking issues, three important ones. None of which I'd caught during implementation.
          
          First blocking issue: `/api/stats` was computing `companyCount` by counting `PipelineCompany` rows where `status: "DONE"`. The intent was to show how many companies were found in each search. The bug: `status: "DONE"` rows can still have `companyId: null` if persistence failed partway through. The correct count is `companyId: { not: null }` — rows where a `Company` record was actually saved. With the wrong filter, every pesquisa recente in the dashboard showed "0 resultados". A one-line Prisma query change fixed it.
          
          Second blocking issue: the homepage builds `/networking?industry=mecanico&municipality=Setúbal` for recent search clicks, but `NetworkingContent` was only reading `category` and `municipality` from `searchParams`. `industryParam` was completely ignored — the param existed in the URL but nothing read it or forwarded it to the filter state. Silent data loss on every recent search click.
          
          The important issues: stats error state was indistinguishable from "no data" (fixed with `statsError`), `toLocaleString` called on a `number | "—"` union (fragile, fixed), and `companies={[]}` on the networking panel was undocumented (added a comment).
          
          All five issues were invisible during implementation. The companyCount bug only shows up in the data. The dropped param only shows up when you click a recent search and notice the industry filter is blank. This is exactly what code review catches and self-review misses.
      - label: "What I learned"
        text: |-
          The two-state homepage (filter → navigate → Networking) only works if the filters on the homepage look connected to what happens next. If you set filters and nothing visibly changes until you click a button, users will wonder if the filters are doing anything. The sidebar and the CTA button had to feel like parts of the same action.
          
          With routes, each page owns its own state. Navigating from Discover to Networking clears the selected company. That's correct behavior — but it's a visible change from the old tab-switching model where everything shared one parent. Worth knowing before a user asks why their selected company disappeared.
      - label: "What I would do differently"
        text: |-
          Test UI layout with the browser earlier, not with Tailwind class reasoning. The max-w problem, icon sizes, step card readability — all invisible in code and obvious in the browser. Settle the "auto-load or wait" debate before building, not midway through. That one change touched NetworkingView's useEffect, the initialFilters prop design, and the key prop strategy. Cleaner to decide once.
  - date: "May 1, 2026"
    sortDate: "2026-05-01"
    title: "Git Debt, Branch Discipline, and Workflow Rules"
    summary: >-
      Updated the git workflow rules in CLAUDE.md.
    sections:
      - label: "What I built / changed"
        text: |-
          Updated the git workflow rules in CLAUDE.md. Dropped the mandatory PR requirement for solo development. Made `bun run build` the gate before merging to main instead of just type checking. Allowed direct main commits for docs and typos. Tightened the CHANGELOG rule to "user-visible changes" rather than "all feat commits." Updated the journal rules to trigger on any meaningful decision or debate.
      - label: "What I was trying to learn / decide"
        text: |-
          I'd been using a workflow borrowed from team projects — PRs for everything, squash and merge, strict branch naming — and it was adding friction without adding value in a solo context. The question was whether to simplify or whether the discipline of the overhead was the point. I looked at a workflow from another project (Vercel + Supabase) and debated each rule against our actual context. Most of it applied directly. The main adaptations: `bun run build` as the gate (not just `npm run build`), keeping CHANGELOG but scoping it to user-visible changes, keeping the `test/` branch type.
      - label: "What went wrong"
        text: |-
          The branch scope problem was real and embarrassing. `feat/async-pipeline` had been open accumulating work across five distinct features — async pipeline, category enrichment, specialty taxonomy, homepage redesign, UI polish — never committed. That's weeks of work on one branch. The rule "a branch should be mergeable within a few days" didn't exist yet.
          
          When I finally committed everything I used Option C — created `feat/enrichment-and-homepage` from the current state and committed in layers. Clean in principle. In practice, several files spanned multiple commits and I couldn't split them without `git add -p` interactive staging, which doesn't work well through Claude's Bash tool. Pragmatic choices were made.
          
          Then I tried to commit CLAUDE.md directly to main (docs-only, now allowed under the new rules) and got a conflict when stash-popping. Root cause: the Prisma client had been regenerated with the new schema on the feature branch, but `pipelineService.ts` on main still referenced the old field names. The pre-commit hook checks the entire project, not just staged files. Even a markdown-only change blocks if anything else in the working tree fails TypeScript. Ended up committing CLAUDE.md on the feature branch instead.
      - label: "Biggest challenge / bug"
        text: |-
          The `.next/dev/types/validator.ts` TypeScript error that blocked a commit midway through the layered sequence. A generated Next.js file with a syntax error — written by the dev server based on the new routes in a partially-generated state when the server was killed mid-write. Fix: `rm -rf app/.next`. The file isn't source-controlled, it gets regenerated. But it blocked a commit in the middle of a layered sequence, which was stressful.
      - label: "What I learned"
        text: |-
          "Direct to main for docs" only works cleanly when main is fully green and the Prisma client hasn't been regenerated with a schema that main doesn't know about. In that state, just put the docs change on the feature branch.
          
          The practical rule for layered commits: if a file spans multiple features (pipelineService.ts touched by both async and enrichment), it goes with the last feature that meaningfully changed it. Not ideal but practical when `git add -p` isn't available.
      - label: "What I would do differently"
        text: |-
          Create branches earlier and more narrowly. Each feature felt small when it started and only revealed its scope as it grew. The branch scope rule ("mergeable within a few days") is the right constraint, but it requires acting when scope grows past the threshold — not just noting it.
          
          
          ## Portfolio notes
          
          Oportuno is a good portfolio project because it covers a full product stack with real technical depth:
          
          - **Data pipeline**: Google Places → enrichment → PostgreSQL via Prisma. Category, specialty, and municipality classification using Claude Haiku.
          - **AI integration**: Claude-generated outreach emails in European Portuguese, GDPR-compliant. Classification pipeline with predefined taxonomies to prevent hallucination.
          - **Web scraping**: public contact email extraction from company websites.
          - **Email sending**: Resend API with per-send result logging.
          - **Modern frontend**: Next.js App Router, TypeScript, Tailwind CSS, proper route-based navigation.
          
          What makes it interesting beyond the stack: it's opinionated about its market. Portuguese SMEs, a specific lead qualification theory (businesses without digital presence are high-opportunity targets), and a workflow built around that theory rather than a generic CRM. That opinionation is what makes it a product rather than a coding exercise.
  - date: "April 28–30, 2026"
    sortDate: "2026-04-28"
    title: "Networking View, Component Extraction, and Pagination"
    summary: >-
      A significant structural change that sat uncommitted for several days.
    sections:
      - label: "What I built / changed"
        text: |-
          A significant structural change that sat uncommitted for several days. Extracted the monolithic `page.tsx` into separate components — `DiscoverView`, `NetworkingView`, `CompanyDetailPanel`, `shared` — and added a two-mode navigation model to the page. Built the Networking view as a proper company browser with pagination, full filter bar, checkbox selection, and bulk action stubs.
          
          The Networking view queries the full Company table rather than showing results from a specific pipeline run. Results come back paginated. The companies API route was extended with `page` + `pageSize` params, case-insensitive filtering on all string fields, and a `hasEmail` filter. Also moved `PORTUGUESE_CITIES` and `normaliseCityKey` from inside `placesService.ts` to a new `lib/cities.ts`, making them importable by the UI.
      - label: "What I was trying to learn / decide"
        text: |-
          The Networking view existed as a concept — browse the accumulated company database — but hadn't been built. The question was whether to wait until the enrichment work (category, specialty, municipality) was done before building it, since those fields would make the filter bar much more useful. The decision was to build the structure now — table, pagination, panel integration, filter wiring — and accept that the filter options would be limited until enrichment landed. The structure is the hard part; adding more filter options is easy once the wiring exists.
      - label: "What went wrong"
        text: |-
          The work sat uncommitted for several days. Uncommitted work is at risk of being lost, invisible to git history, and makes context-switching harder. Each day I planned to clean it up and kept adding more. By the time this session started, the working tree had changes across ten-plus files representing several interleaved concerns. Splitting them cleanly into layered commits after the fact required real effort.
          
          The other problem: building the networking view before the enrichment fields meant the filter bar launched with a free-text "Setor" input that filtered on the raw `industry` field. As discovered later, that filter is essentially useless — "barbearia" won't match "barbeiro." The structural work was right. The filter choice was wrong and should have been flagged earlier.
      - label: "Biggest challenge / bug"
        text: |-
          The `PaginatedResponse<T>` type had to work cleanly on both sides — the API route returns it, the component consumes it. The companies route previously returned a flat array. Changing to `{ items, total, page, pageSize }` required updating the route, the response handler in NetworkingView, and the type system simultaneously. The awkward part: the old flat-array response was still used by the job-scoped fetch that shows companies from a specific pipeline run in Descobrir. So the route needed to handle both cases — `?jobId=X` returns a flat array, no `jobId` returns paginated. That conditional branch is a bit odd but it's the right separation: Descobrir shows a pipeline run's results, Networking browses the whole database.
      - label: "What I learned"
        text: |-
          Component boundaries matter more than component count. `CompanyDetailPanel` is stateless — it takes props and fires callbacks. If it had owned state like "is email draft visible," it would have been harder to reuse across Descobrir and Networking. Keeping it purely presentational means both views can share it without fighting over state ownership.
          
          Pagination has more non-obvious pieces than it looks like: the page window calculation with ellipsis gaps, the result range label, keeping controls disabled during loading, resetting to page 1 when filters change. None of these are hard individually but together they're enough to get wrong.
      - label: "What I would do differently"
        text: |-
          Commit as I go, not in a batch. The component extraction could have been one commit, the API changes another, the networking view another. Checkpoints make history readable and protect against loss. The way it worked, everything lived in uncommitted state for days and required a complex untangling later.
  - date: "April 27, 2026"
    sortDate: "2026-04-27"
    title: "Pipeline Debugging, City Lookup, Pagination, and Autocomplete"
    summary: >-
      Four things that ended up being tightly connected.
    sections:
      - label: "What I built / changed"
        text: |-
          Four things that ended up being tightly connected. A fix for the Descobrir button spinning forever and never showing results in local development. A `lib/cities.ts` module with coordinates for 30 Portuguese cities and municipalities that fixed searches returning only one or two results instead of twenty. Pagination for the Google Places API so searches fetch up to three pages (60 results) instead of stopping at the first 20. And a filter-as-you-type location autocomplete in the sidebar using the same city list.
      - label: "What I was trying to learn / decide"
        text: |-
          I wanted to understand how a self-triggering dev mechanism interacts with the polling model the client already has. The async pipeline was built around "browser posts, server responds immediately, client polls" — but the dev environment broke that contract at step zero because nothing ran the worker. The goal was to make the dev path fit the same observable contract without changing the production behaviour.
      - label: "What went wrong"
        text: |-
          Two things, and the worst was the invisible Unicode combining characters.
          
          I needed to strip diacritics from city names using a character range in a regex. Every time I wrote the regex literal in the editor, the Unicode escape sequences were stored as the actual combining characters — invisible in the file, not what the code wanted. TypeScript didn't flag it. Tests didn't catch it immediately. I reproduced the fix multiple times and each time the same invisible corruption appeared. The solution was using the `RegExp` constructor with a string: `new RegExp("[\\u0300-\\u036f]", "g")` — which forces the escapes to be interpreted as string escapes before becoming a regex. I had to verify the fix at the hex byte level with PowerShell before trusting it.
          
          The second mistake was a fire-and-forget trigger I'd written as `await fetch(workerUrl).catch(() => {})`. The `.catch()` signals "I don't care what happens here" — which visually reads as detached and background. The `await` was right there in the same line, but the `.catch()` at the end dominated my reading of it. The actual behaviour was the opposite: the full worker run had to complete before the HTTP response reached the browser. The client was waiting for a jobId to start polling and getting nothing for up to 90 seconds before timing out. It took a code review to catch it. The fix was `void runWorkerTick(job.id)` — truly detached. The lesson isn't "use void instead of await/catch." It's that a pattern that looks like fire-and-forget isn't unless `await` is absent.
      - label: "Biggest challenge / bug"
        text: |-
          The two-bug diagnosis on the infinite spinner. When I saw no worker activity in the dev logs, the cause seemed obvious: Vercel Cron doesn't run locally, so nothing was triggering the worker. I added the dev-path direct invocation, tried the button again, and the spinner kept spinning. That was the disorienting moment — I'd found and fixed the cause, and nothing changed. There was a second independent bug hiding behind the first.
          
          The second cause was `claimNextJob` picking the oldest PENDING job rather than the one just created. This only manifests when there's a pre-existing stuck job in the database for the same industry/location pair. A fresh database would never hit it. You only discover it after you've already run a failing search, created a stuck job, and tried again with a dirty DB state — which is exactly the state development produces. Each fix ran against a database that had accumulated the residue of every previous failure.
          
          The resolution was threading the `jobId` down from the pipeline route to `claimNextJob` so the worker claims the specific job just created. Once both fixes were in place together, the spinner advanced. Neither alone would have been enough.
      - label: "What I learned"
        text: |-
          The `onMouseDown` pattern is the correct solution when a dropdown needs to register a click before the input's `onBlur` fires and closes the list. `onClick` fires after `onBlur`, so the list disappears before the selection registers. `onMouseDown` fires before `onBlur`. A well-known React pattern that you either know or you spend an hour debugging.
          
          `void somePromise().catch(...)` and `await somePromise().catch(...)` are not the same thing. The `catch` handles rejection in both cases, but only `void` actually detaches. If `await` is present, you're blocking regardless of what you put after the call.
      - label: "What I would do differently"
        text: |-
          Guard against resetting a `RUNNING` job from the start. The original fix reset any job that wasn't DONE — including RUNNING jobs. If the user clicked Descobrir while a worker was mid-run, the reset would race with the active worker. The final version guards correctly, but it was caught by code review, not by me.
  - date: "April 26, 2026"
    sortDate: "2026-04-26"
    title: "Architecture Debate and the Async Pipeline Design"
    summary: >-
      Nothing shipped.
    sections:
      - label: "What I built / changed"
        text: |-
          Nothing shipped. The entire session was design and structured debate — and it was more productive than most days where I wrote code. I went through a formal architecture review of the codebase, defended it against structured counterarguments, then defended my revised positions against a second round of more refined pushback. By the end I had a concrete design for the async pipeline: a job state machine in the database, step-level progress tracking, a client-driven polling model, and idempotency keys on both job creation and email sending.
          
          I also fixed the dev tooling setup — the spark-ai-assist skills (the `/development`, `/bugfix` workflows) weren't being discovered because the additional working directory was pointing to the wrong folder.
      - label: "What I was trying to learn / decide"
        text: |-
          The core question was: what does this pipeline actually need to survive first real usage? The first version — Places API, scraper, Claude, all in one synchronous request — was fine for demos. But it had no recovery, no partial results, and no way to know what happened if it failed. The architecture review forced me to stop describing the problem in terms of file structure ("pipelineService is too big") and start describing it in terms of failure modes ("there's no durable state, so you can't resume or retry").
          
          I was also testing whether I could hold positions under pushback. Some concessions were genuine — I had real blind spots around what "async" means for the user experience. Others were about sharpening framing rather than changing position.
      - label: "What went wrong"
        text: |-
          The first version of my review was reasoning about files more than systems. I flagged coupling and recommended a restructure — which is the right call — but I couldn't explain the actual failure mode. I was naming symptoms without naming the cause. The cause is that the pipeline is stateless and synchronous: without a durable job record, you can't resume, retry safely, inspect progress, or make the system observable. That should have been the first sentence of the critique.
          
          The other miss was on validation. I argued for separating transport validation from domain validation, which is the right principle. But then I implied this should be done now. That contradicted everything I'd said about not adding abstractions before they're needed. The right call was: one validation layer today, split it when a second entry point appears.
      - label: "Biggest challenge / bug"
        text: |-
          The moment that landed hardest was the meta-critique: "you're still reasoning in files more than boundaries." That was true, and it explained all the other inconsistencies. When I described the coupling problem, I said "pipelineService reaches into placesService and scraperService." That's a file-level observation. The boundary-level observation is: "the service layer has no defined interface contract, so nothing prevents a caller from passing in raw HTTP shapes." Those sound similar but lead to completely different recommendations — one leads to "reorganise the files," the other leads to "define the contract and enforce it in the type system."
          
          That shift — from thinking about where files live to thinking about what contracts exist between layers — is the thing I'll carry forward.
      - label: "What I learned"
        text: |-
          Conceding a point and replacing it with something better is different from just conceding. In the first debate round I admitted the error handling gap and stopped. In the second round the pushback was: you conceded but didn't replace. That was right. A good review doesn't just identify what's missing — it defines the minimum viable version of what should be there.
          
          "Async" is not a backend architectural decision — it's a product decision. Moving scraping off the synchronous path means the API can't return results immediately, the frontend needs a polling model, and the user experience fundamentally changes. I'd been framing it as a backend concern ("Playwright blocks the request handler") when the real question is: what does the user see and when?
      - label: "What I would do differently"
        text: |-
          Separate architectural reasoning from implementation recommendations from the start. The review mixed "move types to be colocated" (implementation preference) with "the pipeline has no durable state" (load-bearing structural finding). The first kind of recommendation is easy to dismiss as stylistic; the second isn't. Mixing them makes the structural argument weaker by association.
  - date: "April 26, 2026"
    sortDate: "2026-04-26"
    title: "Pipeline Design: CompanyService and Schema Foundations"
    summary: >-
      The first implementation piece from the async pipeline design: persistCompany() in a new companyService.ts, and the schema changes that back it.
    sections:
      - label: "What I built / changed"
        text: |-
          The first implementation piece from the async pipeline design: `persistCompany()` in a new `companyService.ts`, and the schema changes that back it. The function upserts on `placeId`, always updates classification fields, and null-guards enrichment fields so a failed scrape never overwrites a previously found email or phone number. The pipeline service now calls `persistCompany()` instead of doing a raw upsert inline. I also renamed `website` → `websiteUrl` and `phone` → `phoneNumber` on the schema for consistency, and added `lastEnrichedAt`.
      - label: "What I was trying to learn / decide"
        text: |-
          The design session the day before had produced a concrete architecture but left some operational decisions open: what does the null-guard logic actually look like, and where does the upsert boundary live? The question was whether `persistCompany` should be a thin wrapper around a Prisma call or a real domain function with its own contract. It ended up as the latter — the `CompanyData` interface is the contract, and the function enforces the null-guard decisions explicitly.
      - label: "What went wrong"
        text: |-
          Two things. First, the schema rename broke more than expected. `website` and `phone` existed in four places simultaneously — the Prisma schema, the `CompanyData` interface, the inline upsert in `pipelineService.ts`, and every reference in `page.tsx`. Renaming in the schema and interface without simultaneously updating the upsert body left TypeScript errors pointing at half-migrated references. The correct sequence is: update schema, update interface, update all call sites, then regenerate the client.
          
          Second, `bunx prisma generate` failed with an EPERM error because the dev server had the query engine DLL locked. I'd seen this before. Still caught me off guard.
      - label: "Biggest challenge / bug"
        text: |-
          The pressure-test of the fire-and-forget model exposed a real flaw. I'd assumed `runPipeline(job.id)` would keep executing after `NextResponse.json()` returned — which is true in a long-running Node.js process but not in serverless environments where execution is frozen after the response. Vercel freezes function execution once the response is sent. A scraping job that takes 40 seconds would be killed.
          
          The fix is the separation that was already in the design: `POST /api/pipeline` only creates the job record, a cron-triggered worker runs it independently. This is the right design regardless of hosting environment — "accept work" and "do work" are different responsibilities.
      - label: "What I learned"
        text: |-
          The race condition on the idempotency key was the most instructive moment. The naive pattern — find existing job, create if not found — is not safe under concurrent requests. Both requests can pass the "find" check before either creates the record, then both try to create, and one fails with a unique constraint violation. The correct pattern is optimistic create: try to insert, catch the constraint error, find and return the winner. The constraint error only happens under true concurrency and the recovery is cheap.
          
          `lastEnrichedAt` and `updatedAt` are not the same field. `updatedAt` fires on any write. `lastEnrichedAt` marks when the pipeline last processed this company — the field you query when deciding whether to re-scrape. Conflating the two would make re-enrichment logic unreliable.
      - label: "What I would do differently"
        text: |-
          Write the `CompanyData` interface before writing the upsert, not after. The interface is the contract — it forces you to name every field and decide upfront whether it's required or nullable. The inline upsert in the old `pipelineService.ts` skipped that step and just passed `detail.website` directly to the Prisma call, which meant the null-guard decision was implicit and wrong. Formalising the interface first makes those decisions visible.
  - date: "April 26, 2026"
    sortDate: "2026-04-26"
    title: "Async Pipeline: Job Queue, Worker Endpoint, Polling UI"
    summary: >-
      The full async pipeline system, end to end.
    sections:
      - label: "What I built / changed"
        text: |-
          The full async pipeline system, end to end. `POST /api/pipeline` creates a `PipelineJob` record and returns a `jobId` immediately. A cron-triggered worker endpoint picks up PENDING jobs, seeds per-company rows into `PipelineCompany`, enriches each one through the Places API and scraper, and marks the job DONE. The front end polls every two seconds while a job is running and shows a live progress counter on the Descobrir button. The schema gained two new models and two new enums.
      - label: "What I was trying to learn / decide"
        text: |-
          The design from the previous sessions was concrete. The question was whether it would survive contact with the actual constraints of Next.js API routes and Prisma — whether any of the decisions would break under implementation pressure.
      - label: "What went wrong"
        text: |-
          The session was interrupted by a window crash before the work could be committed. The changes sat in working tree state with no history. Not a code problem — an environment problem — but a reminder that committing incrementally matters even when nothing is "done" yet.
      - label: "Biggest challenge / bug"
        text: |-
          The rename cascade. `website` and `phone` existed in four places simultaneously: the Prisma schema, the `Company` TypeScript interface, the inline upsert in `pipelineService.ts`, and every reference in `page.tsx`. Renaming in the schema without touching all the other sites left TypeScript errors pointing at half-migrated references. The interface is the choke point — update it first and let the compiler guide the rest.
      - label: "What I learned"
        text: |-
          The `CRON_SECRET` bypass for local development is the right pattern for cron-protected endpoints. Requiring the secret in dev adds friction with no security benefit — the endpoint isn't reachable from the internet locally. Skipping the check in dev and enforcing it everywhere else is a clean separation of concerns.
          
          Vercel Cron configuration belongs in `vercel.json` at the project root, not in `app/`. The cron schedule is infrastructure configuration, not application configuration.
      - label: "What I would do differently"
        text: |-
          Commit more often. The full session's work — schema changes, two new service files, two new API routes, `vercel.json`, UI polling logic — should have been at least three separate commits. A crash between commits loses the entire changeset.
  - date: "April 26, 2026"
    sortDate: "2026-04-26"
    title: "Infrastructure, Debugging, and Developer Experience"
    summary: >-
      A day of infrastructure with no visible product output — the kind of session that makes every future session cleaner.
    sections:
      - label: "What I built / changed"
        text: |-
          A day of infrastructure with no visible product output — the kind of session that makes every future session cleaner. Cut the v0.3.0 release, restructured the git hooks into a pre-commit/pre-push split, fixed a CI failure caused by a misplaced Prisma dependency, debugged a Bun-on-Windows crash that was blocking every push, and set up a permission allowlist to reduce approval friction. I spent more time debating the right approach to each of these than implementing them, which was the right tradeoff.
      - label: "What I was trying to learn / decide"
        text: |-
          Where the real value of safety gates sits in a solo workflow. The question kept recurring in different forms: should tests run before every commit? What's actually worth protecting, and what's just friction that erodes the habit? I didn't have a clear answer at the start. By the end I did: the value of a gate is a function of what it catches and when it matters, not how frequently it runs.
      - label: "What went wrong"
        text: |-
          Three things broke in sequence and each revealed something the previous hadn't.
          
          The v0.3.0 release commit was blocked by the pre-commit hook running the full test suite, which crashed Bun with a stack overflow before any tests ran. Not a test failure — a Bun-on-Windows bug with fork worker spawning. The hook couldn't tell the difference, so it blocked the commit. That's when I realised the hook was doing too much.
          
          Once I restructured the hooks and got the release committed, CI failed. `opportunityService.test.ts` crashed with `Cannot find module '.prisma/client/default'`. The service imported `{ Opportunity }` from `@prisma/client` — an enum type — even though it never touched the database. In CI, the generated client doesn't exist until you run `prisma generate`, which the workflow didn't do. The fix was removing the import and using the string union type already in `types.ts`. One line changed.
          
          Then the pre-push hook blocked the local push again because of the same Bun worker crash. I tried several Vitest pool options. Each failed differently.
      - label: "Biggest challenge / bug"
        text: |-
          The Vitest pool debugging. I started with the right diagnosis — Bun on Windows couldn't spawn worker processes — but drew the wrong conclusion. I framed the problem as "which kind of worker process does Vitest support that Bun can handle?" and worked through the list: `forks` crashed with a stack overflow, `threads` crashed with `null is not an object`, `vmThreads` had the same issue. Each failure was different enough to make me think the next option might work.
          
          The thing that unblocked me was reframing. Not "what kind of workers does Bun support" but "what if Bun can't run workers at all?" That shifts the question from pool type to whether workers need to run. `--no-file-parallelism` runs test files sequentially in a single process — no workers spawned, broken worker system bypassed entirely. Tests went from crashing to 52/52 in under four seconds. The fix was there the whole time; I couldn't see it because I was searching inside the wrong category.
      - label: "What I learned"
        text: |-
          The value of a safety gate is a function of what it catches, not when it runs. Pre-commit type checking catches structural breaks early and costs under two seconds — high value, low cost. Pre-commit test runs on a WIP commit in a solo project catch nothing that matters and cost real time. Moving tests to pre-push means they run once before code leaves the machine, which is the moment a passing-test guarantee actually matters.
          
          A service that does pure computation should not depend on generated database code. `opportunityService.ts` takes a URL and some HTML and returns a classification. It has no reason to know Prisma exists. The fact that the return type happened to be a Prisma enum was an accident of how the code was first written. Replacing it with the string union type wasn't a degradation — it was removing a dependency that shouldn't have been there.
      - label: "What I would do differently"
        text: |-
          Separate infrastructure from release work. The v0.3.0 release should have been a clean commit-tag-push with nothing else in flight. Instead I was discovering hook problems and debugging Windows crashes at the same time as trying to cut the release. A better sequence: ship the release cleanly, then open a `chore/dev-workflow` branch for the infrastructure improvements.
          
          Check CI after every push. The opportunityService Prisma import had been there since the beginning and would have failed on the very first CI run. I only found it because I pushed something else. `bun run test` locally doesn't catch "does this work in a fresh environment without generated files" — which is exactly what CI tests.
  - date: "April 26, 2026"
    sortDate: "2026-04-26"
    title: "Renaming LeadBridge to Oportuno"
    summary: >-
      Renamed the project across the codebase — CLAUDE.md, README, TODO, .env.example, page title, page header, email templates.
    sections:
      - label: "What I built / changed"
        text: |-
          Renamed the project across the codebase — CLAUDE.md, README, TODO, `.env.example`, page title, page header, email templates. Eight files, twelve occurrences. Mechanical and clean, but a real product decision underneath.
      - label: "What I was trying to learn / decide"
        text: |-
          "LeadBridge" was a working title — descriptive but generic. It says what the product does but not who it's for. "Oportuno" is a Portuguese word meaning "timely" or "opportune." It works as a double meaning: the businesses you find are ones for whom outreach is literally opportune (they lack a website, they need one now), and the timing of the outreach is opportune before a competitor gets there. It's also inherently Portuguese, which grounds the product in its market.
          
          The real decision was whether to commit to a real name at all or keep the placeholder until the product was more finished. I decided early was better — every day you work with a placeholder name is a day the project feels like an exercise rather than a product.
      - label: "What went wrong"
        text: |-
          Nothing went wrong technically. The only thing to verify was that the email templates used the old name in subject lines and body copy (they did), and that the database name wasn't set from any of these files (it wasn't — it comes from the connection string in `.env.local`).
      - label: "What I learned"
        text: |-
          There's value in naming a project something that means something, even in solo development. "LeadBridge" felt like a placeholder every time I read it in the code. "Oportuno" doesn't. The name appears in places where it has to earn its presence — page titles, email subjects, CLAUDE.md headers — and it holds up.
      - label: "What I would do differently"
        text: |-
          Do the rename earlier. Working title to real name should happen before you write any email copy that uses the name, or at least before you build templates that hardcode it. The templates had the old name in subject lines that were going out in test emails.
  - date: "April 25, 2026"
    sortDate: "2026-04-25"
    title: "Tests, Repo Cleanup, and Infrastructure Fires"
    summary: >-
      Three things that should have been done earlier.
    sections:
      - label: "What I built / changed"
        text: |-
          Three things that should have been done earlier. A 52-test suite covering all four core services — opportunity classification, email extraction, email generation, and template rendering. A repo structure fix: `app/` had its own nested `.git` directory, which meant none of the actual code was tracked by the root repository. And a PostgreSQL setup that had silently stopped working, which blocked everything until I fixed it.
      - label: "What I was trying to learn / decide"
        text: |-
          I wanted to understand what a useful test suite actually looks like for a service layer — not "have tests" as a checkbox, but tests that would catch real bugs. I'd written services without testing them properly and the codebase was reaching the size where that debt starts hurting.
      - label: "What went wrong"
        text: |-
          Almost everything broke in sequence, and each failure looked like a different problem until I was far enough in to see they were all the same problem: the environment had never been properly set up end-to-end.
          
          PostgreSQL 18 was installed but not registered as a Windows service — it wasn't running. The error wasn't "service not running"; it was a connection refused that looked like a bad connection string. I spent time checking `.env` before checking whether Postgres was up. Once I started it and tried `prisma migrate dev`, it failed with a privilege error — the DB user didn't have `CREATEDB`, which Prisma needs for its shadow database. That looked like a Prisma config issue. Then when I fixed the user and ran `prisma generate`, it hit an EPERM error on the query engine DLL because the dev server had it locked. Each failure had a different shape, so each felt like a fresh problem. The through-line was that I'd been assuming the environment worked and it never had.
      - label: "Biggest challenge / bug"
        text: |-
          The test suite caught a real bug I hadn't noticed: `extractEmail` was using `$("a[href^='mailto:']")` as its CSS selector, which is case-sensitive. A page with `MAILTO:` in uppercase would silently return null — no error, just nothing found.
          
          What made this easy to miss: there was a line further down the function — `.replace(/^mailto:/i, "")` — with the `/i` flag. When I read the function before, my eye landed on that `/i` and I registered "handles case" without checking what it applied to. It strips the prefix from a string you've already extracted. If the selector never matched, you never get a string. The case-insensitivity was real — just on the wrong operation, one step too late. The fix was switching the selector to a `.filter()` with `.toLowerCase().startsWith("mailto:")`. The test that caught it was a two-line fixture with an uppercase MAILTO href.
          
          I wouldn't have written that test if I hadn't been forced to think about the function as a consumer rather than as the author who already knew what it was supposed to do.
      - label: "What I learned"
        text: |-
          Writing tests after the fact is genuinely useful even when you think you know what the code does. The process forces you to read the code as a consumer and surfaces assumptions you didn't know you'd made. The `extractEmail` bug wasn't found by reasoning about edge cases — it was found by looking at the function from the outside and asking "what inputs would break this?"
          
          The practical difference between `prisma migrate dev` and `prisma db push` matters on a solo MVP. I'd been using `migrate dev` by reflex because it felt more correct. But it requires `CREATEDB`, generates migration files, and adds operational overhead I don't need when there's no production data to protect. `db push` applies the schema directly. Same result, less friction.
      - label: "What I would do differently"
        text: |-
          Set up the test suite on day one, even with just one test per service. The infrastructure — vitest config, path aliases, a single passing test — is the hard part. Adding tests incrementally is easy once that's in place. Writing 52 tests retroactively is fine but you've already forgotten some of the edge cases you thought about while building.
  - date: "April 24, 2026"
    sortDate: "2026-04-24"
    title: "First Working Version"
    summary: >-
      The first end-to-end working version of Oportuno.
    sections:
      - label: "What I built / changed"
        text: |-
          The first end-to-end working version of Oportuno. You can search for businesses by industry and city, see them in a list, select one, generate an outreach email in European Portuguese, and send it. The pipeline that drives it — Places API → website scraper → Claude → Resend — all runs in one synchronous request. It's slow, it will break under load, and it has no job tracking. But it works from first search to sent email, which is the only thing that matters on day one.
      - label: "What I was trying to learn / decide"
        text: |-
          This was my first time building an AI-integrated product end-to-end rather than just calling an API in isolation. The question I was genuinely curious about was: where does the AI fit in a real workflow? Not as a demo, but as a thing the product actually depends on. The answer I landed on is that the mock mode (`USE_MOCK_AI=true`) is as important as the real mode — it's what makes the rest of the product developable without burning API budget on every page reload.
      - label: "What went wrong"
        text: |-
          I added a decorative icon column on the left side of the layout for visual structure. It looked good to me. It confused everyone who saw it — they tried clicking it, nothing happened, and the app felt broken. I removed it entirely. The other mistake was a `useEffect` that called `fetchCompanies` on page load, which made the company list populate on every open from whatever was last in the database. It looked like the app was persisting state when it wasn't. Magic behavior I hadn't intended and couldn't explain.
      - label: "Biggest challenge / bug"
        text: |-
          Anchor link scrolling inside a nested scroll container. I had a "Ver Rascunho" link pointing to `#email-draft` — tested it, it worked, moved on. Then I gave the main content area `overflow-y-auto` so it could scroll independently of the sidebar, and the link stopped working entirely. Clicking it did nothing. No error, no partial scroll, just silence.
          
          I checked the DOM — the `id` was there. I tried `scroll-behavior: smooth` on the container. Still nothing. I tried `window.scrollTo`. Still nothing, because the window wasn't what needed to scroll.
          
          The actual cause: `<a href="#id">` tells the browser to scroll the document root. When the target element lives inside a div with `overflow-y-auto`, that div is a separate scroll context the document root knows nothing about. The anchor scroll reaches the root, finds nothing to scroll, and silently gives up. The fix was an imperative `element.scrollIntoView({ behavior: "smooth" })` on click — it talks directly to the element and works regardless of scroll context. Standard browser behavior, but it only bites you once you've built something that treats the page as multiple independent scroll regions.
      - label: "What I learned"
        text: |-
          Starting with PostgreSQL instead of SQLite was the right call, even though it added friction upfront. The schema is simple but switching databases at deployment time would have been a real headache. Targeting Postgres from day one means the production deploy is a connection string change, not a migration project.
          
          The `USE_MOCK_AI=true` pattern is essential for anything with an AI call in the critical path. Fast, deterministic, free — it's what makes the rest of the product developable without turning every UI test into an API expense.
      - label: "What I would do differently"
        text: |-
          Browser autocomplete is not the same as application state, and I wasted time figuring that out. After removing the auto-fetch effect, the inputs still showed values on reload — from the browser's cache, not the app. `autoComplete="off"` is a one-word fix. I'd add it to every controlled input by default and only remove it when autocomplete is explicitly wanted.
  - date: "April 24–25, 2026"
    sortDate: "2026-04-24"
    title: "UI Polish"
    summary: >-
      Turned the functional prototype into something I'd be comfortable showing.
    sections:
      - label: "What I built / changed"
        text: |-
          Turned the functional prototype into something I'd be comfortable showing. The main thing was the DESEMPENHO section — five sparkline stat cards using Recharts, each showing a running metric with a trend arrow. But the session was really about figuring out how to build a polished UI inside a single-file constraint. No component library, no design system. Just inline SVG icons, Tailwind, and enough discipline to keep things consistent.
      - label: "What I was trying to learn / decide"
        text: |-
          I wanted to know if I could make a UI feel intentional without reaching for a component library. Everything in this project lives in one file by design — `page.tsx` — so I needed to find patterns for reusable elements that didn't require extracting files. Icons were the main test case. The answer was: define them as typed const arrow functions at module level, accept a `className` prop for size and color. Fourteen of them. It works fine.
      - label: "What went wrong"
        text: |-
          The sparkline cards were a mess for longer than I'd like to admit. Five cards in a row need to visually align — same label height, same value height, same chart height — regardless of how long the text is. My first approach was `align-items: stretch`, which I was certain would force uniform heights. It made the cards the same height but the content inside each card still flowed differently depending on the label length. I tried `justify-content: space-between`. I tried `flex-grow` on the label zone. Each one broke things differently but none of them fixed the actual problem.
          
          The fix was to stop fighting flexible layout and use fixed layout. Explicit heights on every zone: a height for the label, a height for the value, `h-4` for the trend text, `h-10 flex-shrink-0` for the sparkline. Every card then has identical internal geometry. It took me too long to reach for it because explicit heights felt like the wrong answer — rigid, not how you're supposed to build responsive UI. But for a fixed-count card grid where visual alignment is the entire point, it's exactly right.
          
          I also hit a `bun add recharts` permission error from Bash on Windows. Same command worked immediately from PowerShell. Just wasted time.
      - label: "Biggest challenge / bug"
        text: |-
          Storing icon component references in a data array to drive trend arrows in the DESEMPENHO cards. I wrote `<card.trendArrow />` and nothing appeared. No error, no warning, just a blank space. The component was in the array — I console.logged it. The conditional around it wasn't gating it out. The CSS wasn't hiding it. It wasn't in the DOM at all.
          
          TypeScript was completely silent. The type was correct: a component function stored on a typed property. The issue is that JSX has a rule outside the type system: a tag must start with a capital letter to be treated as a component, not as an unknown HTML element. `<card.trendArrow />` — lowercase property access — React interprets as a custom HTML element named "card.trendArrow", renders nothing, moves on. The fix is one line: `const TrendArrow = card.trendArrow` and then `<TrendArrow />`. You have to know this rule exists to find the bug. TypeScript won't tell you.
      - label: "What I learned"
        text: |-
          Fixed-height zones are the right solution for card grid alignment. Not flex-grow, not min-height, not space-between. Give every zone an explicit height and the grid holds regardless of content length. This is the kind of thing you only learn by hitting the wall.
          
          Running a code review after building is worth it even when you wrote the code yourself. The reviewer caught two things I'd missed: `handleDiscover` wasn't resetting the selected company between searches (stale detail panel on re-run), and the company website URL was being passed raw to an `<a href>` without sanitizing — a `javascript:` URI from a malformed scrape would have executed. Both obvious in review. Both invisible while building.
      - label: "What I would do differently"
        text: |-
          I'd separate the sparkline data from the display logic from the start. The DESEMPENHO cards mix real computed counts with hardcoded mock trend percentages. Users can't tell which is which. Either label the mock data clearly or don't show it until it's derived from real data. Mixing real and fake in the same component is a quiet trust erosion.
---

## What I built

Oportuno is a product workflow for discovering Portuguese small businesses with weak or missing digital presence. It starts with a search, enriches company data from public sources, classifies the opportunity, extracts contact emails when available, generates a European Portuguese outreach draft with AI, and logs email delivery.

The current MVP covers the full stack: data discovery, enrichment, classification, AI generation, email sending, and database-backed history.

## What I was trying to learn

I wanted to learn what changes when an AI feature becomes part of a product workflow instead of a small demo. That meant thinking about reliability, user feedback, background processing, testability, and how to keep the system understandable as more services appeared.

## What went wrong

The first version tried to do too much synchronously. That worked locally, but it was the wrong shape for production: scraping and enrichment can take long enough that a serverless request may freeze or time out before the work finishes.

The better design is an async pipeline: create a durable job, process it from a worker endpoint, track per-company progress, and let the UI poll for updates.

## Biggest challenge / bug

The biggest architectural challenge was turning the pipeline into something observable and recoverable. A stateless request can fail silently; a job record can be inspected, retried, marked failed, or resumed.

That led to a job model with explicit states, per-company rows, progress counters, stale-job recovery, and a UI that shows what is happening instead of pretending the result is immediate.

## What I learned

I learned that "async" is not only a backend decision. It changes the product experience. Once work moves out of the request path, the interface needs progress, waiting states, and clear recovery behavior.

I also learned that small naming and boundary decisions matter. Moving company persistence into a dedicated service made null-handling explicit and stopped failed enrichment from overwriting useful data. Writing tests around the service layer caught edge cases that were easy to miss by inspection.

## What I would do differently

I would commit in smaller slices during large architecture changes: schema and services first, API routes second, UI polling third. That gives the work a safer history and makes interruptions less costly.

I would also define interfaces before writing persistence code. The interface is where nullable fields, naming, and update behavior become visible instead of being hidden inside an upsert call.
