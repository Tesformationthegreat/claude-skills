# Deployment reference

The gap between "it works on my machine" and "users can use it" is where most first apps die. This file is the checklist.

## The universal spine

Every deployment follows these steps. The platform changes, the spine does not.

1. Git repo on GitHub from day one. No exceptions.
2. `.env.example` file committed. Real `.env` never committed. Add to `.gitignore`.
3. Environment variables configured in the deploy platform, not in code.
4. Database migrations run before the app starts, via a deploy hook or CI step.
5. Auth callback URLs updated for the production domain.
6. A real domain (custom or platform subdomain) with HTTPS.
7. Smoke test the production deploy. Sign up, do the core flow, sign out, sign in. Before sharing.
8. Error tracking configured. Sentry, Axiom, or at minimum server logs you will actually check.

## Vercel (Next.js, Astro, static sites)

Default path for anything JavaScript based.

**Setup:**
- Connect GitHub repo to Vercel
- Framework preset is auto detected
- Add environment variables in the Vercel dashboard before the first deploy
- Set Node version explicitly if the project uses a specific one

**Environment variables:**
- Set for Production, Preview, and Development separately
- Never use production secrets in Preview. Create a separate Supabase project or database for Preview if needed.
- Variables prefixed with `NEXT_PUBLIC_` are exposed to the browser. Everything else stays server side.

**Custom domain:**
- Add in Vercel dashboard, update DNS (CNAME for subdomains, A record for root)
- HTTPS auto provisioned
- Update auth callback URLs in Supabase or your auth provider immediately after

**Common issues:**
- Build works locally, fails on Vercel: usually a missing env var or a case sensitive import path (Linux vs macOS filesystem)
- Runtime crashes: check Vercel function logs, not build logs
- API route timing out: default timeout is 10s on hobby, 60s on pro. For long running tasks, use background jobs or streaming
- Environment variable not updating: redeploy after adding or changing vars. Vercel does not hot swap them.

## Netlify (static sites, simple apps)

Default path for static sites. Also fine for simple full stack.

**Setup:**
- Connect GitHub repo
- Set build command and publish directory (auto detected for common frameworks)
- Add environment variables before first deploy

**Forms and functions:**
- Netlify Forms is the easiest way to handle contact forms on a static site
- Netlify Functions for serverless endpoints, similar to Vercel

**Common issues:**
- Build fails with Node version error: set `NODE_VERSION` in environment variables or `.nvmrc`
- Functions not deploying: check they are in the correct directory (`netlify/functions` by default)
- Redirects not working: check `netlify.toml` or `_redirects` file

## Supabase (database, auth, storage)

Not a deployment target for the app, but the backend needs its own deploy checklist.

**Setup:**
- Create a new project for production, separate from local dev
- Run migrations via Supabase CLI: `supabase db push`
- Configure auth providers in the dashboard (email, Google, GitHub, etc.)
- Set site URL and redirect URLs to match the production domain
- Enable Row Level Security on every table with user data. Every table. No exceptions.

**Auth callback URLs:**
- Site URL: `https://yourdomain.com`
- Redirect URLs: `https://yourdomain.com/auth/callback`, plus any preview domains
- Forgetting this is the number one reason auth "works locally but not in production"

**Row Level Security (RLS):**
- Every table with user data needs policies
- Default deny, then add policies that allow specific access
- Test RLS by running queries as an anonymous user in the SQL editor

**Common issues:**
- "Auth works locally but not in production": redirect URL not updated
- "User can see other users' data": RLS not enabled or policy too permissive
- "Migration failed": usually a syntax error or a constraint that fails against existing data. Run against a staging copy first.

## Firebase (if chosen)

**Setup:**
- Create Firebase project
- Enable the services you need (Firestore, Auth, Storage, Hosting)
- Install Firebase CLI: `npm install -g firebase-tools`
- `firebase init` and select the services

**Deploy:**
- `firebase deploy` pushes everything
- `firebase deploy --only hosting` or `--only functions` for targeted deploys

**Security rules:**
- Firestore security rules are critical. Default test rules allow anyone to read and write. Fix this before launch.
- Test rules in the Firebase console's rules playground

**Common issues:**
- Hitting free tier limits: Firestore reads and writes add up fast. Monitor usage.
- Cold starts on Cloud Functions: first request after idle is slow. Use minimum instances on paid plan if this matters.
- Auth redirect mismatch: update authorized domains in Firebase Auth settings

## Expo (mobile apps)

**Setup:**
- Expo account and EAS CLI: `npm install -g eas-cli`
- `eas build:configure` in the project
- Apple Developer account ($99/year) for iOS
- Google Play Developer account ($25 one time) for Android

**Builds:**
- Development build: for testing with Expo Go or custom dev client
- Preview build: internal testing, TestFlight or Google Play Internal Testing
- Production build: for App Store and Play Store submission

**Environment variables:**
- Use `eas.json` to define env vars per build profile
- Public values can go in `app.config.js`
- Secrets use `eas secret:create`

**Store submission:**
- `eas submit` handles both stores
- First submission requires more setup (screenshots, descriptions, app icon, review info)
- Budget two weeks for first App Store review. Google Play is faster.

**Common issues:**
- Build fails on iOS: usually a provisioning or bundle ID issue. EAS handles most of this automatically.
- App crashes on launch in production but works in dev: check that all env vars are set in the production build profile
- Push notifications not working: Expo Push requires separate setup per platform, including APNs keys for iOS

## The pre launch checklist

Run through this before sharing the link with anyone who matters.

- [ ] Production domain configured with HTTPS
- [ ] All env vars set for production, none are dev values
- [ ] Database migrations run against production
- [ ] Auth redirect URLs updated for production domain
- [ ] Row Level Security enabled on every user data table (if Supabase)
- [ ] Security rules configured (if Firebase)
- [ ] Signup flow tested end to end on production
- [ ] Core feature tested end to end on production
- [ ] Signup flow tested on mobile browser
- [ ] Error tracking installed and receiving events
- [ ] Analytics installed
- [ ] Favicon set
- [ ] OG image set (twitter:image and og:image meta tags)
- [ ] Meta description set
- [ ] 404 page exists and does not leak debug info
- [ ] Contact method visible somewhere (email, form, Twitter)
- [ ] No API keys or secrets in the repo or client side code

Skipping any of these is a choice. Make it consciously.

## Debugging a broken deploy

If the deploy succeeded but the app is broken, check in this order:

1. Open the deployed site in an incognito window. Rule out cache issues.
2. Open the browser console. Look for errors.
3. Open the network tab. Look for 500s, 404s, and CORS errors.
4. Check server logs on the platform (Vercel Logs, Netlify Functions, etc.)
5. Check the database: is the data there? Are the permissions right?
6. Check environment variables: is everything set, and set correctly?
7. Compare production to local: what is different?

If the deploy itself failed, the build logs tell you why. Read them top to bottom before Googling the error.
