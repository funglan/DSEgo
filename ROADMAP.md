# HKDSE Biology Portal — Advanced Build Roadmap

This maps your original 19-point spec to what's built in this pass ("Phase 1 demo")
versus what's still ahead, and gives the concrete next steps for each.

## What's live now (Phase 1 — in `public/index.html`)

- A 15-question seed bank across all 5 HKDSE Biology syllabus strands x
  Easy/Medium/Hard, bilingual (EN / 繁中), with 4 questions built around original
  SVG diagrams (enzyme rate vs. temperature, potato osmosis bar chart, yeast
  population growth curve, primary/secondary antibody response) — demonstrating
  the "read a graph, answer the question" format.
- DSE-style MCQ player: numbered stem, A–D options, instant marking.
- Answer + explanation panel, hidden by default, opened on click (point 7).
- Automatic hint shown only when the student picks the wrong option (point 8).
- Attempts are written to Firestore (`attempts` collection) with topic,
  subtopic, difficulty, chosen option, correctness — the data layer for
  point 12's analytics.
- Teacher dashboard panel (visible only to `role: teacher` accounts) that
  aggregates attempts by topic and by student, with % correct.
- Google (Gmail) sign-in added alongside the existing email/password flow
  (point 15), auto-creating a `student` profile on first login.
- Page-level EN/中 toggle in the top bar (point 5); the mechanism reads any
  element tagged `data-en`/`data-zh` plus re-renders the active question.
- `firestore.rules` written so: students can only write their own profile/
  attempts; only accounts with `role: teacher` can read every student's
  attempts or (later) write to the question bank.
- `HKDSE_Biology_Question_Bank_Template.xlsx` — the bulk-authoring template
  matching the question schema, with dropdowns for topic/difficulty/answer/
  source and an Instructions sheet (point 1/18).

## Why only 15 questions, and a copyright note (point 18)

Every seed question is either written from scratch ("Original") or an
original question modelled on a real HKDSE topic/graph type ("HKDSE-style,
adapted") — never a verbatim copy of past-paper wording or artwork. Real
past-paper questions have Examination Authority copyright, so they need to
be manually retyped/adapted by a teacher (or licensed) rather than scraped
or reproduced by an AI — the Excel template and future upload tool are
built to make that easy once you have the source material.

## Phase 2 — teacher upload + email intake (points 9, 13)

Not built yet; needs your decisions/credentials, so here's the shape:
- A "Teacher upload" page reads the Excel template client-side (SheetJS),
  validates rows, and writes them into the `questions` Firestore collection
  (rules already allow teacher-only writes there).
- Email-in pipeline: a teacher emails the filled-in Excel file to a fixed
  address → a Cloud Function (2nd gen) parses the attachment and imports it.
  This needs an inbound-email service (e.g. SendGrid Inbound Parse, Mailgun
  routes, or Gmail API push notifications with domain-wide delegation) plus
  the Blaze (pay-as-you-go) Firebase plan for Cloud Functions/outbound
  network access. I can build the Function once you pick a provider and
  enable billing.

## Phase 3 — structural (long) questions (point from your first message)

Once the MCQ engine is solid, the same schema extends to short-answer/
structural items: add a `type: "structural"` question with a text/upload
answer box instead of A–D options, marked by keyword-matching or left for
manual teacher marking in the dashboard.

## Phase 4 — publishing, GitHub, and rollout (points 16, 17, 19)

- Demo first (this build) — try it locally via `firebase serve` or by
  opening `public/index.html`, or deploy to your existing Hosting site
  (`firebase deploy --only hosting,firestore:rules`).
- GitHub: I initialised a local git repo and made the first commit (see
  below) — push it with `git remote add origin <your-repo-url>` then
  `git push -u origin main` once you've created the empty repo on GitHub.
- Wider release to all HKDSE Biology students: once you're happy with the
  demo, we can add a public landing page copy, usage limits/quotas per
  account, and a lightweight admin approval step for new teacher accounts
  so random sign-ups can't self-promote to `role: teacher`.
- "School style" (point 19): the current visual theme (teal/white, rounded
  cards) is a safe generic school look; if you have your school's actual
  colours/crest we can swap the theme quickly.

## Suggested order of next sessions

1. Open the demo, sign in with Google, try a few quizzes in both languages,
   sanity-check the hint/explanation/teacher-dashboard behaviour.
2. Decide the email-intake provider (SendGrid/Mailgun/Gmail API) so Phase 2
   can be built for real.
3. Send me a handful of real past-paper topics/graphs (not verbatim text)
   you want prioritised, and I'll expand the seed bank before wiring the
   teacher upload tool.
