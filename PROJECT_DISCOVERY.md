# Financial Planning Web App — v1 Product Brief (Finalized)

## 1) Confirmed v1 goals

Build a web-based personal finance app that lets you:

- Import ASN bank CSV transactions (1 account/file, 1 known format in v1).
- Label all transactions into custom categories.
- Exclude specific transactions from calculations.
- Create a **year overview** like your Excel:
  - Outgoing money per month per category.
  - Incoming money per month per category.
  - Totals and monthly surplus.
- Add planning inputs (expected income/expenses), including weekly pocket-money budgets.
- Produce a solid monthly/annual plan for next year.

## 2) Confirmed product decisions

- User model: personal-first (single user for v1, max small usage).
- CSV source: Dutch ASN export format.
- Import behavior: single file at a time, no duplicate handling needed in v1.
- Categories: unlimited, created from scratch (no prefilled category pack).
- Currency: EUR only.
- Editing: no complex historical editing flow in v1; use categorization/exclusion adjustments.
- Suggestions: suggest category based on previously labeled `Van / naar` + `Naam` matches.
- Planning logic: recurring future values can be filled for future months.
- Partner handling: one shared overview; separate weekly pocket-money categories allowed.
- Storage: keep uploaded files now; allow deletion later.
- Language: English UI.

## 3) ASN CSV format (v1 contract)

The parser should support this exact header structure in v1:

`Datum, Je rekening, Van / naar, Naam, Adres, Postcode, Woonplaats, Valuta saldo, Saldo voor boeking, Valuta, Bedrag bij / af, Verwerkingsdatum, Valutadatum, Code, Type, Volgnummer, Betalingskenmerk, Omschrijving, Afschriftnummer, Categorie`

### Required parsing rules

- Date fields use `DD-MM-YYYY`.
- Monetary values use comma decimal format (e.g. `-154,75`, `3930,89`).
- Direction comes from `Bedrag bij / af` sign:
  - `< 0` = outgoing
  - `> 0` = incoming
- Label suggestions primarily use:
  - `Van / naar` (counterparty account)
  - `Naam` (counterparty name)
- Imported `Categorie` from CSV can be used as initial label when present, but must remain editable.

## 4) Recommended beginner-friendly architecture (cheap + simple)

### Application stack
- **Next.js (TypeScript)** for frontend + backend API routes (single repo).
- **Prisma + PostgreSQL** for data.
- **NextAuth credentials** (email/password) + bcrypt.

### Hosting
- **Vercel** for web app hosting.
- **Neon or Supabase Postgres** for managed database.
- **Supabase Storage or Vercel Blob** for uploaded CSV retention.

This setup is low-maintenance and cost-effective for your expected v1 usage (~5 users, ~100 imports).

## 5) v1 data model

- `users`
  - id, email, password_hash, created_at
- `bank_imports`
  - id, user_id, filename, storage_path, imported_at
- `transactions`
  - id, user_id, import_id, booking_date, processing_date, value_date
  - counterparty_account, counterparty_name
  - description, amount_cents, direction
  - source_category_text, raw_row_json
- `categories`
  - id, user_id, name, kind (`income|expense|saving|pocket_money`), is_excluded_default
- `transaction_labels`
  - id, transaction_id, category_id, excluded, labeled_at
- `plans`
  - id, user_id, year, month, category_id, planned_amount_cents, is_recurring

## 6) Core v1 screens

1. Login (email/password)
2. CSV Import (upload -> parse preview -> confirm)
3. Transactions (filter unlabeled, set category, exclude/include)
4. Categories (create/edit unlimited categories)
5. Year Overview (income/outgoing per category per month, totals, surplus)
6. Planning (monthly plan + recurring options + weekly pocket money)
7. Basic Charts
   - Line: income vs expense vs saving per month
   - Bar: biggest spending categories

## 7) Calculation behavior (v1)

- **Actuals**: calculated from imported and labeled transactions.
- **Excluded transactions**: not counted in overview/planning totals.
- **Monthly surplus**: `income - expenses - planned_outflows` (with clear formula in UI tooltip).
- **Future months**:
  - if recurring planning is set, auto-fill planned values.
  - actual imported transactions (when available) should take priority in actuals views.

## 8) Delivery phases

### Phase 1 — Scaffold & Auth
- Next.js app, database schema, login.

### Phase 2 — CSV Import & Labeling
- ASN parser, import preview, transaction list, category assignment, exclusions.

### Phase 3 — Year Overview & Planning
- Excel-like monthly table, totals, monthly surplus, recurring planning inputs.

### Phase 4 — Suggestions & Charts
- Rule-based category suggestions from historical labels.
- Line and bar charts.

### Phase 5 — Hardening
- Better validations, backups, optional file deletion UX.

## 9) Immediate next implementation step

Start coding scaffolding with:

1. Next.js + Prisma + Postgres setup.
2. Initial schema migration for the tables above.
3. CSV upload endpoint + ASN parser utility with unit tests for:
   - Dutch decimal parsing.
   - Date parsing (`DD-MM-YYYY`).
   - Incoming/outgoing sign detection.
