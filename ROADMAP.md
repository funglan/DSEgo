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
- Teacher upload tool (point 9, Spark-plan friendly): the teacher dashboard
  now has an "Upload question bank" control. It reads the filled-in Excel
  template entirely in the browser (SheetJS), validates every row (required
  fields, answer must be A–D, difficulty must be Easy/Medium/Hard), and
  batch-writes valid rows into the `questions` Firestore collection —
  no server, no Cloud Functions, no billing plan upgrade needed. Uploaded
  questions are merged with the seed bank automatically the next time any
  student starts a practice session. Rows with an `image_description` but
  no hand-built diagram show as a labelled placeholder box rather than a
  real chart, since Excel can't carry an actual graph — only a Firestore-
  backed vector figure (like the 4 seed questions) can.

## Why only 15 questions, and a copyright note (point 18)

Every seed question is either written from scratch ("Original") or an
original question modelled on a real HKDSE topic/graph type ("HKDSE-style,
adapted") — never a verbatim copy of past-paper wording or artwork. Real
past-paper questions have Examination Authority copyright, so they need to
be manually retyped/adapted by a teacher (or licensed) rather than scraped
or reproduced by an AI — the Excel template and future upload tool are
built to make that easy once you have the source material.

## Phase 2 — email intake (point 13)

The in-browser upload above already solves the core need (teacher gets new
questions into the system) without needing the Blaze billing plan. You
decided to stay on Spark, so the literal "email the file" workflow is
parked as optional, not required:
- If you want it later without upgrading to Blaze: a free Google Apps
  Script (tied to any Gmail account, including hiufungchan366@gmail.com)
  can run on a timer, read new mail via the built-in `GmailApp` service,
  and push validated rows into Firestore using its REST API — no Cloud
  Functions, no billing account. More moving parts to set up (a script
  project, a Firestore service account) than the in-browser upload, so
  only worth building if you specifically want the email step back.
- If you outgrow Spark later (e.g. want real-time email import, server-side
  image generation, or scheduled digests), Cloud Functions on Blaze is the
  upgrade path — Blaze's free tier (2M function invocations/month) means
  actual cost stays near $0 at this project's scale even after upgrading.

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
