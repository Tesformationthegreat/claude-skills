# Debugging reference

When things break in production, the user is stressed. Get them to a fix fast. Start with the most likely cause, not the most interesting one.

## The debug workflow

Run these in order. Do not skip.

1. **Classify the problem.** Is it build, runtime, auth, database, API, UI, deployment, webhook, or quota? Quick taxonomy:
   - **Build:** compile errors, missing imports, env vars at build time
   - **Runtime:** server logs, network tab, SSR failures
   - **Auth:** callbacks, allowed origins, session handling
   - **Database:** migrations, RLS or security rules, connection string
   - **API:** status codes, CORS, request shape
   - **Webhook:** signature verification, raw body handling, secret mismatch
   - **Deployment:** env vars, DNS, SSL, platform config
   - **Quota:** rate limits, free tier caps, billing

   Say which before proceeding.
2. **Get four facts.** If the user has not given them, ask.
   - Exact error text, not paraphrased
   - Where it appears (browser console, server log, build log, terminal)
   - What action triggered it
   - What changed recently
3. **State the most likely cause.** One sentence.
4. **Give three checks in priority order.** Three, not ten.
5. **If still broken, ask for one specific artifact.** A log line, a screenshot, a config file. Not "send me everything."
6. **Do not suggest a rewrite until the current failure is isolated.** Fix the broken path first. Refactors come later. Turning a bug into an architecture rewrite is a classic AI failure mode. Resist it.

## Response format

When debugging, reply in this order:

1. Likely cause
2. Why it is likely
3. Exact checks to run now, in order
4. What to send back if still broken

Keep the first reply short. Do not dump ten possible causes unless the evidence is genuinely weak. Decisive guidance beats comprehensive guidance when someone is blocked.

## When to stop debugging and roll back

If the broken flow is revenue critical (signup, login, checkout, payments, data write paths), stop trying to fix forward. Bias toward rollback or temporary disablement first, debug after.

Options in order of preference:
1. Revert the last deploy on the platform (Vercel and Netlify have one-click rollback)
2. Feature flag the broken path off if a flag exists
3. Put up a maintenance page for the affected route only
4. Only then, dig into the root cause

Losing five minutes of uptime to a rollback beats losing an hour of conversions to a live bug. Tell the user this when the flow they describe is money or trust critical.

## Pre-diagnosis triage

Three questions settle most issues before you read the error.

1. Did it ever work in production, or is this the first deploy?
2. What changed between the last working version and now?
3. Does it work locally?

**Never worked in production, works locally:** environment, config, or deploy settings. Almost always.

**Stopped working after a change:** roll back the change mentally and test that hypothesis first.

**Does not work locally either:** the bug is in the code, not the deploy. Debug locally first.

**Before blaming production specifically:** try to reproduce with production parity. Pull production env vars into a local `.env.production.local`, run against the production database in read-only mode if possible, or deploy to a preview branch and test there. Many "production only" bugs are actually reproducible and just were not tested that way.

## Top production bugs

These cover 90% of first deploy issues. Check in order.

### 1. Missing or wrong environment variable

**Symptom:** App loads but a specific feature (auth, API calls, database) fails. Or app crashes on startup.

**Check:**
- Every var in `.env.example` is set in production
- Production vars are production values, not dev values (classic trap: still pointing at the dev Supabase project)
- Preview deploys are using the right vars. Preview should not use production secrets, but it also should not be missing them entirely.
- `NEXT_PUBLIC_` or similar public prefixes are correct
- The deploy was triggered after the var was added. Most platforms do not hot swap.

### 2. Auth redirect URL still pointing at localhost

**Symptom:** Login works locally, fails on production. Redirects to localhost after OAuth or magic link.

**Check:**
- Supabase: Site URL and Redirect URLs in Auth settings
- Firebase: Authorized domains in Auth settings
- OAuth providers (Google, GitHub): redirect URIs include production domain
- Any hardcoded callback URLs in code

### 3. CORS blocking the API call

**Symptom:** Network tab shows the request failing with a CORS error. Usually only on production or a different domain.

**Check:**
- If calling your own API, the API route should allow the frontend origin
- If calling a third party API from the browser, route the call through a server proxy
- Next.js API routes: set CORS headers explicitly if calling from a different origin

### 4. Database migration not run on production

**Symptom:** Table does not exist. Column does not exist. Relation error.

**Check:**
- Migrations have been pushed to the production database
- Migrations ran without error (check migration logs or the migrations table)
- The app is pointing at the production database, not a stale one

### 5. Build succeeded but runtime crashed

