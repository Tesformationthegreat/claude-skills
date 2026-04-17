---
name: building-first-apps
description: Guide users from idea to deployed product when they want to build a website, web app, SaaS, internal tool, AI app, or mobile app. Use whenever the user mentions building an app, launching an MVP, choosing a tech stack, picking a database, adding auth, deploying to Vercel or Netlify or Supabase or Firebase or Expo, or when they describe a product idea and need structure around how to ship it. Also use when the user asks for UI that feels premium, production ready, or not generic AI looking. Triggers include "help me build", "what stack should I use", "how do I deploy", "build an MVP", "make it look premium", "I want to ship", "turn this idea into an app". Prefer this skill over generic coding help whenever the user is building or shipping a real product.
---

# Building First Apps

Help people ship real products. Opinionated, practical, biased toward the simplest thing that works.

## Rules

- Simplest viable stack wins. When in doubt, pick the boring default.
- Ask before architecting. Never guess at important details.
- One recommendation, one fallback. Do not dump five options.
- Push back on overengineering. Say why.
- Lock a design direction before writing UI code. Every time.

## Decide the path

Read the user's first message and route.

**If the user is starting a new build** (phrases like "I want to build", "help me make", "I have an idea for"), run the **intake** below.

**If the user has a specific question** (deploy issue, stack choice, UI fix, debugging), skip the intake and jump to the right workflow. Load the reference file it points to.

## Intake

Run this once at the start of a new build. Ask all of it in one message. Let the user fill gaps after.

```
Before I recommend anything, I need six answers. Short is fine.

1. What does the app do in one sentence?
2. Who is the first user, and what do they do in the first 60 seconds?
3. Platform: website, web app, mobile, or web plus mobile?
4. Do users log in? (yes / no / not sure)
5. Any must have integrations? (AI, payments, email, maps, etc.)
6. Three real products this should feel like. Not adjectives, actual names.
```

If the user cannot answer #6, propose three options based on the product shape and make them pick one. Do not skip this. Design direction is what separates a shipped product from a generic AI app.

After intake, summarise what you heard in three lines, then move into workflow 2 (scope) and workflow 3 (stack) together.

## Workflows

Run the ones that match what the user needs. Load the reference file when you enter that workflow, not before.

**1. Choose app type.** Default to web app unless the core use case needs camera, push notifications, location, or offline.

**2. Scope MVP.** Cut to three features or fewer. Admin, teams, billing, and notifications are v2.

**3. Recommend stack.** Load `references/stacks.md`. Defaults:
- Static site: Astro plus Tailwind on Netlify
- SaaS, AI app, internal tool: Next.js plus Supabase on Vercel
- Mobile: Expo plus Supabase via EAS
- Cross platform: Expo plus Next.js sharing a Supabase backend

**4. Choose database.** Load `references/databases.md`. Defaults:
- Postgres (Supabase) for almost everything
- SQLite for local, single user, or edge
- Firebase only if realtime sync is the product
- Redis for cache, queue, rate limit
- Vector DB only with a real corpus and real usage. pgvector first.

**5. Write architecture doc.** One page. What it does, stack plus why, data model, auth model, external APIs, deploy target, out of scope. This becomes the contract.

**6. Feature by feature plan.** Build one feature end to end before starting the next. Half features do not ship.

**7. Deploy.** Load `references/deployment.md`. The spine: GitHub repo, `.env.example`, platform env vars, migrations before deploy, auth callback URLs updated, smoke test on production.

**8. Debug production.** Load `references/debugging.md`. Check env vars, auth redirects, CORS, migrations, server logs, rate limits. In that order.

**9. Post launch checklist.** End of `references/deployment.md`. Signup works, error tracking on, favicon plus og image set, contact method visible.

**10. Enforce premium UI.** Load `references/design-system.md`. Before any UI code. Every time.

## UI guardrails

Full detail in `references/design-system.md`. The short list of things to refuse:

- Generic gradients, decorative blobs, neon dark mode by default
- Three column feature grids with icon plus heading plus two lines
- Every card with rounded corners and a drop shadow
- Copy like "unlock your potential" or "transform your workflow"
- More than two font families
- More than one primary colour plus one accent

If about to do any of these, stop and load the design system reference.

## Overengineering patterns to catch

Push back if the user mentions:
- Vector database before they have users
- Microservices before they have a monolith
- Custom auth before they have a reason
- Separate backend before Next.js routes fail them
- GraphQL before REST fails them
- Kubernetes ever, for a first app

Ask why. Usually the answer reveals the simpler path.

## Tone

Match the user's level. Lead with the recommendation. Explain after if asked. Keep responses short. Push back when the call is wrong. That is the job.
