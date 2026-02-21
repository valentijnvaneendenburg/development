# Chromebook Guide — Can you build this fully online?

Short answer: **yes, you can build and run this project fully online on a Chromebook**. You do **not** need to buy a laptop first.

## Recommended approach (fully online)

Use this stack:

- **GitHub Codespaces** for development (VS Code in browser)
- **Neon or Supabase** for PostgreSQL
- **Vercel** for hosting/deployment
- **GitHub** for source control

This gives you a complete workflow without local installs.

## Why this works well on Chromebook

- No heavy local setup (Node, database, Prisma) required on device
- Same development environment every time
- Easy deploy path to production
- Good enough for your v1 scope (small user count and import volume)

## Step-by-step setup

1. Create a GitHub repo (e.g. `finance-planner`).
2. Open repo in **Codespaces** (`Code` -> `Codespaces` -> `Create codespace on main`).
3. In Codespaces terminal, bootstrap app:

```bash
npx create-next-app@latest . --typescript --eslint --app --src-dir --import-alias "@/*"
npm install prisma @prisma/client next-auth bcrypt zod csv-parse
npm install -D vitest tsx @types/bcrypt
npx prisma init
```

4. Create a Neon/Supabase Postgres database and copy connection string.
5. Add `.env` in Codespaces:

```env
DATABASE_URL="postgresql://USER:PASSWORD@HOST:5432/DB?sslmode=require"
NEXTAUTH_URL="http://localhost:3000"
NEXTAUTH_SECRET="replace-with-a-random-secret"
```

6. Build Prisma schema and run migration:

```bash
npx prisma migrate dev --name init
npx prisma generate
```

7. Push to GitHub and import to Vercel.
8. Add same env vars in Vercel and deploy.

## When a laptop is useful (but optional)

You might want a laptop later if you need:

- Much faster local builds/tests for bigger projects
- Multiple heavy apps running at once
- Better ergonomics (screen/keyboard) for long coding sessions

For your current project stage, Chromebook + cloud dev is a solid choice.

## Practical recommendation for you

- **Start now with Chromebook + Codespaces**.
- Re-evaluate hardware only after v1 is running and you feel productivity limits.
