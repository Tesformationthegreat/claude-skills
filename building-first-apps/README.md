# building-first-apps

A Claude skill for going from idea to deployed product. Opinionated, practical, and biased toward the simplest stack that works.

## What it does

When you describe a new build, the skill runs a short intake, scopes the MVP, picks a stack and database, writes a one page architecture doc, plans the features, guides the deployment, and enforces a premium UI standard before writing any interface code.

When you have a specific question (fix a broken deploy, pick a database, make a UI feel less generic), it skips the intake and jumps to the right workflow.

## The intake

Every new build starts with six questions:

```
1. What does the app do in one sentence?
2. Who is the first user, and what do they do in the first 60 seconds?
3. Platform: website, web app, mobile, or web plus mobile?
4. Do users log in? (yes / no / not sure)
5. Any must have integrations? (AI, payments, email, maps, etc.)
6. Three real products this should feel like. Not adjectives, actual names.
```

Question 6 is the one that matters most. It is what prevents the generic AI look. If you say "clean and modern," the skill pushes back and asks for real product names. Linear, Stripe, Notion, Arc, Raycast, Apple. That reference set drives everything about the UI.

## Example

**You:** I want to build an app that helps freelancers track invoices.

**Claude:** Before I recommend anything, six quick answers:

1. What does the app do in one sentence?
2. Who is the first user, and what do they do in the first 60 seconds?
3. Platform: website, web app, mobile, or web plus mobile?
4. Do users log in?
5. Any must have integrations?
6. Three real products this should feel like.

**You:**
1. Track invoices, see who has paid, chase who has not
2. A freelancer who just sent three invoices this month. First 60 seconds: sign up, add two clients, log an invoice
3. Web app, mobile later
4. Yes
5. Stripe for payments eventually, email for reminders
6. Linear, Stripe Dashboard, Things 3

**Claude:** Got it. Here is the shape.

Stack: Next.js plus Supabase plus Tailwind, deployed to Vercel. Postgres for the data. Resend for email reminders.

MVP is three features: add client, log invoice, mark paid. That is it. Stripe integration is v2.

Design direction from your references: dense and keyboard friendly like Linear, quietly confident typography like Stripe Dashboard, restrained and soft edges like Things 3. Primary is a single accent colour, everything else neutral. No gradients.

Want me to write the one page architecture doc next, or start on the data model?

## What makes it different

Every recommendation is one choice with one fallback. The skill pushes back when you reach for a vector database, microservices, or custom auth before you need them. And it will not write UI code until you have named three real products to reference.

## Install

**Claude.ai**

1. Download `building-first-apps.skill` from this folder
2. In Claude.ai, go to Settings then Capabilities, turn on Code Execution
3. Then Customize then Skills, click the plus, upload the .skill file
4. Done. Just talk to Claude normally and the skill triggers when you describe a build

**Claude Code**

```bash
git clone https://github.com/Tesformationthegreat/claude-skills.git
cp -r claude-skills/building-first-apps ~/.claude/skills/building-first-apps
```

## Files

```
building-first-apps/
├── SKILL.md               Intake, workflows, guardrails
├── README.md              This file
└── references/
    ├── stacks.md          Per app type stack recommendations
    ├── databases.md       Database decision tree
    ├── deployment.md      Vercel, Netlify, Supabase, Firebase, Expo
    ├── design-system.md   Premium UI enforcement
    └── debugging.md       Top production issues and fixes
```

Each reference file only loads when the relevant workflow starts.

## Feedback

If this helps you ship something, a star on the repo helps others find it. Found a gap or want to suggest a workflow? Open an issue.

## License

MIT. Use it, fork it, ship with it.
