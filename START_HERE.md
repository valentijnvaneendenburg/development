# Start Here — Build the Financial Planning App (v1)

This checklist is the fastest way to go from idea to a working online MVP.

## 0) Prerequisites (install once)

- Node.js 20+
- npm 10+
- Git
- A GitHub account
- A Vercel account
- A Neon or Supabase account (PostgreSQL)

## 1) Create the project

```bash
npx create-next-app@latest finance-planner --typescript --eslint --app --src-dir --import-alias "@/*"
cd finance-planner
npm install prisma @prisma/client next-auth bcrypt zod csv-parse
npm install -D tsx vitest @types/bcrypt
npx prisma init
```

## 2) Configure environment variables

Create `.env` and add:

```env
DATABASE_URL="postgresql://USER:PASSWORD@HOST:5432/DB?sslmode=require"
NEXTAUTH_URL="http://localhost:3000"
NEXTAUTH_SECRET="replace-with-random-secret"
```

## 3) Add the initial database schema

Create these tables via Prisma models:

- `users`
- `bank_imports`
- `transactions`
- `categories`
- `transaction_labels`
- `plans`

(These are defined in the product brief.)

Then run:

```bash
npx prisma migrate dev --name init
npx prisma generate
```

## 4) Build the ASN CSV parser first

Implement a parser utility with tests for:

- `DD-MM-YYYY` date parsing
- Dutch decimals (e.g. `-154,75`)
- incoming/outgoing detection from `Bedrag bij / af`
- mapping of fields like `Van / naar`, `Naam`, `Omschrijving`, `Categorie`

Run tests:

```bash
npx vitest
```

## 5) Implement v1 screens in this order

1. Login (email/password)
2. CSV upload + parse preview + confirm import
3. Transactions table with:
   - category assignment
   - exclude/include toggle
   - filter unlabeled
4. Categories management (unlimited categories)
5. Year overview table (monthly by category, totals, surplus)
6. Planning screen (monthly + recurring + weekly pocket money)

## 6) Add category suggestions (simple rule-based)

Start with a deterministic strategy:

- exact match on `Van / naar`
- fallback: exact/normalized match on `Naam`
- show top suggestion + confidence label

## 7) Deploy online (cheap MVP)

1. Push code to GitHub.
2. Import project into Vercel.
3. Add environment variables in Vercel.
4. Attach managed Postgres (`DATABASE_URL`).
5. Deploy.

## 8) First definition of done (v1)

You are “done” when you can:

- log in
- upload ASN CSV
- label/exclude transactions
- view yearly monthly totals by category (in/out)
- enter planning values
- see monthly surplus

## 9) Your immediate next action (today)

Do this now in order:

1. Run the project creation commands in section 1.
2. Create a managed Postgres DB and set `DATABASE_URL`.
3. Implement Prisma schema + migration.
4. Build CSV parser + tests before UI polish.

Once this is done, you can send me your `schema.prisma` and I’ll review/fix it with you.

## Chromebook note

If you are on a Chromebook, you can do this fully online using **GitHub Codespaces** (browser IDE) + managed Postgres + Vercel. See `CHROMEBOOK_GUIDE.md` for the exact workflow.
