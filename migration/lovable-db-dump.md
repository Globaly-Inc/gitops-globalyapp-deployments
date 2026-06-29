Your data lives in Lovable Cloud (a managed Postgres backend). To dump everything via `pg_dump`/`psql`, you need the direct database connection string. Here's how to get it and use it:

1. Find your database credentials

Desktop:
1. Open your project → click the Cloud tab (top nav)
2. Go to Database in the Cloud sidebar
3. Click Connect (or look for "Connection string" / "Database settings")
4. You'll see:
   - Host: `db.some-random-secret.supabase.co`
   - Database: `postgres`
   - Port: `5432` (direct) or `6543` (pooler)
   - User: `postgres` (or `postgres.some-random-secret` for pooler)
   - Password: shown/reset there — copy it now, it's only fully visible once

Mobile: Tap `…` (bottom right) → Cloud → Database → Connect.

The full connection string looks like:
```
postgresql://postgres:[YOUR-PASSWORD]@db.some-random-secret.supabase.co:5432/postgres
```

2. Dump the whole database

Once you have the password, run locally (requires PostgreSQL client tools installed — `brew install postgresql` on Mac, or `apt install postgresql-client` on Linux):

```bash
# Schema + data, single file
pg_dump "postgresql://postgres:YOUR_PASSWORD@db.some-random-secret.supabase.co:5432/postgres" \
  --schema=public \
  --no-owner \
  --no-privileges \
  -f globalyos_dump.sql
```

Variants:
- `--schema-only` — structure only
- `--data-only` — rows only
- `-Fc -f dump.backup` — compressed custom format (restore with `pg_restore`)

3. Restore anywhere

```bash
psql "postgresql://user:pass@new-host:5432/dbname" -f globalyos_dump.sql
```

Easier alternative: Supabase CLI

If you don't want to install `pg_dump`:
```bash
npm i -g supabase
supabase login
supabase db dump --db-url "postgresql://postgres:YOUR_PASSWORD@db.some-random-secret.supabase.co:5432/postgres" -f full.sql
```

⚠️ Important: Treat the password like a root key — don't paste it into the codebase, `.env` checked into git, or chat. If you ever leak it, rotate it from the same Cloud → Database screen.

Want me to also generate a script that exports every table to individual CSVs in one shot as a backup option?