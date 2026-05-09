---
title: "ProjectDesk"
date: 2026-05-08
summary: "A project workspace for organizing tasks, notes, priorities, and the real development decisions behind a personal productivity tool."
featured: false
slug: "projectdesk"
status: "In progress"
stack:
  - React
  - Vite
  - Supabase
  - PWA
  - Local-first storage
  - Vitest
lesson: "Small product tools become serious when state, offline behavior, mobile layout, and testing all have to work together."
entries:
  - date: "May 8, 2026"
    sortDate: "2026-05-08"
    title: "Making ProjectDesk Feel Less Abrupt"
    summary: >-
      I added a small UX polish layer for motion and loading.
    sections:
      - label: "What I built"
        text: |-
          I added a small UX polish layer for motion and loading. New tasks now enter with a quiet slide/fade, completed and reopened tasks get a brief visual pulse, and the priority dashboard rows use the same completion feedback when they stay visible. I also added a real workspace loading screen, a route-loading spinner, better auth/session feedback, and clearer file upload/opening states.
      - label: "What I was trying to learn"
        text: |-
          I was trying to make ProjectDesk feel more responsive without making it feel animated for animation's sake. The app is a daily-use dashboard, so feedback should confirm actions and reduce uncertainty, not draw attention away from the work.
      - label: "What went wrong"
        text: |-
          The tempting version would have been broader: animate every card, every section, and every counter. That would have been easy to overdo. The useful part was narrower: task creation, task completion, and places where the user is waiting.
      - label: "Biggest challenge / bug"
        text: |-
          The hardest part was keeping the animation state out of the sync layer. Task creation and completion already flow through localStorage and Supabase, and that code is too important to disturb for a visual flourish. The better answer was to track small UI-only flags in the components and let the saved data remain boring and stable.
      - label: "What I learned"
        text: |-
          Loading states and animation are both trust signals. A spinner is not decoration when the app is hydrating a workspace or uploading a file; it tells me the app is alive and that I do not need to click again.
      - label: "What I would do differently"
        text: |-
          For future polish passes, I would keep using real moments of uncertainty as the guide. If the user just did something or is waiting for something, that is where feedback belongs.
  - date: "May 8, 2026"
    sortDate: "2026-05-08"
    title: "Giving ProjectDesk a Real App Icon"
    summary: >-
      I turned the supplied ProjectDesk icon image into proper PWA assets: favicon, Apple touch icon, 192px and 512px app icons, and a padded maskable Android icon.
    sections:
      - label: "What I built"
        text: |-
          I turned the supplied ProjectDesk icon image into proper PWA assets: favicon, Apple touch icon, 192px and 512px app icons, and a padded maskable Android icon. I also wired the manifest and browser title so the installed app reads as ProjectDesk instead of the older Project Ecosystem name.
      - label: "What I was trying to learn"
        text: |-
          I was trying to make the install experience feel like an actual product, not a web shortcut. The icon is a small detail, but on Android it becomes the first visual signal that this is something worth opening every day.
      - label: "What went wrong"
        text: |-
          The environment did not have Python/Pillow available, so the first straightforward resize path was not available. I switched to native Windows image APIs from PowerShell instead.
      - label: "Biggest challenge / bug"
        text: |-
          The main design concern was Android maskable cropping. A normal square icon can look good in the manifest but get cut awkwardly by launcher masks, so I generated a separate maskable version with extra safe-area padding.
      - label: "What I learned"
        text: |-
          PWA polish is partly about providing the right sizes and purposes, not just having a nice source image. The same artwork needs slightly different treatment for browser tabs, Apple touch icons, standard Android icons, and maskable Android launchers.
      - label: "What I would do differently"
        text: |-
          For the next PWA pass, I would pair icon work with screenshots so the install prompt tells a fuller story: recognizable icon first, then a preview of the project dashboard.
  - date: "May 7, 2026"
    sortDate: "2026-05-07"
    title: "Making Many Projects Feel Manageable"
    summary: >-
      I added a small project management layer to the homepage: search, status filtering, and recent activity ordering.
    sections:
      - label: "What I built"
        text: |-
          I added a small project management layer to the homepage: search, status filtering, and recent activity ordering. The project grid still feels like the same ProjectDesk homepage, but it now has a way to stay usable when the number of projects grows.
          
          Pinned projects remain first. Everything else now sorts by recent activity across the project, tasks, subtasks, and files, which should make the page feel closer to what I am actually working on.
      - label: "What I was trying to learn"
        text: |-
          I was trying to solve scale without redesigning the app. The question was not "how do I build a project database?" It was "how do I keep this calm card homepage useful once it has more projects than fit comfortably on one screen?"
          
          The answer was to add just enough control: search when I know what I want, status filtering when I want to narrow the view, and better default ordering when I do not want to think.
      - label: "What went wrong"
        text: |-
          The first instinct could have been pagination, but pagination would make project cards feel more like a table. It solves count, but not necessarily attention. Search and filters match the way I actually look for work better.
          
          I also had to be careful not to hide projects by default. Filtering to Active first would reduce clutter, but it would also change the current homepage behavior and could make projects feel missing after a deploy. Keeping the default as All preserves trust.
      - label: "Biggest challenge / bug"
        text: |-
          The hardest part was defining "recent" correctly. A project can become current because the project itself changed, but also because a task, subtask, or file changed. Sorting only by the project row timestamp would miss that.
          
          So the sort now derives activity from the nested project data while still respecting pinned projects first.
      - label: "What I learned"
        text: |-
          Homepage scale does not always need a big new navigation model. Sometimes the best product move is a quiet toolbar that helps the existing layout keep working.
          
          I also learned that default states matter. A filter can be powerful without being active by default.
      - label: "What I would do differently"
        text: |-
          If this grows further, I would consider adding saved views like "Active", "Pinned", or "Recently touched". But I would wait until real use proves that the single search and status filter are not enough.
  - date: "May 7, 2026"
    sortDate: "2026-05-07"
    title: "Separating Homepage Features With Quiet Space"
    summary: >-
      I added more breathing room between the Projects grid and the Priority Dashboard, plus a subtle divider line above the dashboard.
    sections:
      - label: "What I built"
        text: |-
          I added more breathing room between the Projects grid and the Priority Dashboard, plus a subtle divider line above the dashboard. I also moved New Project out of the global header controls and into the Projects toolbar, leaving the account/workspace menu as the header utility.
          
          This was a small visual change, but it matters because the homepage now has two strong feature areas stacked together and a separate account control. The layout needed to explain those boundaries without making the page heavier.
      - label: "What I was trying to learn"
        text: |-
          I was trying to understand how to separate features without redesigning the page. The Projects section and Priority Dashboard should feel related, but not glued together. The goal was to create a clear pause in the layout without turning either section into a heavy panel.
      - label: "What went wrong"
        text: |-
          The dashboard title was too close to the last row of project cards. It made the dashboard feel like a continuation of the project grid instead of its own tool. At the same time, the New Project button sitting next to the account menu made a project-specific action feel mixed with workspace/account controls.
          
          The page did not need a new component; it needed hierarchy.
      - label: "Biggest challenge / bug"
        text: |-
          The main challenge was restraint. A big card, colored band, or heavy divider would solve separation but would also make the app feel busier. The better first move was whitespace, with a very light divider that can be removed if it feels too visible.
          
          The second challenge was placing New Project where it belongs. It is not a global account action; it is part of managing the project list, so it now sits with search, status filtering, and count.
      - label: "What I learned"
        text: |-
          Spacing is a feature boundary. Good layout can explain structure before the user reads a title.
      - label: "What I would do differently"
        text: |-
          When adding new homepage tools, I would think about section rhythm immediately: title, controls, content, pause. It is easier to preserve calm density when each feature has room to start and end.
  - date: "May 7, 2026"
    sortDate: "2026-05-07"
    title: "Mobile Homepage Density After Real Phone Testing"
    summary: >-
      I tightened the mobile homepage after seeing it on an actual phone screenshot.
    sections:
      - label: "What I built"
        text: |-
          I tightened the mobile homepage after seeing it on an actual phone screenshot. The account menu now reads as a compact account/workspace utility instead of a full email chip, the New Project action becomes a smaller project-toolbar action on mobile, the project count gets quieter, and the project cards keep the two-column layout while using smaller rings, calmer badges, and lighter shadows.
      - label: "What I was trying to learn"
        text: |-
          I was trying to keep the useful density without letting every control compete for attention. The mobile homepage had all the right features, but the hierarchy was too loud: account, search, filter, New Project, cards, and Priority Dashboard were all asking for the same amount of focus.
      - label: "What went wrong"
        text: |-
          The desktop hierarchy did not translate cleanly to phone. The email chip was sensible on desktop, but on mobile it became a large visual object in the Projects section. The New Project button also felt too heavy next to the status filter. The result was a control area that felt more important than the projects themselves.
      - label: "Biggest challenge / bug"
        text: |-
          The hardest part was preserving two-column cards. One-column cards would be easier to read, but they would make the homepage much longer. The better compromise was to keep two columns and reduce internal competition: smaller progress rings, shorter pinned indicator, smaller card padding, and hidden card arrows on mobile.
      - label: "What I learned"
        text: |-
          Mobile density is not only about fitting things on screen. It is about deciding which things deserve visual weight. A control can stay available while becoming quieter.
      - label: "What I would do differently"
        text: |-
          For future mobile changes, I would test the first version on a real phone earlier. Browser responsive mode helps, but the real screenshot made the hierarchy problem obvious immediately.
  - date: "May 7, 2026"
    sortDate: "2026-05-07"
    title: "Making Account Controls Feel Global on Mobile"
    summary: >-
      I changed the mobile account/workspace control from a text chip under the Projects header into a compact gear button on the right side of the header.
    sections:
      - label: "What I built"
        text: |-
          I changed the mobile account/workspace control from a text chip under the Projects header into a compact gear button on the right side of the header. After debating the menu behavior, I moved it away from the centered modal treatment and made it a viewport-safe top-right popover that still feels connected to the gear.
      - label: "What I was trying to learn"
        text: |-
          This was about separating app-level utilities from project-level work. The account menu is not part of the Projects feature, so placing it in the same visual flow as search, filters, and New Project made the hierarchy confusing.
      - label: "What went wrong"
        text: |-
          The earlier mobile account chip still looked like content inside Projects. It also inherited the desktop dropdown behavior, which meant the menu could open toward the side and become hard to read on a narrow screen. The first safe fix used a centered panel, but that felt heavier than the action deserved.
      - label: "Biggest challenge / bug"
        text: |-
          The key challenge was keeping desktop unchanged while making mobile behave differently. Desktop benefits from the visible email chip. Mobile benefits from an icon-sized global utility and a fixed-position menu that is clamped to the viewport but still visually anchored to the button.
      - label: "What I learned"
        text: |-
          Responsive design is not only about size. Sometimes the same control needs a different role expression on phone: text label on desktop, compact utility icon on mobile.
      - label: "What I would do differently"
        text: |-
          For future global actions, I would decide early whether they belong in the page content flow or in a utility area. That distinction matters much more on mobile.
  - date: "May 6, 2026"
    sortDate: "2026-05-06"
    title: "Adding Real Login Boundaries"
    summary: >-
      I started the auth implementation for ProjectDesk instead of treating login as just a UI screen.
    sections:
      - label: "What I built"
        text: |-
          I started the auth implementation for ProjectDesk instead of treating login as just a UI screen. I added Supabase email/password sign-in and sign-up, threaded the authenticated session into the app, scoped Supabase sync by user id, and added an RLS migration for projects, tasks, subtasks, files, and Storage objects.
          
          I also changed new file uploads to live under user-prefixed Storage paths and made the app open uploaded files through signed URLs. That sets up the bucket to become private once I have checked what to do with older pre-auth uploaded objects.
          
          After signing in, I also cleaned up the home screen that auth made awkward. The Data button and Sign out button looked like loose pieces scattered across the header, so I folded them into one account menu, moved New Project into the header, and made Projects the first desktop section just like mobile.
          
          I followed that by adding the first workspace foundation. ProjectDesk now bootstraps a profile and a personal workspace for a signed-in user when the workspace SQL is installed, adds `workspace_id` to synced rows, and keeps workspace-scoped local cache ready for the day I need multiple workspaces or members.
      - label: "What I was trying to learn"
        text: |-
          I was trying to turn a personal no-auth sync app into something with a real security boundary without breaking the offline-first behavior that makes ProjectDesk useful. The important question was where auth should sit: around the whole app, inside the sync adapter, or both.
      - label: "What went wrong"
        text: |-
          The easy path would have been adding a login form and leaving the storage layer mostly alone. That would have looked done, but it would not have protected data. The harder truth was that every remote row needed ownership, every query needed to be scoped, and local cache needed to stop being a single global bucket once users existed.
      - label: "Biggest challenge / bug"
        text: |-
          The hardest part was the migration story. Existing rows and uploaded files were created before users existed, so they do not magically belong to anyone. If I enabled RLS blindly, old rows with no `user_id` would disappear from the API. If I made the Storage bucket private blindly, old files stored under legacy paths could stop opening. The solution was to add a careful migration SQL with a backfill block and keep bucket privacy as a final explicit step after legacy objects are checked.
          
          The first real sign-in also exposed a sync contract issue: RLS policies care about parent ownership, so saving projects, tasks, subtasks, and files in parallel can fail if child rows arrive before their parent rows are visible. The sync code now saves parent-first.
          
          The next hard part was resisting the urge to build full team collaboration. A profile/workspace model can become a rabbit hole: invitations, roles, switching, shared RLS policies, and UI management. For ProjectDesk today, the honest slice is a personal workspace with a migration path, not a team product.
      - label: "What I learned"
        text: |-
          Auth is not a feature sitting beside sync; it changes the sync contract. The app needs to know who owns the local cache, who owns each remote row, and whether a Storage object path can be proved to belong to the current user.
          
          Workspace support has the same lesson at a different level. The app should not wait until it needs teams to start recording where data belongs. Adding `workspace_id` now gives future features a place to attach without forcing a redesign later.
      - label: "What I would do differently"
        text: |-
          If starting over, I would add `user_id` columns and RLS-shaped policies even in the no-auth phase, with a fixed development owner or workspace abstraction. Retrofitting ownership after data exists is much more delicate than designing for it early.
  - date: "May 6, 2026"
    sortDate: "2026-05-06"
    title: "Quality Tooling Baseline"
    summary: >-
      I added the first quality baseline for ProjectDesk: Vitest tests for route helpers and storage transforms, an ESLint setup, a scoped Prettier setup, and a smoke command that...
    sections:
      - label: "What I built"
        text: |-
          I added the first quality baseline for ProjectDesk: Vitest tests for route helpers and storage transforms, an ESLint setup, a scoped Prettier setup, and a smoke command that runs the production build plus direct route helper tests. I also extracted the route helpers from `App.jsx` into `src/lib/routes.js` so the slug, route parsing, and route resolution behavior can be tested without rendering the whole app.
          
          I also reviewed icon-only edit/delete controls and added missing accessible labels on file action buttons and the subtask submit button. That turns the quality work into something more than tooling; it closes a small but real usability gap.
      - label: "What I was trying to learn"
        text: |-
          I was trying to protect the fragile parts of the app without making ProjectDesk feel over-engineered. The route and sync bugs have been the most painful, so the first tests focus there instead of jumping straight into broad component tests.
      - label: "What went wrong"
        text: |-
          Prettier immediately showed that the existing codebase is not formatted in Prettier's default style. Running it across the whole app would have created a huge noisy diff unrelated to quality behavior. I scoped formatting to the new tooling and test files instead, which gives me a starting point without rewriting the whole app's visual history.
      - label: "Biggest challenge / bug"
        text: |-
          The biggest challenge was getting value from tests without disturbing working routing code. Extracting helpers from `App.jsx` could have been risky if I changed behavior while moving them. The tests helped make that move explicit: slug first, generated slug second, id fallback last, and direct `/projects/:project/:tab` paths remain parseable.
      - label: "What I learned"
        text: |-
          Quality setup should start by protecting what has already hurt. For this app, that means route resolution, normalized storage shape, pending sync scope, and stale local versus newer remote merge behavior.
      - label: "What I would do differently"
        text: |-
          I would extract route helpers earlier. Keeping route logic inside `App.jsx` made it harder to test the exact behavior that mattered most after the slug refresh bugs.
  - date: "May 6, 2026"
    sortDate: "2026-05-06"
    title: "Forcing the Installed PWA to Notice the Fix"
    summary: >-
      I bumped the service worker cache version after the mobile task action-sheet fix deployed but still looked unchanged on the phone.
    sections:
      - label: "What I built"
        text: |-
          I bumped the service worker cache version after the mobile task action-sheet fix deployed but still looked unchanged on the phone. Then the screenshot revealed the real issue: I had fixed the task row menu, but the broken menu was the project header overflow menu beside the status badge. I updated the project and file overflow menus to use the same viewport-safe menu pattern as task actions, then centered the panel after real-device feedback showed the bottom placement could still hide the first action. When tapping outside still did not close the menu on Android, I added document-level outside-tap handling so closing no longer depends on the invisible backdrop receiving the event.
      - label: "What I was trying to learn"
        text: |-
          I was trying to separate a failed UI fix from a stale deployment experience. The code on `main` had the new fixed task popover, but the phone behaved like the old issue was still there because a different menu was actually failing.
      - label: "What went wrong"
        text: |-
          The previous push changed React and CSS, and then I bumped `sw.js`, but that still did not address the screenshot. I had misread "inside a task" too narrowly and focused on task cards. The visible clipped menu was in the project header.
      - label: "Biggest challenge / bug"
        text: |-
          The hard part was slowing down enough to identify the exact menu. The project header menu reused the same mobile menu panel class but not the same safe positioning treatment. I fixed the shared mobile overflow behavior so task, project, and file menus open as centered panels instead of being anchored to small buttons near screen edges or fighting the bottom controls. The final lesson was that invisible overlays can be unreliable in a PWA context, so the menu also needs a direct document listener that closes it when the tap target is outside the menu wrapper.
      - label: "What I learned"
        text: |-
          For an installed PWA, a successful Vercel deployment does not guarantee the running app instance has refreshed. But this session also taught the simpler lesson: screenshots beat assumptions. The bug was real, but I had patched the wrong instance of the pattern.
      - label: "What I would do differently"
        text: |-
          For PWA-visible fixes, especially mobile CSS fixes, I would consider bumping the service worker cache version in the same release commit when the installed app needs to be forced onto fresh assets. I would also ask for or inspect a screenshot earlier when there are multiple similar overflow menus in the app.
  - date: "May 6, 2026"
    sortDate: "2026-05-06"
    title: "Turning Supabase Into a Real Boundary"
    summary: >-
      I moved ProjectDesk from a mostly anonymous Supabase sync model into a more serious authenticated setup.
    sections:
      - label: "What I built"
        text: |-
          I moved ProjectDesk from a mostly anonymous Supabase sync model into a more serious authenticated setup. I added login, user-scoped data, RLS policies, profile and workspace tables, a migration path for the old default workspace data, and authenticated Storage policies for uploaded files.
          
          The biggest practical change is that ProjectDesk now has a real owner boundary. Projects, tasks, subtasks, files, profiles, workspaces, and workspace memberships are all tied to authenticated Supabase users instead of depending on a shared anonymous shape.
      - label: "What I was trying to learn"
        text: |-
          I was trying to understand where the app actually lives now. At the beginning, it was easy to mix up local pgAdmin, Prisma, local Postgres, Supabase Auth, and Supabase's hosted database. The important realization was that ProjectDesk's production data is in Supabase, and pgAdmin is only useful if it is connected to the same Supabase Postgres instance.
          
          I also wanted to learn the correct order for hardening a synced app. Auth, RLS, workspace ownership, file policies, and private buckets are connected, but they cannot all be changed casually at the same time without risking locked-out data or broken file links.
      - label: "What went wrong"
        text: |-
          The confusing part was the setup order. We tried to backfill `user_id` before the database had the expected columns, which produced a clear but stressful error: the column did not exist. That exposed that the mental model was still fuzzy. The right order had to be columns first, then backfill existing rows, then enforce RLS policies.
          
          There was also a subtle difference between Storage policies and bucket privacy. Seeing authenticated policies in place can make it feel like the bucket is fully private, but a public bucket still allows public URL access. The policies are necessary, but the bucket privacy switch is a separate final hardening step.
      - label: "Biggest challenge / bug"
        text: |-
          The biggest challenge was not the code. It was sequencing the migration without losing trust in the data. Existing rows needed to be claimed by the real user id, workspace rows needed to exist before app code depended on them, and files needed to keep opening while Storage policies changed.
          
          The storage question was especially delicate because old uploaded objects may use legacy paths, while new auth-aware uploads use user-prefixed paths and signed URLs. Making the bucket private is the right long-term security move, but doing it before checking old file objects could make older files stop opening. The final decision was to switch policies first, test upload/open/refresh behavior, and only then make the bucket private.
      - label: "What I learned"
        text: |-
          RLS is not just a SQL feature. It changes the whole app contract. Inserts need the right `user_id`; workspace-aware queries need the right `workspace_id`; file uploads need paths that policies can reason about; and remote sync must never accidentally cross user boundaries.
          
          I also learned that Supabase's dashboard can make separate concepts look like one thing. Auth users, Postgres rows, RLS policies, Storage object policies, and bucket privacy all live near each other, but each one answers a different security question.
      - label: "What I would do differently"
        text: |-
          Next time I would write the migration checklist before running any SQL: create columns, backfill owner fields, verify null counts, add or confirm RLS policies, verify app login, test Storage upload/open, then consider private bucket mode.
          
          I would also name the Supabase phase more explicitly in the roadmap. "Add auth" was too broad. What actually happened was a sequence: identity, ownership, workspace foundation, row security, Storage policy hardening, and finally private file access.
  - date: "May 6, 2026"
    sortDate: "2026-05-06"
    title: "Finishing the File Privacy Loop"
    summary: >-
      I added the final file hardening pieces: a SQL migration to make the project-files bucket private, and app logic that removes the physical Supabase Storage object after a file...
    sections:
      - label: "What I built"
        text: |-
          I added the final file hardening pieces: a SQL migration to make the `project-files` bucket private, and app logic that removes the physical Supabase Storage object after a file metadata soft-delete has synced successfully.
          
          This means ProjectDesk keeps the safer sync behavior first. The database row is still soft-deleted so devices can converge, but the actual uploaded object is cleaned up after the remote metadata save has worked.
      - label: "What I was trying to learn"
        text: |-
          I was trying to turn the earlier Supabase debate into a careful implementation. The important distinction was between hiding a file in the app, deleting its metadata, deleting the physical stored object, and making the bucket private. Those are four different steps, and doing them in the wrong order could break file access or make sync harder to reason about.
      - label: "What went wrong"
        text: |-
          The older TODO made physical cleanup sound like a future problem because metadata soft-delete needed to be stable first. Once auth, RLS, signed URLs, and user-prefixed paths were working, the TODO was ready to become code.
          
          The risk was making cleanup too aggressive. If object deletion happened before metadata sync, an offline or failed save could leave a file hidden locally but still unresolved remotely. So I kept cleanup after successful `saveState`.
      - label: "Biggest challenge / bug"
        text: |-
          The trickiest part was keeping failure behavior calm. Physical Storage deletion can fail for old legacy paths or policy reasons, especially around pre-auth uploads. That should not undo the metadata delete. The app now logs cleanup failures as warnings while preserving the soft-delete.
      - label: "What I learned"
        text: |-
          For synced file systems, metadata is the source of truth for app behavior, and object cleanup is a follow-up maintenance action. That order keeps the UI and cross-device sync predictable.
      - label: "What I would do differently"
        text: |-
          In future file features, I would design the object lifecycle from the start: upload object, create metadata, soft-delete metadata, then remove object after a successful sync boundary. Writing that contract early would make later privacy hardening less stressful.
  - date: "May 6, 2026"
    sortDate: "2026-05-06"
    title: "Choosing Practical Tests Before Bigger Testing"
    summary: >-
      I added a first real quality layer to ProjectDesk: focused tests for route helpers and storage transforms, a lint command, a format check, and a smoke command that builds the...
    sections:
      - label: "What I built"
        text: |-
          I added a first real quality layer to ProjectDesk: focused tests for route helpers and storage transforms, a lint command, a format check, and a smoke command that builds the app and verifies direct project-route behavior. The goal was not to pretend the app now has exhaustive coverage. The goal was to protect the specific places that had already caused pain.
          
          The quality checklist also became documented in the project workflow, so testing is no longer just something I remember to do. It is part of how the app gets changed and pushed.
      - label: "What I was trying to learn"
        text: |-
          I was trying to decide how much testing is enough for a personal app that is becoming important for daily use. ProjectDesk does not need a huge enterprise testing system yet, but it also cannot keep relying only on manual clicking, especially after the routing and sync bugs.
          
          The testing debate was really about risk. The riskiest parts were not generic React rendering. They were route resolution, slug fallback, refresh behavior, and storage normalization. Those are the places where one quiet mistake can make the app feel broken or unsafe.
      - label: "What went wrong"
        text: |-
          Before these tests, the app had good manual verification but weak automated memory. We had already seen bugs where the visible symptom looked like a UI problem, but the real cause was routing or storage mapping. Without small tests, those lessons could easily fade and the same class of bug could return.
          
          There was also a temptation to jump straight into broad component testing or end-to-end testing. That would be useful later, but it would have been too much setup too early. The better move was a small test harness that catches high-value mistakes immediately.
      - label: "Biggest challenge / bug"
        text: |-
          The hardest part was choosing the boundary of the first tests. Storage and routing are both connected to the whole app, so it would be easy to overbuild. I kept the tests close to pure helpers and transform behavior because those are fast, stable, and easy to trust.
          
          The direct route smoke test mattered because refreshing project pages had repeatedly been misleading. It proved that the important route helpers still understand slugs, generated slugs, duplicate slugs, id fallbacks, and invalid routes.
      - label: "What I learned"
        text: |-
          Good early tests should encode scars. The best first tests are not random coverage numbers; they are little alarms placed exactly where previous bugs taught me the system is fragile.
          
          I also learned that a quality workflow feels much calmer when it has layers: `test:run` for behavior, `lint` for code issues, `format:check` for consistency, `smoke` for deploy confidence, and `audit --omit=dev` for production dependency risk.
      - label: "What I would do differently"
        text: |-
          I would add helper tests earlier, especially around data transforms and route resolution. They are cheap and they make later feature work less tense.
          
          For the next quality step, I would avoid chasing coverage for its own sake. I would add tests when a new feature touches sync, routing, auth, or persistence, and I would add browser-level smoke tests only when they protect a real user flow that unit tests cannot see.
  - date: "May 5, 2026"
    sortDate: "2026-05-05"
    title: "Mobile Task Menu Visibility"
    summary: >-
      I fixed the mobile task overflow menu so task actions open in a fixed mobile popover instead of being positioned inside the task row.
    sections:
      - label: "What I built"
        text: |-
          I fixed the mobile task overflow menu so task actions open in a fixed mobile popover instead of being positioned inside the task row. The change is mobile-only, because the desktop task actions are still working well and should stay close to the original design.
      - label: "What I was trying to learn"
        text: |-
          I was trying to turn real phone feedback into a practical polish fix. The issue was not that the menu existed, but that the task row was too fragile a place to anchor it on a narrow screen.
      - label: "What went wrong"
        text: |-
          The first fix was too timid. I moved the menu from opening sideways to opening below the three-dot button, but it was still an absolutely positioned child of a dense task row inside a scrollable mobile layout. On an actual phone, it could still be partly hidden.
      - label: "Biggest challenge / bug"
        text: |-
          The challenge was admitting that the anchor model was the problem, not just the direction. I fixed it by rendering a tap-outside backdrop and making the task menu a fixed bottom popover on mobile. That keeps the menu visible without touching storage, routing, sync, or desktop actions.
      - label: "What I learned"
        text: |-
          Mobile menus in scrollable task lists should not depend on the row having enough spare space. A fixed popover is less clever, but it is much more reliable on real phones.
      - label: "What I would do differently"
        text: |-
          I would test overflow menus on a narrow real-device viewport immediately after adding them, especially when the menu is positioned inside a scrollable card or row.
  - date: "May 2, 2026"
    sortDate: "2026-05-02"
    title: "Data Safety and Backup Tools"
    summary: >-
      I added a data-safety slice: safer delete confirmations for tasks, subtasks, and files, project pinning, and a small Data modal for JSON export/import backups.
    sections:
      - label: "What I built"
        text: |-
          I added a data-safety slice: safer delete confirmations for tasks, subtasks, and files, project pinning, and a small Data modal for JSON export/import backups. The Data button stays quiet on the home screen, but it gives me a way to download a backup or restore from one when I need it.
          
          Project pinning adds a simple organization layer without going straight into drag-and-drop. Pinned projects move to the top, which solves the daily problem of reaching important projects quickly, especially on mobile.
      - label: "What I was trying to learn"
        text: |-
          I was trying to improve trust before adding more ambitious features. Search, tags, and due dates are useful, but backups and safer deletes protect the data I already have. This felt like the right next step because ProjectDesk is now syncing across devices and holding real work.
      - label: "What went wrong"
        text: |-
          The easy version of import would have simply replaced local state. That would look correct for a moment, but old Supabase rows could come back during the next remote refresh. Import is not just a localStorage operation anymore; it has to think about remote convergence too.
      - label: "Biggest challenge / bug"
        text: |-
          The biggest challenge was import semantics. If a backup is meant to replace current data, then records missing from the backup need to become soft-deleted sync events, not just disappear locally. Otherwise Supabase can resurrect them later. I handled this by normalizing the imported backup, marking imported records as pending sync, and creating tombstones for current records that are not present in the backup.
      - label: "What I learned"
        text: |-
          Once sync exists, every data tool needs to think in events, not only snapshots. Export is a snapshot. Import is a mutation that must tell the sync layer what changed, including deletions.
          
          I also learned that pinning is a better first step than full project ordering. It solves the main UX need with much less complexity.
      - label: "What I would do differently"
        text: |-
          I would add backup/export earlier in any personal data app. It lowers the fear of testing bigger features. I would also define import behavior explicitly from the start: replace first, merge later only if there is a clear use case.
  - date: "May 2, 2026"
    sortDate: "2026-05-02"
    title: "Hardening Sync Instead of Trusting the Happy Path"
    summary: >-
      I tightened the sync layer so ProjectDesk behaves better when devices are temporarily stale, offline, or recovering from a failed Supabase save.
    sections:
      - label: "What I built"
        text: |-
          I tightened the sync layer so ProjectDesk behaves better when devices are temporarily stale, offline, or recovering from a failed Supabase save. The app now writes user changes to localStorage before attempting network sync, retries pending local changes after a successful remote refresh, and keeps Supabase from being overwritten by stale local rows.
          
          I also made normalization more defensive. Duplicate local records are collapsed by item id using the newest event timestamp, and rows sent to Supabase always have safe timestamp values. This is not flashy UI work, but it makes the app feel less fragile.
      - label: "What I was trying to learn"
        text: |-
          I was trying to turn the sync system from "works when everything is online and ordered" into something closer to a daily-use system. Desktop and Android will not always refresh at the same moment, and a phone can be offline, backgrounded, or holding older localStorage. The sync layer has to expect that.
      - label: "What went wrong"
        text: |-
          The uncomfortable discovery was that localStorage was not always acting as the fallback I thought it was. In the previous flow, `persist()` updated React state, but if the app was offline it returned before calling the storage adapter. That meant an offline edit could look real on screen while not being safely cached.
          
          That is exactly the kind of bug that creates false confidence: the UI changes, but the fallback layer does not actually have the edit yet.
      - label: "Biggest challenge / bug"
        text: |-
          The hardest part was preserving the rule that Supabase should be authoritative after a successful remote load without throwing away legitimate offline edits. The solution is the `sync_pending` marker. Local-only or failed-sync items can survive hydration only when they explicitly say they are pending. Everything else can be treated as stale local ghost state.
          
          The retry path now respects that distinction. After remote refresh, if pending items still exist, ProjectDesk tries to sync only those pending rows. If Supabase has something newer, the local row is skipped instead of resurrecting stale data.
      - label: "What I learned"
        text: |-
          Offline-first is not just "save to localStorage." The order matters. The app needs to cache locally first, sync remotely second, and reconcile carefully later. A stale client should not be allowed to rewrite the world just because it woke up late.
      - label: "What I would do differently"
        text: |-
          I would design the storage adapter around explicit mutation events earlier. Full-state persistence helped bootstrap the app quickly, but cross-device sync becomes easier to reason about when each change has a clear scope, timestamp, and pending state from the beginning.
  - date: "May 2, 2026"
    sortDate: "2026-05-02"
    title: "When Sync Worked but Still Looked Broken"
    summary: >-
      I fixed a sync feedback bug where a task could successfully save to Supabase, appear on mobile, and still leave the desktop UI saying "Sync failed." The data path was working,...
    sections:
      - label: "What I built"
        text: |-
          I fixed a sync feedback bug where a task could successfully save to Supabase, appear on mobile, and still leave the desktop UI saying "Sync failed." The data path was working, but the status path was mixing two different operations: the actual save and the follow-up background refresh.
          
          Now a successful save marks the app as synced immediately. The post-save remote refresh still runs, but it is quiet. If that background refresh has a temporary issue, it logs the problem without turning a successful save into a scary failure message.
      - label: "What I was trying to learn"
        text: |-
          This was about trust. A sync indicator is only useful if it tells the right truth. If the user can see the task on mobile, the app should not keep shouting failure unless the actual save failed.
      - label: "What went wrong"
        text: |-
          The previous flow chained save and refresh together too tightly. `saveState()` could succeed, but `refreshFromRemote()` could fail afterwards and overwrite the visible status. That made the UI report the whole operation as failed even though the important part had already succeeded.
      - label: "Biggest challenge / bug"
        text: |-
          The confusing part was that the symptom sounded like a sync failure, but the evidence contradicted it. The task showing on mobile proved Supabase had the new row. That shifted the investigation from persistence to status semantics.
      - label: "What I learned"
        text: |-
          Not every background error should become a user-facing failure. Saving, refreshing, and reconciling are related, but they are not the same user promise. The status UI should reflect the promise the user cares about most in that moment.
      - label: "What I would do differently"
        text: |-
          I would model sync status as separate channels earlier: save status, refresh status, and offline status. A single string is simple, but it can blur important distinctions once the sync system gets more capable.
  - date: "May 2, 2026"
    sortDate: "2026-05-02"
    title: "Fresh Remote Data Was Being Held Hostage"
    summary: >-
      I changed remote refresh so incoming Supabase data is applied to the UI and cached locally before the app tries to retry pending local edits.
    sections:
      - label: "What I built"
        text: |-
          I changed remote refresh so incoming Supabase data is applied to the UI and cached locally before the app tries to retry pending local edits. This matters because one bad pending local row should not prevent a device from seeing fresh changes made elsewhere.
      - label: "What I was trying to learn"
        text: |-
          The goal was to separate two different jobs: reading the truth from Supabase, and retrying local work that previously failed. They belong in the same refresh loop, but they should not have the same failure boundary.
      - label: "What went wrong"
        text: |-
          The previous sync hardening was too strict in the wrong place. It merged remote state, then tried to push pending local rows, and only after that did it apply the merged state to React. If retrying pending rows failed, the whole refresh failed. That explains why a task could exist in Supabase but only appear on another device after a manual refresh or later reload.
      - label: "Biggest challenge / bug"
        text: |-
          The hard part was realizing that "sync failed" did not necessarily mean "remote load failed." It could mean "remote loaded, but pending retry failed before the UI was allowed to update." That is a subtle but important difference.
      - label: "What I learned"
        text: |-
          Fresh remote data should not be held hostage by cleanup or retry work. Apply what the server knows first. Then retry pending local changes as a second step.
      - label: "What I would do differently"
        text: |-
          I would split refresh into named phases in code: fetch remote, apply remote, retry pending, confirm. That would make these failure boundaries clearer and easier to test.
  - date: "May 2, 2026"
    sortDate: "2026-05-02"
    title: "Cleaning Up Mobile Actions"
    summary: >-
      I moved secondary actions on mobile into compact overflow menus.
    sections:
      - label: "What I built"
        text: |-
          I moved secondary actions on mobile into compact overflow menus. Task cards now keep the main interaction simple: tap the card to open details, tap the checkbox to complete, and use the small menu for add subtask, edit, show/hide details, or delete. File cards use the same pattern for rename, open, and delete. Project detail also gets a mobile actions menu for edit and delete.
          
          Desktop keeps the existing direct buttons, because the extra space makes them useful there instead of cluttered.
      - label: "What I was trying to learn"
        text: |-
          This was about respecting the difference between desktop and phone UX. On desktop, visible controls are efficient. On mobile, too many visible controls make every row feel noisy and increase accidental taps.
      - label: "What went wrong"
        text: |-
          The earlier mobile layout still inherited too much of the desktop control surface. It was responsive in size, but not fully responsive in interaction priority. The UI needed fewer permanent controls, not just smaller controls.
      - label: "Biggest challenge / bug"
        text: |-
          The hard part was deciding what should remain primary. The answer is: content and completion. Opening the task and checking it off are frequent enough to stay visible. Editing, deleting, expanding, and adding subtasks can be one tap deeper.
      - label: "What I learned"
        text: |-
          Mobile polish is often about removing competition. A single overflow menu can make the whole card feel calmer without removing power.
      - label: "What I would do differently"
        text: |-
          I would define mobile action hierarchy earlier in the design: primary tap, quick toggle, secondary menu. That pattern now feels like a good rule for the rest of ProjectDesk.
  - date: "May 2, 2026"
    sortDate: "2026-05-02"
    title: "The Menu Worked, Then Sync Undid It"
    summary: >-
      I fixed the mobile task menu actions where "Add Subtask" or "Show Details" would open the task briefly and then collapse again.
    sections:
      - label: "What I built"
        text: |-
          I fixed the mobile task menu actions where "Add Subtask" or "Show Details" would open the task briefly and then collapse again. The actual interaction was working, but the next sync refresh was pulling `expanded: false` back from Supabase.
      - label: "What I was trying to learn"
        text: |-
          This was another reminder that UI state and synced state need a clear contract. If a field is part of the data model and remote hydration reads it, then saving has to write it too.
      - label: "What went wrong"
        text: |-
          The `expanded` field existed in the database schema and was read during remote load, but it was missing from the Supabase task upsert payload. So local state changed to expanded, then the remote row came back without that update and collapsed the card.
      - label: "Biggest challenge / bug"
        text: |-
          The tricky part was that it looked like a menu/UI bug because it happened right after tapping the menu. The real cause was the storage mapper silently omitting one field.
      - label: "What I learned"
        text: |-
          For synced apps, every field needs to be checked in both directions: normalize/load and save/upsert. One missing save field can make the UI feel haunted even when the component code is fine.
      - label: "What I would do differently"
        text: |-
          I would add small storage transform tests sooner. A simple test that checks task fields round-trip through the Supabase payload would have caught this immediately.
  - date: "May 2, 2026"
    sortDate: "2026-05-02"
    title: "Making Task Details Feel Complete"
    summary: >-
      I added subtask creation directly inside the Task Details modal.
    sections:
      - label: "What I built"
        text: |-
          I added subtask creation directly inside the Task Details modal. Before this, task details could show subtasks, but adding one required going back to the task row action. That made the detail view feel incomplete.
          
          Now the modal has a small add-subtask row below the existing subtasks. The new subtask is sent through the same persistence path as the task-list flow, and it appears immediately in the modal after adding.
      - label: "What I was trying to learn"
        text: |-
          This was about matching user expectation. If I open "Task Details", I expect to manage the important parts of the task there, not just read them.
      - label: "What went wrong"
        text: |-
          The original modal was built as a task editor first and a task management surface second. Once descriptions and subtasks became more important, that split started to feel awkward.
      - label: "Biggest challenge / bug"
        text: |-
          The subtle challenge is that the modal receives the task object from the moment it was opened. If I add a subtask while the modal is open, the parent state updates, but the modal can still be holding the old task snapshot. I handled that by keeping a small local subtask list in the modal so the added item appears immediately.
      - label: "What I learned"
        text: |-
          Modal state needs special care in React. If a modal is opened from a snapshot of data, the user still expects it to behave live while they are inside it.
      - label: "What I would do differently"
        text: |-
          Later, I would consider making Task Details resolve the current task from app state by id instead of holding a snapshot. That would make edits, subtasks, and sync updates feel even more consistent.
  - date: "May 2, 2026"
    sortDate: "2026-05-02"
    title: "Choosing Less Process on Purpose"
    summary: >-
      I added a small reference to AIAssist in ProjectDesk's AGENTS.md. The important decision was not to copy the full Oportuno CLAUDE.md workflow into this project.
    sections:
      - label: "What I built"
        text: |-
          I added a small reference to AIAssist in ProjectDesk's `AGENTS.md`. The important decision was not to copy the full Oportuno `CLAUDE.md` workflow into this project.
          
          ProjectDesk now knows where AIAssist lives and when to use workflows like `/development`, `/bugfix`, and `/test-case-design`, but those workflows are selective tools rather than mandatory ceremony for every small change.
      - label: "What I was trying to learn"
        text: |-
          I was trying to decide whether more process would make this project safer or just heavier. Oportuno has a broader product surface, more compliance concerns, and a more formal AI dev team workflow. ProjectDesk is still a fast-moving personal app where too much orchestration could slow down useful iteration.
      - label: "What went wrong"
        text: |-
          The tempting mistake would have been copying the entire Oportuno instruction file because it looks mature. But maturity in one project can become friction in another. The rules need to fit the project, not just look impressive.
      - label: "Biggest challenge / bug"
        text: |-
          The hard part was separating useful structure from performative structure. AIAssist is useful when a feature is large, ambiguous, or risky. It is not useful if every tiny UI fix has to go through a five-phase workflow.
      - label: "What I learned"
        text: |-
          Good project instructions should reduce decision fatigue, not add new hoops. ProjectDesk already has strong enough guardrails: branches, builds, changelog, journal, and testing gates. AIAssist should augment that when the work is big enough.
      - label: "What I would do differently"
        text: |-
          I would keep this pattern for future projects: reference shared tooling, explain when to use it, and avoid importing another project's full operating model unless the context truly matches.
  - date: "May 1, 2026"
    sortDate: "2026-05-01"
    title: "Navigation Needs Different Desktop and Mobile Answers"
    summary: >-
      I started the navigation resilience pass: duplicate project slugs are normalized, invalid project routes now show a real not-found screen instead of silently sending me home,...
    sections:
      - label: "What I built"
        text: |-
          I started the navigation resilience pass: duplicate project slugs are normalized, invalid project routes now show a real not-found screen instead of silently sending me home, and project detail pages have breadcrumbs on desktop.
          
          The important detail is that breadcrumbs are desktop-only. On mobile, the project detail header already has the back button, project name, and compact tabs. Adding breadcrumbs there would add noise instead of clarity.
      - label: "What I was trying to learn"
        text: |-
          I was trying to make navigation more trustworthy without making the interface heavier. Desktop has room for context trails like `Projects / Project / Tasks`. Mobile needs simpler touch-first navigation, so the same feature should not automatically appear in the same way on both screen sizes.
      - label: "What went wrong"
        text: |-
          The tempting mistake would be to say "add breadcrumbs" and put them everywhere. That would technically satisfy the feature, but it would ignore the phone UX work I have been doing. Mobile and desktop share the same data and routes, but they should not always share the same visual solution.
      - label: "Biggest challenge / bug"
        text: |-
          The hard part was separating navigation correctness from navigation presentation. The not-found state and duplicate slug handling are app-wide correctness improvements. Breadcrumbs are presentation, and they only make sense where there is enough space.
      - label: "What I learned"
        text: |-
          ProjectDesk needs a standing rule: desktop and mobile are related experiences, not identical screens. Desktop can afford more explicit context. Mobile should prioritize reachability, low clutter, and tap clarity.
      - label: "What I would do differently"
        text: |-
          For future features, I would decide the desktop and mobile behavior separately before implementation. The question should be: "What is the right interaction for this device?" not "How do I squeeze the desktop feature onto mobile?"
  - date: "May 1, 2026"
    sortDate: "2026-05-01"
    title: "Testing Before Commit Rule"
    summary: >-
      I added a new project workflow rule: before committing implementation changes, the assistant should stop and ask me to test the work first.
    sections:
      - label: "What I built"
        text: |-
          I added a new project workflow rule: before committing implementation changes, the assistant should stop and ask me to test the work first. The assistant must also explain what changed, where to test it, what flows matter, and what should feel different from before.
          
          This rule belongs in `AGENTS.md` because it changes how future sessions should work, not just how this one session happened.
      - label: "What I was trying to learn"
        text: |-
          I was trying to make the workflow match how I actually use the app. ProjectDesk is visual, mobile, sync-heavy, and deployed to Vercel. A build passing is necessary, but it does not prove the UX feels right on my phone or that a change behaves the way I expect in real use.
      - label: "What went wrong"
        text: |-
          The mobile density work was ready to commit from a code perspective, but I had not tested it yet. That created a bad workflow pressure: the assistant was ready to package the change before I had a chance to feel it in the app.
      - label: "Biggest challenge / bug"
        text: |-
          The challenge is that implementation confidence and product confidence are different. The build can pass while the UI still feels too large, too cramped, or awkward on mobile. The rule protects against committing something that is technically correct but experientially wrong.
      - label: "What I learned"
        text: |-
          For this project, the user test is part of the development loop, not a courtesy after the fact. Especially for mobile UX, the right sequence is: implement, build, explain how to test, wait for feedback, then commit.
      - label: "What I would do differently"
        text: |-
          I would have made this rule explicit earlier. It fits the way this project is evolving: small visual and interaction changes should be tried in the real app before they become part of git history.
  - date: "May 1, 2026"
    sortDate: "2026-05-01"
    title: "Mobile Home Screen Priorities"
    summary: >-
      I made another mobile-only UX pass focused on the home screen.
    sections:
      - label: "What I built"
        text: |-
          I made another mobile-only UX pass focused on the home screen. On desktop, the structure stays the same, but on mobile the Projects section now comes before the Priority Dashboard because projects are the main thing I need to reach quickly on a phone. I also made project cards smaller on mobile so more of them fit on screen, tightened the project detail tabs, replaced the always-visible dashboard filters with a filter sheet, and compressed the big priority summary cards into small counters.
          
          This was not a redesign. It was a correction of mobile priority. The app already worked on a phone, but it still carried some desktop assumptions: big dashboard cards at the top, filters taking permanent space, and project cards that looked nice but used too much vertical room.
          
          After testing, I realized the first pass was still too generous with space. The project cards were technically smaller, but they still felt oversized on a phone. The filter sheet also felt too heavy, like the controls had become cards instead of simple inputs, and task rows still used too much vertical room. I tightened the mobile layout again: project cards now use a denser two-column layout, the filter button has a clearer modern icon treatment, the filter sheet fields are more natural, and mobile task rows are shorter.
          
          The next round was even more specific: the Priority Dashboard list still felt too bulky because the mobile rows were carrying full text badges like "Medium" and "Active". I changed the mobile version to keep the project chip but represent priority and status as small colored dots. That gives me the scan signal without forcing every row to become a mini table.
      - label: "What I was trying to learn"
        text: |-
          I was trying to learn how to make the mobile experience feel intentional without damaging the desktop version that already works. The important question was not "can this fit on a phone?" but "what should be easiest to reach on a phone?" That changed the answer. Projects are navigation, so they belong higher. Dashboard stats are useful, but on mobile they should summarize instead of dominate.
      - label: "What went wrong"
        text: |-
          The first instinct with responsive design is often to stack everything and call it mobile-friendly. That is not enough here. Stacking desktop sections in the same order meant I still had to scroll through dashboard content before reaching the projects. The layout was technically responsive, but the interaction priority was wrong.
          
          There was also a small documentation trap. I almost marked the whole mobile UX phase as done because several foundation pieces were already built, but the filter collapse and polish work were still open. Catching that helped keep the roadmap honest.
          
          The second trap was thinking "smaller" was enough. On a real phone, slightly smaller cards still felt like desktop cards wearing a mobile costume. The stronger fix was density: less padding, smaller completion rings, two project columns where possible, and shorter task rows.
          
          The third trap was keeping too many words in the mobile dashboard rows. The meaning was clear, but the visual cost was too high. On mobile, compact symbols can be better than labels when the pattern is repeated and easy to learn.
      - label: "Biggest challenge / bug"
        text: |-
          The challenge was preserving the desktop layout while changing the mobile reading order. I solved it by adding a projects wrapper and using mobile-only ordering in CSS instead of restructuring the whole app around the phone layout. That keeps desktop stable while letting the phone version behave more like a task app.
          
          The filter change followed the same principle. Desktop keeps the visible selects because they work well there. Mobile gets one compact Filters button and a bottom sheet so the controls are available without permanently occupying the screen.
      - label: "What I learned"
        text: |-
          Mobile polish is not just smaller spacing. It is about deciding what deserves the first screen. In this app, projects are the most important tap target, so they should appear first. Priority stats still matter, but they can become compact counters on mobile.
          
          I also learned to be more precise with TODO status. "Mobile UX foundation is done" is different from "mobile UX is finished." The former is true; the latter would be dishonest.
          
          I also learned that a mobile UI needs to be judged in hand, not only in code. The earlier layout was reasonable on paper, but the actual feeling of scrolling and reaching controls showed where it was still too bulky.
          
          I also learned that repeated metadata should become visual language. A red/orange/green priority dot and a green/gray status dot are enough for scanning once the pattern is familiar, while the task title stays readable.
      - label: "What I would do differently"
        text: |-
          I would have designed the mobile home order earlier instead of inheriting the desktop section order. The desktop dashboard-first flow makes sense for scanning work. The mobile flow should be navigation-first: projects, then dashboard, then deeper filtering and review.
          
          Next time, I would start mobile planning by asking what the user needs to touch first, not what the desktop screen shows first.
  - date: "May 1, 2026"
    sortDate: "2026-05-01"
    title: "The File Upload Was Still Lying"
    summary: >-
      After testing, uploaded files still disappeared after a few seconds.
    sections:
      - label: "What I changed"
        text: |-
          After testing, uploaded files still disappeared after a few seconds. That was the signal that the file card was being shown before the system could prove the file metadata had actually survived remote sync.
          
          I tightened the upload flow so a file is only added to the UI after both pieces succeed: the bytes upload to Supabase Storage, and the metadata row upserts into the `files` table. I also changed remote refresh so a file metadata load error is treated as a real sync error, not as an empty file list.
      - label: "What I was trying to learn"
        text: |-
          I was trying to make the UI stop presenting hopeful state as durable state. For uploads, "the bytes reached Storage" is not enough. The app needs the metadata row too, because that is what lets the file appear after refresh and on another device.
      - label: "What went wrong"
        text: |-
          The first version still relied on the general app save flow to persist file metadata after the Storage upload. That left a small but important gap: the file could appear locally, then remote refresh could come back without the metadata and make it vanish.
      - label: "Biggest challenge / bug"
        text: |-
          The tricky part was that disappearing after a few seconds looked like a UI bug, but it was really a sync contract bug. A later refresh was correcting the UI back to what Supabase knew. Supabase did not reliably know about the file yet.
      - label: "What I learned"
        text: |-
          For file uploads, the commit point should be explicit. Only show success after object storage and metadata persistence both succeed. Anything earlier is just optimism.
  - date: "April 30, 2026"
    sortDate: "2026-04-30"
    title: "Mobile Project Detail Slice"
    summary: >-
      I started Mobile UX Phase 2 with a focused first slice: project detail and task interaction.
    sections:
      - label: "What I built"
        text: |-
          I started Mobile UX Phase 2 with a focused first slice: project detail and task interaction. The mobile header now stacks into a clearer structure, tabs become full-width tap targets, task cards show a simpler mobile hierarchy, and Add Task moves to a floating button on phones.
          
          I also adjusted mobile modals so they feel closer to an app screen than a desktop dialog squeezed into a phone. Inputs are larger, actions stack full-width, and the sync indicator is moved up so it does not fight the Add Task button.
      - label: "What I was trying to learn"
        text: |-
          The goal was to improve mobile without disturbing the desktop version that already feels good. This was a breakpoint-first change: desktop remains the reference design, mobile gets its own ergonomics where the compressed desktop pattern was getting in the way.
      - label: "What went wrong"
        text: |-
          The Add Task affordance took a few iterations. Copying the New Project card pattern was too large for a task list, then a row-sized version still needed visual tuning. The plus icon also exposed a tiny but annoying typography issue: a text plus does not sit optically centered inside a circle.
      - label: "Biggest challenge / bug"
        text: |-
          The main design challenge was deciding what belongs in mobile primary interaction. A task card should be tappable as the main action, while edit/delete/subtask controls should stay secondary. The UI needs to feel fast without hiding important controls too aggressively.
      - label: "What I learned"
        text: |-
          Mobile UX is not just smaller desktop CSS. It needs a different interaction priority: tap the card, see the essentials, keep actions reachable, and avoid permanent bottom UI collisions. The sync indicator and FAB already showed why bottom spacing matters early.
      - label: "What I would do differently"
        text: |-
          I would define mobile interaction rules before styling: what is primary, what is secondary, and what should move into detail views. That would make each CSS decision less reactive.
  - date: "April 30, 2026"
    sortDate: "2026-04-30"
    title: "Making Sync Feedback Quieter"
    summary: >-
      I changed the sync indicator so successful sync messages behave like a short toast instead of a permanent companion.
    sections:
      - label: "What I changed"
        text: |-
          I changed the sync indicator so successful sync messages behave like a short toast instead of a permanent companion. "Synced just now" now appears briefly after a successful save or refresh, then gets out of the way.
          
          Offline, error, and active syncing states still stay visible because they affect trust. Success should reassure and disappear; problems should remain discoverable.
      - label: "What I was trying to learn"
        text: |-
          This was about the difference between confidence and noise. A sync indicator is useful when it tells me something changed or something is wrong. It becomes distracting when it follows every interaction after the app is already fine.
      - label: "What went wrong"
        text: |-
          The previous implementation treated `lastSyncedAt` as a reason to keep the status visible forever. That made sense technically, but not experientially. Once a successful sync is old news, the UI should return to quiet.
      - label: "What I learned"
        text: |-
          Not all states deserve the same visibility. Success can be temporary. Error and offline should be persistent. Syncing should be visible while work is happening.
  - date: "April 30, 2026"
    sortDate: "2026-04-30"
    title: "Making PWA Updates Predictable"
    summary: >-
      I started fixing the awkward PWA update behavior where Vercel had a new deployment, but desktop or Android could keep showing old code after several refreshes.
    sections:
      - label: "What I changed"
        text: |-
          I started fixing the awkward PWA update behavior where Vercel had a new deployment, but desktop or Android could keep showing old code after several refreshes. The service worker was too aggressive: it cached the app shell and served cached responses before trying the network.
          
          The new direction is controlled updates. Navigation requests now prefer the network, hashed assets can still be cached, and a new service worker waits until the app shows "Update available" and the user chooses Reload.
      - label: "What I was trying to learn"
        text: |-
          This was about making ProjectDesk trustworthy as an installed app. A PWA should feel fast and resilient, but not stale. Offline support loses value if I cannot tell whether I am using the current deployment.
      - label: "What went wrong"
        text: |-
          The earlier service worker used a simple cache-first pattern and called `skipWaiting()` automatically. That is fine for a first PWA pass, but it becomes frustrating during real testing because old `index.html` and app-shell responses can linger.
      - label: "Biggest challenge / bug"
        text: |-
          The tricky part is balancing freshness with control. I do not want surprise reloads while writing a task, but I also do not want to refresh five times and hope the new build appears. The controlled toast gives the user the choice.
      - label: "What I learned"
        text: |-
          PWA caching needs product behavior, not just technical caching. Static assets and navigation documents deserve different strategies. Updates should be visible, intentional, and boring in the best possible way.
  - date: "April 30, 2026"
    sortDate: "2026-04-30"
    title: "Making File Uploads Real"
    summary: >-
      I replaced the browser-only file upload path with Supabase Storage uploads.
    sections:
      - label: "What I changed"
        text: |-
          I replaced the browser-only file upload path with Supabase Storage uploads. Before this, uploading a file created a local data URL, which could appear briefly but was not durable across refreshes or devices.
          
          The new shape is cleaner: Supabase Storage stores the actual file bytes, and the `files` table stores metadata such as name, bucket, storage path, public URL, MIME type, size, timestamps, and soft-delete state. localStorage keeps metadata only.
      - label: "What I was trying to learn"
        text: |-
          This was about separating file metadata from file contents. Tasks, notes, and links fit naturally in database rows. Uploaded file bytes do not. They need object storage.
      - label: "What went wrong"
        text: |-
          The original upload feature looked like it worked because the UI could render a local browser object. But it was not actually persistent in the way I need ProjectDesk to work across desktop and Android.
      - label: "Biggest challenge / bug"
        text: |-
          The important risk was avoiding a fake success state. If upload to Storage fails, the app should not add a file card that looks permanent. The file should only appear after the Storage upload succeeds and metadata can be synced.
      - label: "What I learned"
        text: |-
          File upload is not just another field in app state. It needs a two-layer system: object storage for bytes, database metadata for listing and sync. This also sets up the future path for private buckets and signed URLs after authentication exists.
  - date: "April 29, 2026"
    sortDate: "2026-04-29"
    title: "Task Titles and Descriptions"
    summary: >-
      I changed tasks from a single text field into a clearer title plus description model.
    sections:
      - label: "What I built"
        text: |-
          I changed tasks from a single `text` field into a clearer `title` plus `description` model. The dashboard and project task list now stay clean by showing only the task title, while clicking a task opens a detail modal with the title, description, project, importance, priority, done status, and subtasks.
          
          The add/edit task flow now has a title input and optional description textarea. Existing tasks still work because the app maps old `text` values into the new `title` field and keeps `text` as a compatibility mirror for now.
      - label: "What I was trying to learn"
        text: |-
          This was about separating scanning from understanding. A dashboard needs short task names so it stays readable. But actual work often needs more context than a title can hold. Splitting title and description gives both views what they need.
      - label: "What went wrong"
        text: |-
          The risk was changing the data shape while the app already has localStorage, Supabase, sync timestamps, soft deletes, and pending sync markers. A simple rename from `text` to `title` would have broken old local data and existing Supabase rows.
          
          The safer path was a compatibility migration: `title = task.title || task.text`, `description = task.description || ''`, and Supabase keeps the old `text` column while adding `title` and `description`.
      - label: "Biggest challenge / bug"
        text: |-
          The main challenge was avoiding a half-migration. It was not enough to update the task card UI. The title/description shape had to be normalized on load, saved to localStorage, loaded from Supabase, upserted back to Supabase, and used consistently in both the dashboard and project detail views.
      - label: "What I learned"
        text: |-
          Data model changes are product changes. The UI request sounded simple, but because tasks are synced across devices, the app needed a careful compatibility layer and a database migration. Keeping `text` as a temporary mirror is not elegant, but it is the right kind of boring safety while the app evolves.
  - date: "April 29, 2026"
    sortDate: "2026-04-29"
    title: "Choosing a Realistic First Version"
    summary: >-
      I did not cut a release yet, but I clarified how versioning should work for ProjectDesk.
    sections:
      - label: "What I built"
        text: |-
          I did not cut a release yet, but I clarified how versioning should work for ProjectDesk. The app currently has no official release tags, and `CHANGELOG.md` is still under `Unreleased`, even though `package.json` still has the default-looking `1.0.0` value.
          
          The decision was to treat the first real release as a catch-up release, not as the beginning of the project. Since the app already has routing, Supabase sync, localStorage fallback, task/project editing, PWA basics, and a real UI, calling the first tag `v0.1.0` would undersell the maturity. A more honest first release target is `v0.6.0`, meaning functional alpha / early private beta.
      - label: "What I was trying to learn"
        text: |-
          I was trying to understand the difference between commit history, release history, and product maturity. A version number is not a count of how many times I pushed code. It is a communication tool: how stable is this thing, what expectations should I have, and how much room is left before `1.0.0`?
      - label: "What went wrong"
        text: |-
          The project started moving fast before versioning was formalized. That meant the app accumulated several release-worthy milestones without tags: React conversion, local persistence, routing, Supabase sync, sync conflict fixes, PWA basics, and UI polish.
          
          Because of that, `package.json` saying `1.0.0` became misleading. It is technically a version field, but it does not represent an actual release. The true source of release truth should be git tags plus dated changelog sections.
      - label: "Biggest challenge / bug"
        text: |-
          The hardest part was deciding whether the first tag should be conservative or realistic. Starting at `v0.1.0` would be clean from a release-history perspective, but it would make the current app sound like a tiny prototype. Starting at `v0.6.0` is less pure, but more honest about the existing product.
          
          The rule I settled on is: because earlier milestones were never tagged, the first release can be a maturity catch-up release. From there onward, version numbers should advance normally.
      - label: "What I learned"
        text: |-
          Versioning is storytelling for stability. `v0.6.0` says: this app is real and usable, but still not a daily-driver guarantee. `v0.9.0` should mean release candidate. `v1.0.0` should mean I trust it as a stable personal production app.
          
          I also learned that `CHANGELOG.md` should stay under `Unreleased` until a release is intentionally cut. When that happens, the release process should update the package version, convert `Unreleased` into a dated version section, commit `chore: release vX.Y.Z`, tag the commit, and push the tag.
      - label: "What I would do differently"
        text: |-
          I would start versioning earlier, even with tiny tags. `v0.1.0` could have been the React/Vite conversion, `v0.2.0` the localStorage app, `v0.3.0` routing/editing, `v0.4.0` Supabase sync, and `v0.5.0` PWA/deploy readiness.
          
          For future work, I should not bump versions casually. Normal work stays under `Unreleased`. A release happens only when a coherent set of changes is stable, built, merged to `main`, documented, and tagged.
  - date: "April 29, 2026"
    sortDate: "2026-04-29"
    title: "First Release Candidate Polish"
    summary: >-
      I brought ProjectDesk into a shape that feels like a real first private-beta release candidate.
    sections:
      - label: "What I built"
        text: |-
          I brought ProjectDesk into a shape that feels like a real first private-beta release candidate. The project section now has a cleaner visual system: projects use a neutral slate identity instead of competing with the red, orange, and green priority colors. Project creation moved into the project grid as a first-position add card, and project deletion now uses a confirmation modal with the same soft-delete sync model as the rest of the app.
          
          I also polished the dashboard and project cards around the rough edges that appeared during real testing. Long project names no longer push the completion circle out of the project card, long project names in the Priority Dashboard truncate inside their chip, and the Project and Priority columns now feel more aligned.
      - label: "What I was trying to learn"
        text: |-
          This session was about turning working software into something I could trust and look at every day. The main lesson was visual semantics: priority colors should mean urgency, not project identity. If project colors reuse red, orange, green, and purple, the dashboard starts asking my brain to decode two meanings from the same palette.
      - label: "What went wrong"
        text: |-
          The first attempts were too colorful. Muting project colors helped, but the picker still invited me to choose colors that looked like priority states. Then the add-project action became its own little design debate: a header button was easy to reach, but it competed visually; an add card looked better, but could get buried if there are many projects.
          
          The other issue was defensive layout. A fake project named with a long `xxxxx` string quickly exposed that the cards and dashboard chips were not resilient enough. The app looked polished with normal names, but fragile under ugly real-world input.
      - label: "Biggest challenge / bug"
        text: |-
          The hardest part was separating project identity from priority without making the app bland. The final direction was to keep project identity quiet and let priority/status remain semantic. The project cards still have structure and polish, but they do not shout in the same visual language as high/medium/low priority.
          
          The layout bug was also a useful reminder: card headers need reserved space for fixed UI like completion rings. A long title should wrap, clamp, or ellipsize; it should never be allowed to push important controls out of view.
      - label: "What I learned"
        text: |-
          Design systems are not just colors and spacing. They are meanings. A color can only carry so much meaning before the interface becomes noisy. In ProjectDesk, red/orange/green should belong to priority and status. Projects can be identified by placement, name, and subtle neutral accents.
          
          I also learned that release readiness is not only about big features. Small edge cases like long names, centered table chips, stale TODOs, and truthful release notes matter because they reduce friction and make the app feel cared for.
      - label: "What I would do differently"
        text: |-
          I would define semantic colors earlier: priority colors, status colors, and identity colors should be separate from the start. I would also test ugly data sooner: very long project names, empty projects, many projects, and awkward task titles expose layout assumptions faster than normal demo data.
  - date: "April 29, 2026"
    sortDate: "2026-04-29"
    title: "Cutting the First Release"
    summary: >-
      I prepared ProjectDesk for its first official versioned release: v0.6.0.
    sections:
      - label: "What I built"
        text: |-
          I prepared ProjectDesk for its first official versioned release: `v0.6.0`. This means the project finally moved from an always-unreleased working app into a tagged release flow with package metadata, README status, and changelog release notes aligned.
          
          The important part is that this release is not pretending to be `1.0.0`. It is a functional alpha / early private beta: usable and real, but still with mobile, PWA, and sync confidence work ahead.
      - label: "What I was trying to learn"
        text: |-
          I was trying to make release management feel practical instead of ceremonial. The point was not to create paperwork. The point was to make the state of the app understandable: what changed, what version represents this snapshot, and what still needs validation before I call it stable.
      - label: "What went wrong"
        text: |-
          The README had fallen behind the actual app. It still talked about project colors even after I removed project color picking, and it did not clearly mention task descriptions, project creation/deletion, sync status, soft deletes, or the first release target.
          
          That mattered because a release is partly documentation. If the README describes an older app, the version tag becomes less trustworthy.
      - label: "Biggest challenge / bug"
        text: |-
          The main challenge was drawing the line between product work and release work. The app had a lot of good changes ready, but the release needed its own small pass: update README, move `CHANGELOG.md` out of pure `Unreleased`, set the package version, build, commit, tag, and push.
      - label: "What I learned"
        text: |-
          A release is a snapshot with a story. The code matters, but so do the changelog, README, package version, and git tag. They should all point to the same truth.
      - label: "What I would do differently"
        text: |-
          Next time, I would keep README and changelog closer to the work as it lands, so release prep is mostly verification instead of cleanup. I would also decide release targets earlier in the cycle so package metadata does not sit at a misleading default like `1.0.0`.
  - date: "April 28, 2026"
    sortDate: "2026-04-28"
    title: "React Conversion, Routing, Supabase Sync, and Project Hygiene"
    summary: >-
      I turned the original single-file Claude artifact into a proper React + Vite app that I can run locally and use day to day.
    sections:
      - label: "What I built"
        text: |-
          I turned the original single-file Claude artifact into a proper React + Vite app that I can run locally and use day to day. The app now has component files, a real app shell, responsive CSS, localStorage persistence, PWA files, dynamic project routes, editable project and task data, and seeded project content with tasks, subtasks, notes, priorities, and files so I can test the whole workflow without starting from an empty state.
          
          I also added the project maintenance pieces that make this feel like a real repo instead of a loose experiment: `README.md`, `todo.md`, `CHANGELOG.md`, `AGENTS.md`, `.gitignore`, `.env.example`, Supabase setup files, and a private folder for non-committed material. Moving the project into `D:\PERSONAL\ProjectDesk` made it feel like the real home for the app instead of a generated artifact folder. The storage layer now keeps localStorage as the offline cache while preparing data for Supabase sync across desktop and Android.
          
          The app also became more app-like than page-like. Project routes now survive refreshes, project tabs live in the URL, browser back and forward work, and renaming a project updates the route instead of leaving the old slug behind. That mattered because I want to use this daily, not just demo it once.
          
          Before the first push, I also formalized the way I want to maintain the project. `AGENTS.md` now carries the git, versioning, changelog, and journal rules so a future assistant session has the same operating context. `todo.md` now has a phased roadmap, which helps separate immediate mobile/PWA/sync work from later production ideas like auth and RLS.
          
          This first phase felt bigger than a normal conversion because I was also deciding what kind of project this was going to be. It stopped being just "make the artifact run" and became "make this something I can trust tomorrow." That changed the standard. A working screen was not enough; I needed a place for docs, a repeatable setup, a way to remember decisions, and a structure that another session could understand without me retelling the whole story.
      - label: "What I was trying to learn"
        text: |-
          I was trying to learn how to take a useful prototype and give it enough structure that it can survive daily use. The main problem was not just converting HTML to React. It was preserving the visual feel while making the app maintainable, mobile-friendly, persistent, and eventually syncable across devices.
          
          I also wanted to understand the boundary between local-first data and cloud sync. ProjectDesk should work immediately with localStorage, but it should not trap the data on one machine. Supabase is the bridge, and the challenge was designing that bridge without making the app depend on authentication, RLS, or a more complicated backend before I need them.
          
          Another thread was learning how much polish is required before a tool is comfortable on both desktop and Android. Summary cards need to wrap, task tables need to become card-like on small screens, buttons need to be finger-sized, and the original visual design needs to survive those changes. Responsiveness is not just a CSS afterthought here; it is part of whether the app will actually be used.
          
          I was also learning how easily a prototype can hide missing product decisions. The original artifact already looked useful, but once I wanted to use it daily, questions appeared everywhere: where does the data live, what happens after refresh, how does a phone user tap this, what happens when the URL changes, and how do I know which changes belong in git? Those questions were not separate from the build. They were the build becoming real.
      - label: "What went wrong"
        text: |-
          The hardest part was that the app went through several layers of "it looks fixed, but it is not fixed yet." First, the UI updated when I added a task, but Supabase stayed empty because the save path was not obvious enough. Then the console started showing `Saving to Supabase`, which proved the persistence hook was running, but Supabase rejected the rows because the database expected UUIDs while the original local seed data used friendly string ids like `polimetrics`.
          
          After that, another confusion appeared: the browser kept showing older Supabase requests against `project_ecosystem_*` tables even after the code had been changed to use `projects`, `tasks`, and `subtasks`. That pointed to stale browser/PWA cache or an old dev server still serving previous code. The code and the browser were out of sync, which made the error messages feel contradictory until I treated the running app itself as a separate thing to debug.
          
          There was also basic environment friction. `npm` was not available in PowerShell inside VS Code at first, so even running the project required fixing the local Node/npm setup before the app work could continue. It is easy to dismiss that as setup noise, but it matters because the project is only useful if I can actually start it myself without depending on the assistant every time.
          
          The emotional pattern of this session was "one more fix should do it," followed by a new symptom appearing somewhere else. That was frustrating, but useful. Each new failure narrowed the system boundary: first the save path, then the database schema, then browser cache, then routing. I want the journal to remember that progress did not feel linear while it was happening.
      - label: "Biggest challenge / bug"
        text: |-
          The biggest bug was the Supabase sync path because it combined three different problems that looked like one problem. There was the app-state mutation problem, where changes needed to go through a single `persist(nextState)` helper. There was the data-shape problem, where nested projects, tasks, and subtasks needed to flatten into relational rows with the correct foreign keys. And there was the id problem, where local prototype ids had to become UUID-safe before Supabase would accept them.
          
          The fix was to make persistence explicit in `App.jsx`, then make the storage adapter defensive. New ids now use `crypto.randomUUID()` when available, and older string ids are converted into stable UUIDs before rows are sent to Supabase. I also learned to distrust a passing UI update as proof of persistence. A UI can look correct while the save path is completely broken.
          
          The routing work had its own subtle challenge. A route based on a project name is nicer to read, but it cannot be treated as static. If I rename a project, the URL has to canonicalize to the new slug, while old id-based links still need to keep resolving where possible. That is the difference between "there is a URL" and "navigation feels trustworthy."
          
          The resolution was not one big clever move. It was a sequence of small confirmations: the app built successfully, local routes loaded, Supabase accepted rows, and the repo finally had the maintenance files that explain how to keep going. That matters because this project is supposed to become something I use, not just something that technically runs once.
      - label: "What I learned"
        text: |-
          The main lesson is that offline-first sync needs an adapter boundary that is stricter than the UI state. The UI can use nested project objects because that is ergonomic for React. Supabase needs flat tables with ids and foreign keys. Mixing those concerns makes every bug harder to see. A storage adapter should be the only place that knows how to translate between those worlds.
          
          I also learned that PWA and dev-server caching can make debugging feel haunted if I forget that the browser may not be running the code I just edited. When a console error points at old table names or old line numbers, the next step is not more code changes. The next step is to verify which server is running, reload hard, and clear or bypass stale service-worker state.
          
          I learned that documentation files have different jobs. `README.md` explains how to run and understand the project. `todo.md` keeps unfinished work visible. `CHANGELOG.md` describes what changed for the project. This journal is different again: it records why the work was difficult and what I learned from it. Keeping those separate makes the repo easier to maintain.
      - label: "What I would do differently"
        text: |-
          I would define the Supabase schema and id strategy before writing the first sync adapter. The original app data came from a prototype, where ids like `polimetrics` were useful and readable. Supabase wanted UUIDs. That mismatch should have been resolved at the boundary from the beginning instead of discovered through 400 errors in the browser console.
          
          I would also add a tiny visible sync diagnostic sooner, at least during development. Browser console logs helped, but a simple internal sync status that distinguishes "saved locally", "syncing remotely", and "remote sync failed" would make daily use less mysterious, especially when testing across desktop and Android.
          
          I would also separate "simple Supabase sync now" from "real multi-user sync later" more explicitly from the start. No login, no RLS, and no realtime is the right first step for this app, but the todo list needs to keep future auth and RLS visible so I do not accidentally treat the simple version as production-ready security.
          
          The proof that this first phase was solved was not a single green check. It was that I could explain the project back to myself: how to run it, what files matter, what is private, what is future work, and how data is supposed to move. That clarity is easy to undervalue, but without it the code would still feel like a generated artifact instead of my project.
  - date: "April 28, 2026"
    sortDate: "2026-04-28"
    title: "Remote Hydration on Deployed Refresh"
    summary: >-
      I fixed the deployed reload behavior after discovering that tasks created in the Vercel app were successfully reaching Supabase but disappearing from the UI after refresh.
    sections:
      - label: "What I built"
        text: |-
          I fixed the deployed reload behavior after discovering that tasks created in the Vercel app were successfully reaching Supabase but disappearing from the UI after refresh. The app now treats startup as a two-step hydration process: load localStorage immediately so the interface appears fast, then fetch Supabase and replace the local state if remote projects or tasks exist. The remote state is written back into localStorage, but the app does not save local data back to Supabase during that initial load.
          
          I also added console logs for the hydration path: `Loaded local state`, `Loaded remote Supabase state`, and `Hydration complete`. Those logs are intentionally plain because this bug needed visibility more than cleverness.
      - label: "What I was trying to learn"
        text: |-
          I wanted to understand why the save path could be correct while refresh was still wrong. That distinction matters. A row appearing in Supabase proves the write adapter works, but it says nothing about whether the deployed app reconstructs remote rows into the nested project/task/subtask shape the UI expects.
          
          The misleading part was emotional as much as technical. Seeing the data in Supabase gave me a little confidence, then refreshing and losing it immediately took that confidence away. I had to separate "the database has the row" from "the interface knows how to become that row again." Those are different problems, even though they look identical when the UI is blank or stale.
      - label: "What went wrong"
        text: |-
          The original startup logic was too implicit. It loaded local state, then tried remote state, but the only guard was a generic `hydrated` flag. That made it too easy for future save logic to blur initialization and user edits. Also, remote loading treated optional file loading as part of the same remote fetch path, which could make an unrelated files issue block the important task/project hydration.
          
          This was misleading because the save path was already working. Seeing rows in Supabase made it tempting to assume the reload bug had to be somewhere small, but the actual problem was the startup story. The app needed to show local data quickly, respect remote data when it was newer, and avoid accidentally treating initial load as a user edit. Those are three different responsibilities, and I had been letting them blur together.
      - label: "Biggest challenge / bug"
        text: |-
          The biggest challenge was separating "fast local UI" from "authoritative remote sync" without causing a write-back loop. If localStorage loads first, the app must not immediately decide that localStorage is the latest truth and push it back to Supabase. The fix was to add explicit `hasHydrated` and `isInitializing` guards so `saveState()` cannot run until the remote load has finished.
          
          The proof for this stage was not just "the build passed." The proof was seeing the hydration logs in order and understanding what each one meant: local state loaded first, remote state was fetched, remote state was applied to React state, then hydration completed. That gave me a way to reason about the startup process instead of guessing from the UI.
      - label: "What I learned"
        text: |-
          Hydration is its own phase, not just the first render. In an offline-first app, startup has at least two truths: the cached local truth and the remote truth. The code needs to name that transition clearly, otherwise a later mutation path can accidentally treat initialization like an edit.
          
          I also learned to be careful with words like "load" and "save." They sound simple, but here they carried hidden timing rules. Loading localStorage is not the same as accepting it as final truth. Fetching Supabase is not the same as overwriting everything. Saving should not happen just because initialization moved data through state. Naming those phases made the bug smaller.
      - label: "What I would do differently"
        text: |-
          I would add a small storage/hydration test around remote composition before deploying sync behavior. It should verify that flat Supabase rows become nested projects with tasks and subtasks, and that initialization writes remote state to localStorage without triggering a remote save.
          
          I had to tighten the hydration fix one more time because the first version still allowed a strange one-refresh delay: Supabase rows were being cached locally, but the React UI was not reliably showing the remote state until the next reload. The important correction was to apply the normalized remote state directly with `setData()` before marking hydration complete, and to treat the localStorage write as a cache update rather than the thing the UI waits on. That is a useful distinction: localStorage is a persistence layer, not a UI update mechanism.
          
          The next suspicion was routing. The URL used a readable slug like `/projects/portfolio-journal`, while the project display name lived as `"Portfolio Journal"`. I added a normalized `slug` field to project state so route identity is explicit, while the UI can still show the human name. The important bit is that the slug stays generated from the current project name, so renaming a project still moves the URL in the way I wanted.
          
          That exposed one more persistence gap: the slug existed in app state, but the live Supabase project table did not have a `slug` column yet, and the project upsert did not include it. I added `slug` to the schema, added a small SQL migration, and wired project saves to include it. Running the `alter table projects add column if not exists slug text;` SQL in Supabase was the missing database-side step.
          
          Looking back, this entry was the first hint that the refresh problem was not only about sync. The data path could be correct and still not enough if the route identity was unstable. At the time, I was still thinking mostly in terms of local versus remote state. The later route bugs made this clearer: a saved project is only useful if the URL can reliably find it.
  - date: "April 28, 2026"
    sortDate: "2026-04-28"
    title: "Project Rename Navigation Fix"
    summary: >-
      I fixed the last awkward part of project renaming.
    sections:
      - label: "What I built"
        text: |-
          I fixed the last awkward part of project renaming. The app was already saving the new project name and slug, but immediately after clicking Save it could navigate to the new URL before React could resolve that slug in the current state. Now the rename path builds the next normalized app state first, persists it, and only then moves the URL to the new project slug.
          
          I also made route lookup more explicit: slug first, generated route key second, id last. That keeps readable project URLs as the normal path while preserving older id-based links where practical.
          
          This was the moment where I started to feel the cost of naming something wrong. The variable was called like an id, but the value in the URL was often a slug. That small mismatch made the code easier to misunderstand. The app did not just need a fix; it needed the code to admit what the value really was.
      - label: "What I was trying to learn"
        text: |-
          I was trying to understand the difference between persistence being correct and navigation feeling correct. The data could be saved, but if the router briefly saw a slug that the current state did not know yet, the app treated the route as unresolved and went home.
      - label: "What went wrong"
        text: |-
          The bug was a timing issue hidden inside a simple rename flow. I was changing route state and app state in the same interaction, but the fallback logic could still evaluate the new route against the old project list. That made the app look like the rename had broken the route even though the name itself had persisted.
          
          This was one of those bugs where the UI made the wrong thing look guilty. Because the app went home after renaming, it felt like the name had not saved or the route had not updated. In reality, the new name could persist and the new route could be valid, but the guard could still check too early. The failure was in the transition between states, not in either final state.
      - label: "Biggest challenge / bug"
        text: |-
          The hardest part was not the slug generation. It was making sure the app never points the URL at a route the current React state cannot resolve. The fix was to create one full next state object, normalize it, pass that to persistence, and then update the active route from that same state.
          
          At this stage I thought the ordering fix was probably enough. It was a reasonable theory: update the state, then update the route. But the later failures showed that "state update" in React does not mean "state is immediately available to every effect." That was the part I had not fully respected yet. The journal should preserve that false confidence because it explains why the next fix existed.
      - label: "What I learned"
        text: |-
          When UI state and URL state both describe the same object, they need to be updated from the same source of truth. A route fallback is useful, but it should wait until hydration and initialization are complete, otherwise it can punish a valid route that is only temporarily unresolved.
          
          I also learned that a route guard can be both necessary and dangerous. It protects the app from invalid URLs, but if it runs during a legitimate transition it can become the bug. That is why the later `pendingRoute` fix mattered: it gave the app a way to say, "I am moving to a valid route, but the state has not fully caught up yet."
      - label: "What I would do differently"
        text: |-
          I would put route-state transitions behind a smaller helper earlier. Project renames, tab changes, hydration canonicalization, and back/forward navigation all touch the same idea: keep the URL and active project in sync without racing the current state.
          
          The proof here was incomplete at first. The app looked better after renaming, but the later deployed refresh tests showed that I had only solved part of the transition. That is a useful reminder: a fix can be locally true and still incomplete across refresh, deployment, and remote sync. I should test routing fixes in the exact place they failed, not only in the local happy path.
  - date: "April 28, 2026"
    sortDate: "2026-04-28"
    title: "The Refresh Bug Was Three Bugs"
    summary: >-
      I finished tracing the refresh and rename problem all the way down to its real cause.
    sections:
      - label: "What I built"
        text: |-
          I finished tracing the refresh and rename problem all the way down to its real cause. At the start, it looked like a simple refresh bug: I would create or rename something, refresh the deployed app, and the project view would disappear or fall back home. By the end, the app had proper Vercel SPA routing, clearer hydration logs, explicit project slugs, slug persistence in Supabase, and safer rename navigation.
          
          The final clean behavior is simple: the route parameter resolves by `project.slug` first, then falls back to `project.id`. When I rename a project, the app updates the project `name` and `slug` in state, persists that full updated state, and only then navigates to the new slug URL. Not the other way around.
      - label: "What I was trying to learn"
        text: |-
          I was trying to learn how to debug a bug whose symptom kept pointing at different systems. The same visible behavior could be explained by Vercel, Supabase, localStorage, hydration timing, browser cache, or the router. The lesson was to keep narrowing the evidence instead of assuming the first plausible explanation was the real one.
      - label: "What went wrong"
        text: |-
          The "simple refresh bug" was actually three bugs wearing the same mask.
          
          First, there was a Vercel SPA routing bug. Refreshing a route like `/projects/portfolio-journal/tasks` can produce a 404 on a static host if the host tries to find that path as a real file. The fix was `vercel.json`, telling Vercel to always serve `index.html` so React can handle client-side routes.
          
          Second, there was a hydration and sync suspicion. Because data appeared to disappear after refresh, it looked like Supabase or localStorage was failing. That was a reasonable suspicion, so I added logs around local state, remote Supabase state, row counts, and the final React state. Those logs showed that projects and tasks were actually loading correctly. The data was there.
          
          Third, there was the real root cause: route identity mismatch. The URL used readable slugs like `/projects/spark/tasks`, but parts of the app still treated that route value as if it were a project id. The real project ids are UUIDs. So after refresh or rename, the app could have valid data in memory but still fail to resolve the active project, because it was comparing the route slug to the wrong kind of identifier. When it could not resolve the project, it fell back home.
      - label: "Biggest challenge / bug"
        text: |-
          The hardest part was that the symptom changed depending on timing. On the first refresh, it looked like a sync failure because the task or project seemed to disappear. On the second refresh, it came back, which made the problem look like cache or hydration. Later, the route logs finally showed the smoking gun: `unresolved project route`.
          
          That log changed the investigation. If state had the projects and tasks, but the route was unresolved, the problem was not Supabase. It was the mapping between URL and project. The app needed a consistent rule: `routeParam -> project.slug first -> project.id fallback`.
          
          The rename flow had the same lesson. Navigating to `/projects/new-slug/tasks` before the updated state could resolve `new-slug` created a brief invalid route. The correct order is: update name and slug in state, persist that state, then navigate to the new slug.
      - label: "What I learned"
        text: |-
          I learned that "data disappeared" is not always a storage bug. Sometimes the data is loaded correctly, but the current view cannot find it because routing identity is wrong. In this app, project ids and project slugs both matter, but they are not interchangeable.
          
          I also learned that a good debug log should answer a specific question. The hydration logs proved the data existed. The route logs proved the active route could not resolve. Together they separated a sync problem from a navigation problem.
      - label: "What I would do differently"
        text: |-
          I would define route identity earlier. If routes use slugs, projects should have a first-class `slug` field from the beginning, and every route lookup should follow the same rule. I would also add route debug logs sooner: current path, route param, active project id, active project slug, and available project slugs. That would have made the real bug visible faster.
          
          I had to tighten the rename fix one more time after realizing that even the correct route resolver could still race React state updates. The important detail was ordering. The app now sets a `pendingRoute` before persisting the renamed state, which tells the route guard not to fall back home while the renamed project is still being applied. Once the new slug can resolve against the updated project list, the pending route is applied. This made the logic match the mental model: pause the guard during the transition, then move the URL only when the route is real.
          
          The next layer was stale remote data. A first refresh could still briefly look wrong because localStorage had the newest edit, while Supabase might still return the previous project or task version. I added `updated_at` to project and task state and changed hydration to merge local and remote by freshness instead of blindly trusting remote. That is a better fit for an offline-first app: remote sync is important, but newer local edits should not be overwritten just because the network response is a little behind.
          
          This is exactly the kind of bug that can eat a morning. Not because any single part is impossibly hard, but because several normal systems were barely out of sync at the same time: routing, async React state, Supabase latency, local cache, and merge logic. Any one of them being slightly off created weird behavior that looked like a different problem.
          
          The important thing is that ProjectDesk has crossed a real threshold now. I fixed SPA routing on Vercel, hydration ordering, slug-versus-id route resolution, the route guard race, timestamp tracking, and stale remote overwrite handling. This is no longer just a beginner prototype. It is starting to behave like a real local-first app with the kind of edge cases real apps have.
          
          The final test confirmed it. The route resolved correctly to `oportuno`, the project stayed open, the newer local state survived remote hydration, there was no fallback to the home page, and the second-refresh bug was gone. That was the proof I needed that the fix was not just hiding the symptom anymore. The app is now keeping route identity, local cache, and Supabase hydration in agreement on the first refresh.
          
          After the bug was solved, I cleaned up the temporary logging I had used to find it. That felt like a small but important maturity step. During debugging, the noisy logs were helpful because they exposed what the app believed about hydration, routes, tasks, and Supabase rows. Once the issue was fixed, those same logs became clutter and made the app feel less polished.
          
          The new logger keeps the useful signals without dumping full app state into the console. Development can still show hydration and sync summaries, while production keeps only warnings and errors. This is a good reminder that debug tools have a lifecycle: add noise when I need visibility, then remove or tame that noise once the system is understood.
  - date: "April 28, 2026"
    sortDate: "2026-04-28"
    title: "Deciding the Git Workflow"
    summary: >-
      I updated AGENTS.md with a practical Git workflow for ProjectDesk.
    sections:
      - label: "What I built"
        text: |-
          I updated `AGENTS.md` with a practical Git workflow for ProjectDesk. The rule is now clear: `main` is production because Vercel deploys from it, and branches are the safe workspace for anything that changes app behavior. Documentation-only edits can still go straight to `main`, but logic, routing, storage, sync, schema, UI behavior, dependencies, and anything that needs testing should happen on a branch.
          
          I also clarified the commit language. Internal or developer-only changes should not automatically be called `feat`. The logging cleanup is a good example: it added a helper, but the user-facing behavior is not a new product feature, so `chore` is the better type.
      - label: "What I was trying to learn"
        text: |-
          I was trying to find a workflow that protects the deployed app without making solo development feel heavy. I do not need enterprise ceremony for a personal project, but I do need a buffer between "I am experimenting" and "Vercel is deploying this to production."
      - label: "What went wrong"
        text: |-
          The confusion came from mixing two habits. Earlier, I had been pushing directly to `main` because it was fast and the project was moving quickly. But once Vercel is connected, `main` stops being just a branch; it becomes the production release path. That changes the meaning of a push. A direct push is no longer just saving work to GitHub. It can become a deploy.
          
          The existing `AGENTS.md` already said not to commit directly to `main`, but the workflow was not specific enough for how I actually want to work. It still talked about PRs as the default, which felt too heavy for solo work, while the branch rule was not concrete enough to stop us from sliding back into direct-to-main pushes.
      - label: "Biggest challenge / bug"
        text: |-
          The hardest part was choosing rules that are strict enough to protect production but simple enough that I will actually follow them. If every typo requires a branch, the workflow becomes annoying and I will start ignoring it. If app logic can go straight to `main`, Vercel can deploy broken behavior too easily.
          
          The compromise is practical: behavior changes go through a branch, docs and tiny safe edits can go directly to `main`, and `npm run build` is required before merging branch work. The mental model is simple: `main` is production, branch is experiment/work.
      - label: "What I learned"
        text: |-
          I learned that Git workflow should match deployment reality. Before Vercel, pushing `main` mostly meant backup and history. After Vercel, pushing `main` means release. That makes branch discipline more important, even for a solo developer.
          
          I also learned that the commit type should describe the user impact, not just the fact that code was added. A new internal logger is useful, but it is a `chore`, not a product `feat`. That keeps the changelog and history more honest.
      - label: "What I would do differently"
        text: |-
          I would define this workflow as soon as Vercel is connected. The moment `main` becomes the deployment source, the project needs a lightweight release boundary. I would also pause before every commit and ask: is this production behavior, developer tooling, documentation, or a fix? That one question should decide both the branch name and the commit type.
  - date: "April 28, 2026"
    sortDate: "2026-04-28"
    title: "Task Pagination as Daily-Use Polish"
    summary: >-
      I added frontend pagination to the priority task list.
    sections:
      - label: "What I built"
        text: |-
          I added frontend pagination to the priority task list. The dashboard now shows 10 tasks by default, lets me move between pages, shows the current range with text like "Showing 1-10 of 27", and includes a simple rows-per-page selector for 10, 25, or 50 tasks.
          
          The important detail is that filtering happens before pagination. That means the page count and visible rows are based on the filtered list, while the summary cards still describe the full filtered dataset rather than just the current page.
      - label: "What I was trying to learn"
        text: |-
          I was trying to make the dashboard feel more usable as real data grows. The seeded app was already useful for testing, but a daily app will eventually have enough tasks that an endless list becomes tiring. Pagination is not glamorous, but it makes the interface calmer.
      - label: "What went wrong"
        text: |-
          The only real decision point was where pagination belonged. At first the request sounded like it might apply to the project task tab, but the existing "task table" is the priority dashboard list. Once I checked the code, the right place was clear: paginate the filtered dashboard tasks in `PriorityDashboard.jsx`, not the project detail cards.
      - label: "Biggest challenge / bug"
        text: |-
          The main challenge was keeping the numbers honest. It would be easy to paginate first and then filter, or to let the summary cards accidentally reflect only the current page. The correct behavior is filters first, summary from the full filtered set, page controls from the filtered count, and visible rows from the current page.
      - label: "What I learned"
        text: |-
          Small UX improvements still need clear data boundaries. "Tasks shown", "tasks filtered", and "tasks summarized" sound similar, but they are different sets. Naming those sets in code made the feature easier to reason about.
      - label: "What I would do differently"
        text: |-
          If task volume keeps growing, I would eventually consider making pagination state part of the URL or remembered UI preferences. For now, local component state is enough because this is frontend-only polish and does not need database or routing changes.
  - date: "April 28, 2026"
    sortDate: "2026-04-28"
    title: "Stale Device Sync Conflicts"
    summary: >-
      I changed the sync model so one stale device should not resurrect data that another device already deleted.
    sections:
      - label: "What I built"
        text: |-
          I changed the sync model so one stale device should not resurrect data that another device already deleted. Projects, tasks, and subtasks now support `deleted_at` tombstones, and the UI hides deleted items instead of removing them from local state immediately. That means a delete remains syncable even though it disappears from the interface.
          
          The Supabase save path also became more careful. Instead of blindly upserting every local row on every save, it checks the remote row freshness first. A local project rename can still write the project row, but stale local tasks are skipped if Supabase has a newer update or delete event for those tasks.
      - label: "What I was trying to learn"
        text: |-
          This was about the next level of local-first sync. The previous fixes made refresh and hydration reliable, but they still assumed each device was mostly up to date before saving. That is not true in daily use. My desktop can delete a task, my phone can still have the old task cached, and then the phone can make an unrelated edit before it has refreshed.
          
          The important lesson is that a full nested state save is dangerous once multiple devices exist. Saving "everything I know" from a stale device is not the same as saving "the thing I just changed."
      - label: "What went wrong"
        text: |-
          The old sync path treated the local app state as one big truth. That was fine when there was one device or when both devices were refreshed, but it failed the moment one client was stale. If mobile renamed a project while still holding an old task list, the old task list could be sent back to Supabase as if it were current.
          
          Hard deletes made this worse. Once a task was removed from local state, there was no durable delete event to compare against. The app needed to remember that a deletion happened, not just hide the item.
      - label: "Biggest challenge / bug"
        text: |-
          The bug was not that Supabase failed. Supabase did exactly what the app asked: accept rows. The problem was that the app was asking the database to save too much stale context. The real unit of conflict is the item, not the whole app state.
          
          The fix was to make `updated_at` and `deleted_at` act like per-item event timestamps. During merge, the latest item event wins. During save, local rows only upsert if their latest event is newer than the remote row. A deleted item stays hidden, but the tombstone remains available so other devices can converge.
      - label: "What I learned"
        text: |-
          Soft deletes are not just about undo or audit history. In a sync app, they are how deletion travels. Without a tombstone, an offline or stale client cannot know whether an absent row means "deleted" or "not loaded here yet."
          
          I also learned that mutation-level sync is the direction this app should keep moving toward. This fix still preserves the simple storage adapter, but it makes the full-state sync much less dangerous by filtering stale rows before writing them. The mental model is better now: project rename updates the project, task delete updates the task, and unrelated stale rows should not get a free ride back into the database.
      - label: "What I would do differently"
        text: |-
          I would design delete semantics before adding cross-device sync. The first working version made save/load possible, but delete behavior is where sync trust really gets tested. Next time, I would add `deleted_at` at the same time as `updated_at`, because they are two halves of the same conflict story.
  - date: "April 28, 2026"
    sortDate: "2026-04-28"
    title: "Local Ghost-State Divergence"
    summary: >-
      I tightened hydration so Supabase becomes the source of truth after a successful remote load.
    sections:
      - label: "What I built"
        text: |-
          I tightened hydration so Supabase becomes the source of truth after a successful remote load. The app now drops local-only tasks and subtasks unless they are explicitly marked `sync_pending`, which means they represent a real local edit that has not had a chance to sync yet.
          
          I also added a temporary development-only count diagnostic for hydration. It reports local active task count, remote active task count, merged active task count, and the local-only or remote-only task ids. That gives me a direct way to prove whether the app is converging or preserving ghosts.
      - label: "What I was trying to learn"
        text: |-
          This was the next sync lesson after soft deletes. Tombstones stopped stale devices from resurrecting deleted rows, but manual refresh still showed different counts between desktop and mobile. That meant the problem had moved from "stale writes" to "stale local cache surviving hydration."
          
          The key distinction is subtle: localStorage is useful for fast startup and offline edits, but after Supabase loads successfully, old local-only rows should not be treated as equal truth.
      - label: "What went wrong"
        text: |-
          The merge logic was too generous. It preserved the union of local and remote ids, which sounded safe at first because it avoided losing local work. But in practice it meant old local-only tasks could live forever even after Supabase no longer had them.
          
          That created the confusing symptom: both devices could sync new actions, but after refresh their task counts still differed. The app was not failing to load Supabase. It was loading Supabase and then re-attaching stale local extras during merge.
      - label: "Biggest challenge / bug"
        text: |-
          The hardest part was not dropping legitimate unsynced work. If I made Supabase blindly overwrite localStorage, a task created locally right before a refresh could disappear before its background save finished. The compromise is `sync_pending`: normal local-only ghosts get dropped, but explicitly pending local edits survive.
          
          I also made local edits mark the changed row, not the whole project tree, as pending where possible. That keeps the mental model closer to the real conflict: editing a task should not make an old project row authoritative, and renaming a project should not drag stale tasks along with it.
      - label: "What I learned"
        text: |-
          Offline-first does not mean local-first forever. It means local is allowed to be useful while the network is uncertain. Once the remote source loads successfully, the app needs rules for what local data is still allowed to argue with it.
          
          The new rule is clearer: Supabase wins unless local has a concrete pending edit. That should make refresh a convergence point instead of another chance for stale data to survive.
      - label: "What I would do differently"
        text: |-
          I would add pending-sync state at the same time as the first Supabase adapter. It is easy to think of sync as "load and save rows," but real sync needs item identity, freshness, deletion events, and pending local intent. Without that last piece, the app cannot tell the difference between a real unsynced task and a ghost from yesterday.
  - date: "April 28, 2026"
    sortDate: "2026-04-28"
    title: "Automatic Remote Refresh"
    summary: >-
      I added automatic remote refresh so desktop and mobile do not depend on manual browser refresh to converge.
    sections:
      - label: "What I built"
        text: |-
          I added automatic remote refresh so desktop and mobile do not depend on manual browser refresh to converge. The app now refreshes from Supabase after startup hydration, when the window gains focus, when the page becomes visible again, every 20 seconds while open, and once after a successful local save.
          
          The sync indicator also became more honest. Instead of only saying saving or saved, it now has clear states for syncing, synced, error, and offline. It stays small and out of the way, but it gives me a signal that the app is doing work in the background.
      - label: "What I was trying to learn"
        text: |-
          This was about moving from "correct after refresh" to "correct while I am using it." A daily app should not require me to remember which tab or device has the newest state. If I change something on mobile, desktop should catch up on its own.
          
          The goal was not to add Supabase Realtime yet. I wanted the simpler version first: regular remote refresh with strong merge rules and no overlapping fetches.
      - label: "What went wrong"
        text: |-
          The previous fixes made data trustworthy after a refresh, but that still left an awkward product problem. Both devices could be correct eventually, while still feeling stale in the moment. That is not good enough for daily use because it forces me to manually refresh and then wonder whether I refreshed enough times.
          
          There was also a status problem. The app had a sync indicator, but it only knew the happy path. If the network was offline or Supabase failed, the UI did not have a good way to admit that.
      - label: "Biggest challenge / bug"
        text: |-
          The tricky part was avoiding overlapping refreshes. Focus, visibility, interval polling, and post-save refresh can all happen close together. Without a guard, the app could start multiple remote fetches and make the state harder to reason about.
          
          The fix was a single `refreshFromRemote()` path with an in-flight guard. It fetches Supabase, merges remote state with the current local state using the existing timestamp/delete/pending rules, applies the result to React state immediately, and writes it back to localStorage.
      - label: "What I learned"
        text: |-
          Convergence is a product behavior, not just a database behavior. Supabase can have the correct rows, and localStorage can eventually catch up, but the app still needs a rhythm for checking remote truth while it is open.
          
          I also learned that a sync indicator should be simple but honest. "Synced" feels reassuring only if the app can also say "syncing," "offline," or "error" when that is what is really happening.
      - label: "What I would do differently"
        text: |-
          I would add a remote refresh loop shortly after the first working Supabase sync. The app does not need Realtime immediately, but it does need a basic convergence rhythm before cross-device testing feels sane.
          
          The next small trust improvement was adding "last synced" text to the indicator. The app already knew when it was syncing, offline, or in error, but "synced" without time still left a little uncertainty. Now the indicator can say "Synced just now", "Synced Xm ago", or "Synced Xh ago", which is a tiny UI detail that carries a lot of confidence when checking desktop and mobile side by side.
---

## What I built

ProjectDesk is a personal project workspace for organizing project notes, tasks, priorities, and the operational details that make a project easier to continue after a break.

The project has become a place to explore local-first behavior, authentication boundaries, mobile ergonomics, PWA constraints, testing, and the practical tradeoffs of making a small tool reliable.

## What I am trying to learn

I am using ProjectDesk to learn how everyday productivity software becomes dependable: how state survives refreshes, how offline and installed-app behavior changes the design, and how small interaction details affect whether a tool feels usable in real life.

## What I am documenting

The journal captures implementation choices, bugs, testing decisions, mobile polish, release discipline, and the moments where a prototype started needing product-level care.
