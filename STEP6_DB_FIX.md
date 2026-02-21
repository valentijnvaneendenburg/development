# Step 6 DB Fix (Codespaces + Prisma P1001)

You should **proceed**, but not with a local Postgres container yet.

Best next step for your setup: use a managed Postgres (Neon/Supabase), then run migration.

## Why

- You already built everything correctly up to migration.
- Error `P1001` means Prisma cannot reach the database host in `DATABASE_URL`.
- In your summary, `DATABASE_URL` still had placeholders, so migration cannot work yet.

## Recommended path (now)

1. Create a database in Neon or Supabase.
2. Copy the real connection string (pooled URL is fine for app runtime; direct URL preferred for migrations if provider gives both).
3. Put the real value in `.env`:

```env
DATABASE_URL="postgresql://<user>:<password>@<host>:5432/<db>?sslmode=require"
```

4. Validate Prisma can read config:

```bash
npx prisma validate
```

5. Run migration:

```bash
npx prisma migrate dev --name init
npx prisma generate
```

6. If migration succeeds, run app:

```bash
npm run dev
```

## If P1001 still appears

Check these quickly:

- Connection string has no placeholders (`USER`, `PASSWORD`, `HOST`, `DB`).
- Database is active (not paused) in provider dashboard.
- `sslmode=require` is present.
- No accidental quotes/newlines in `.env`.
- If provider gives `DIRECT_URL`, add it for migrations:

```env
DATABASE_URL="...pooled..."
DIRECT_URL="...direct..."
```

and set in Prisma datasource when needed.

## Should you switch to SQLite?

Not recommended for your production path. Keep PostgreSQL now to avoid rework later.

## Should you run local Postgres container?

Optional fallback only. Since you want fully-online Chromebook workflow, managed Postgres is the cleanest path.
