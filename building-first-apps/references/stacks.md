# Stacks reference

Opinionated defaults per app type. Pick the default unless there is a real reason to deviate. "I saw a Twitter thread" is not a real reason.

## How to use this file

Identify the app type from workflow 1. Read only the relevant section. Present the recommendation to the user as a single choice with a one line why. Offer the fallback only if they push back.

## Static website

Use case: portfolio, personal site, landing page, docs site, blog, small business site.

**Default:** Astro plus Tailwind, deployed to Netlify or Vercel.

Why: Ships fast, near zero JS by default, great Lighthouse scores, easy to maintain. Markdown for content works out of the box.

**Fallback:** Plain HTML plus Tailwind if the user wants zero build step.

**Avoid:** Next.js for a pure static site. Overkill and slower to build than Astro.

## Marketing site with CMS

Use case: content driven site where a non developer updates copy and images.

**Default:** Next.js plus Sanity, deployed to Vercel.

Why: Sanity has a solid studio, real time preview, and structured content. Next.js handles ISR for fast updates without full rebuilds.

**Fallback:** Astro plus Contentlayer if the content team will edit in Markdown.

**Avoid:** WordPress unless the user explicitly asks for it. It is not wrong, it is just a different world.

## SaaS web app

Use case: users sign up, log in, do things, maybe pay.

**Default:** Next.js (App Router) plus Supabase plus Tailwind, deployed to Vercel.

Why: One tool gives you Postgres, auth, storage, row level security, and edge functions. Next.js on Vercel is the lowest friction deploy path in the industry right now. Tailwind keeps the UI velocity high if the design system is locked.

**Fallback:** SvelteKit plus Supabase if the user strongly prefers Svelte.

**Avoid:**
- Separate Express backend unless there is a specific reason
- Prisma plus a custom Postgres unless the user needs something Supabase cannot do
- Firebase for relational data (see databases reference)
- NextAuth plus custom Postgres plus Clerk plus anything. Pick one auth provider.

## Internal tool

Use case: tool only the company uses. Dashboards, admin panels, workflow tools.

**Default:** Next.js plus Supabase plus Tailwind, deployed to Vercel with a private deployment protection.

Why: Same stack as SaaS, no reason to use anything different. Gives you room to grow if the tool becomes a product later.

**Fallback:** Retool or similar no code tool if the user is non technical and the tool is truly throwaway.

**Avoid:** Overbuilding. Internal tools should be ugly and functional, not pretty and slow.

## AI app

Use case: app where the core value is an LLM doing something useful.

**Default:** Next.js plus Anthropic SDK plus Supabase, deployed to Vercel.

Why: Next.js server routes handle streaming responses cleanly. Supabase stores user data and auth. Anthropic SDK handles the Claude calls.

**Specific guidance:**
- Streaming is not optional. Static responses feel broken. Use the Vercel AI SDK or the raw Anthropic streaming API.
- Do not build a RAG system before you need one. If the user's docs fit in the context window, just put them in the context window.
- Do not pick a vector database before you have real usage. pgvector in Supabase handles the first thousand documents fine.
- Rate limit the API route. Users will abuse it. Use Upstash Redis or a simple token bucket.
- Log every prompt and response somewhere you can query. You will need this to debug and improve prompts.

**Fallback:** If the user wants a simpler setup and does not need auth, a single HTML file that calls the Anthropic API directly is valid for a prototype. Not for production.

## Mobile MVP

Use case: app that needs to live on a phone.

**Default:** Expo (React Native) plus Supabase, distributed via Expo Application Services.

Why: Expo removes 90% of the pain of React Native. Supabase gives you the same backend as web. EAS handles the build and store submission pipeline. You can ship to iOS and Android from one codebase.

**Fallback:** Flutter plus Supabase if the team prefers Dart or needs more native feel.

**Avoid:**
- Pure native (Swift or Kotlin) for an MVP unless there is a specific reason (gaming, heavy camera work, deep OS integration)
- React Native CLI (non Expo) for a first app. The pain is not worth it.
- Ionic or Capacitor as a primary choice. Fine for web to mobile wrappers, not great as a primary mobile stack.

## Cross platform (web plus mobile)

Use case: same product across web and mobile, shared backend.

**Default:** Expo for mobile plus Next.js for web, sharing a Supabase backend. Shared TypeScript types in a monorepo via Turborepo.

Why: Two codebases but one source of truth for data and business logic. Trying to share UI code across web and mobile is usually more pain than it saves at MVP stage.

**Fallback:** Expo Router and React Native for Web if the user really wants one codebase. Warn them the web output will feel less native than a real web app.

**Avoid:** Trying to ship both from day one. Ship one, validate, then add the other. Most apps do not need both.

## Stack guardrails

Before confirming any stack, check:

- Does the user know the languages involved? If not, is the learning curve acceptable?
- Is there a reason not to use the default? If not, use the default.
- Are there services (payment, email, SMS) that pin to a specific stack? Usually no, but worth checking.
- Is the user in a region where certain services have latency or compliance issues? Supabase has EU and US regions. Firebase is Google Cloud. Mention this if the user is outside North America.

## When to deviate from defaults

Real reasons to deviate:
- Team has deep expertise in a different stack
- Existing infrastructure the new app needs to fit into
- Specific compliance requirements (HIPAA, SOC 2, data residency)
- Performance requirements the default cannot meet
- Cost at scale, if the user has projected usage that would be expensive on the default

Not reasons to deviate:
- Trendy on Twitter
- A YouTuber recommended it
- "I want to learn X"
- Fear of vendor lock in when the app has zero users
