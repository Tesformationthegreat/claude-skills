# Databases reference

Database choice is where most first time builders overengineer. Default to Postgres. Deviate only for real reasons.

## The one question that matters

Is the data relational? That is, does it have users, accounts, teams, items, orders, or anything where one record points at another record?

If yes, use Postgres. Stop reading. Go to workflow 5.

If you are not sure, it is probably relational. Use Postgres.

## The decision tree

**Use Postgres when:**
- The app has users and things users own
- Data has relationships (foreign keys)
- You want to ask complex questions (joins, aggregates, filters)
- You are building anything that looks like a SaaS, internal tool, or content platform
- You do not have a strong reason to pick something else

Default provider: Supabase. Alternative: Neon. Both give you managed Postgres with a solid developer experience.

**Use SQLite when:**
- The app is single user
- The app runs locally or on the edge
- It is a prototype you will throw away or migrate from later
- You are using Turso for distributed SQLite at the edge

SQLite is underrated for prototypes. A single file, zero setup, fast enough for the first thousand users of most apps.

**Use Firebase Firestore when:**
- Real time sync across clients is the core feature
- The data is document shaped (nested objects, not relational)
- The user is okay with Google Cloud vendor lock in
- Common good fits: chat apps, collaborative tools, live multiplayer

**Use MongoDB when:**
- The data is genuinely document shaped and will not become relational later
- The user has strong reasons (existing infra, team expertise)
- Honestly, it is rarely the right call for a first app. If the user asks for MongoDB, ask why. Usually the answer reveals Postgres is better.

**Use Redis when:**
- You need a cache (session store, API response cache)
- You need a queue (background jobs)
- You need a rate limiter
- You need realtime features (pub sub)

Redis is never the primary database. It sits next to Postgres. Default provider: Upstash for serverless friendly Redis.

**Use a vector database when:**
- The app needs semantic search or RAG over a corpus
- The corpus is larger than a few hundred documents
- Embeddings need to be queried at scale

Default: pgvector inside Supabase Postgres. You do not need Pinecone or Weaviate for the first ten thousand vectors. Postgres handles it fine.

If the user asks for a vector database before they have real usage, push back. Ask what they are retrieving and why. Nine times out of ten they do not need one yet. One time out of ten they do.

## Anti patterns to catch

**"I am going to use MongoDB because I know JavaScript."** MongoDB being JavaScript shaped is not a good enough reason. Postgres works fine with JavaScript.

**"I want to future proof with microservices and separate databases per service."** No. One Postgres. When you actually have scale problems, you will have the revenue to hire someone to fix it.

**"I want a graph database because the data is connected."** All data is connected. That is why foreign keys exist. Graph databases (Neo4j) are for specific problems like recommendation engines with deep traversal. Not for a social app MVP.

**"I need a vector database for my AI app."** Maybe. Ask how many documents. If under a few thousand, pgvector is fine. If under a few hundred, you probably do not need vectors at all, just stuff the content in the context window.

**"I want to use DynamoDB because it scales."** Unless the user is building at Amazon scale or already on AWS, this is premature. Postgres scales to millions of users for most apps.

## Storage

File storage is separate from database choice. Defaults:

- **Supabase Storage** if on Supabase. Integrated with auth and row level security.
- **Firebase Storage** if on Firebase.
- **Cloudflare R2** for cheap, S3 compatible storage when you want to decouple from the database provider.
- **AWS S3** if the user is already on AWS.

Do not put large files (images, videos, PDFs) in the database. Store a URL in the database, put the file in storage.

## Migrations

Use migrations from day one, even for solo projects. Changing the schema without migrations will burn you the first time you deploy to production.

- **Supabase:** use the Supabase CLI migration system or Drizzle
- **Firebase:** Firestore has no schema, so migrations are data transforms you run as scripts
- **Plain Postgres:** Drizzle, Prisma, or Kysely all have migration tools

Commit migration files to git. Run them in CI or via deploy hooks, not manually in production.

## Summary table

| Situation | Pick |
|---|---|
| Anything with users and relational data | Postgres (Supabase default) |
| Local prototype or edge app | SQLite (Turso for edge) |
| Realtime sync is the product | Firebase Firestore |
| Genuinely document shaped data | MongoDB (rare) |
| Cache, session, queue, rate limit | Redis (Upstash) |
| Semantic search over big corpus | pgvector first, dedicated vector DB only at scale |
| File storage | Supabase Storage or R2 |