**Symptom:** Deploy shows green but the app does not load. Or a specific page crashes.

**Check:**
- Platform logs (Vercel Logs, Netlify Functions logs), not build logs
- Browser console for client side errors
- Network tab for failing requests

### 6. Rate limit or quota hit on a third party API

**Symptom:** Feature works for a while, then stops. Errors like 429 or 503.

**Check:**
- Anthropic, OpenAI, or other AI API quotas
- Supabase free tier limits
- Email provider limits (Resend, SendGrid)
- Add rate limiting on your side if users are spamming

### 7. Case sensitive filesystem

**Symptom:** Import works locally on macOS or Windows but fails to build on Linux (Vercel, Netlify).

**Check:**
- Import paths match file names exactly, including case
- `./Components/Button` vs `./components/Button` is a different file on Linux

### 8. Missing or misconfigured access policies

**Symptom:** User can see other users' data. Or user cannot see their own data. Or storage uploads fail silently. Or file URLs return 403.

**Check:**
- Supabase RLS: enabled on the table, policies use `auth.uid()` correctly
- Supabase Storage: bucket policies exist, public vs private setting matches intent
- Firebase Firestore rules exist and are not the default test rules
- Firebase Storage rules cover read and write separately
- Default deny is the baseline. Add specific allow policies. Test as an anonymous user before shipping.

### 9. Stale cache or CDN

**Symptom:** Old version of the site still showing. Users see different things.

**Check:**
- Hard refresh (Cmd+Shift+R)
- Incognito window
- CDN cache (Vercel usually handles this, but custom caches may not)
- Service worker (if PWA) caching old assets

### 10. Edge runtime vs Node runtime mismatch

**Symptom:** Works locally, fails on deploy with "module not found" or "function is not defined."

**Check:**
- Next.js route using a Node only module (fs, crypto) but configured for edge runtime
- Switch to Node runtime: `export const runtime = 'nodejs'`

### 11. Webhook signature verification failing

**Symptom:** Webhook endpoint returns 400. Stripe, Supabase auth hooks, Resend, or similar refuse to deliver or mark the event as failed.

**Check:**
- Webhook secret matches the one in the provider dashboard (different secrets for test mode and live mode in Stripe)
- Request body is being read raw, not parsed as JSON before verification. Next.js App Router: use `req.text()`. Express: use `express.raw()` on the webhook route.
- The secret is set in production, not just local
- Clock skew on the server is not causing timestamp verification to fail

### 12. Server component using a browser-only API

**Symptom:** Build succeeds but page throws "window is not defined" or "document is not defined" at runtime or during SSR.

**Check:**
- Next.js App Router: add `"use client"` to components that use `window`, `document`, `localStorage`, or browser-only libraries
- Dynamic imports with `ssr: false` for components that cannot be server rendered
- `useEffect` for side effects that touch browser APIs
- Check library docs: some (charting, maps) require client-only wrappers

### 13. DNS or SSL not propagated on new custom domain

**Symptom:** Domain shows "not secure" or the browser cannot reach the site. Or `www.domain.com` works but `domain.com` does not (or vice versa).

**Check:**
- DNS records are correct (A record for apex, CNAME for subdomains)
- DNS has propagated (can take up to 48 hours, usually minutes). Use `dig` or `whatsmydns.net` to verify.
- SSL certificate has been issued by the platform (Vercel, Netlify do this automatically after DNS verifies)
- Both `www` and apex are configured, with a redirect from one to the other
- Auth providers and any hardcoded URLs updated to match the final canonical domain

## Reading logs

Platform logs have a pattern. Read them top to bottom. First error is usually the root cause. Subsequent errors are often symptoms.

- **Vercel:** Project → Logs → filter by function or deployment
- **Netlify:** Site → Deploys → click a deploy → Deploy log and Function log
- **Supabase:** Project → Logs → filter by API, Auth, Database, Storage
- **Firebase:** Firebase Console → Functions → Logs

## Escalation

Some problems are not worth debugging alone. Tell the user to escalate when:
- The error is internal to the platform and not in user code
- The stack trace points at library internals
- The issue is intermittent with no clear reproduction
- The user has spent more than an hour on it

Where to escalate:
- Vercel: Discord, or support if on paid plan
- Supabase: Discord, or support ticket
- Firebase: Stack Overflow with firebase tag, or Google Cloud support on paid plan
- Expo: Discord or Expo forums
- Anthropic: support@anthropic.com for API issues
- Stripe: Dashboard support, they respond fast

Paste the exact error into Google before debugging from scratch. Stack Overflow still beats most LLMs for specific error messages with a long history.
