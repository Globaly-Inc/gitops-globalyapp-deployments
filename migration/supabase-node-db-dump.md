Yes — a few options, depending on what you need:

1. Full SQL dump via Supabase CLI (recommended)
This gives you a single `.sql` file with schema + data for the whole database, not table-by-table CSVs:

```bash
# Install once
npm i -g supabase

# Login & link to your project
supabase login
supabase link --project-ref some-random-secret

# Dump schema (DDL)
supabase db dump --schema public -f schema.sql

# Dump data only
supabase db dump --data-only -f data.sql

# Or both together with pg_dump via the CLI
supabase db dump --db-url "postgresql://postgres:[PASSWORD]@db.some-random-secret.supabase.co:5432/postgres" -f full.sql
```

You can restore the same file into any Postgres with `psql -f full.sql`.

2. Direct `pg_dump` (if you have the DB password from Lovable Cloud → Connect)
```bash
pg_dump "postgresql://postgres:[PASSWORD]@db.some-random-secret.supabase.co:5432/postgres" \
  --schema=public --no-owner --no-privileges -f globalyapp_full.sql
```
Add `-Fc` for a compressed custom-format dump (smaller, faster restore with `pg_restore`).

3. Supabase Dashboard → Database → Backups
Daily automated backups are kept on Pro+ plans and can be downloaded as a `.sql` dump — no scripting needed.

4. If you only want CSVs but in bulk, `pg_dump --data-only --inserts` or a small script wrapping `COPY (SELECT…) TO STDOUT CSV` per table is faster than doing each one manually.

Want me to generate a one-shot script that exports the full schema + data to `/mnt/documents/` for you?