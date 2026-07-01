
### Migration tasks
```
## user-identification
SELECT current_user, session_user;
```


```
Created token : sbp_some_random_rext_hai_ta

Login to Pod :
kubectl exec -it -n globalyapp-web-server deployments/api-migrater-globaly-os-deployment – sh

Migration : 
supabase login --token sbp_some_random_rext_hai_ta
Initialize
supabase init
supabase init --force
Project connect
supabase link --project-ref some_random_proj_id

Migrate 
supabase db push
supabase db push –debug

## for custom migration on database

#### funtion : https://supabase.com/docs/guides/database/connecting-to-postgres#supavisor-session-mode
supabase db push --db-url "postgresql://postgres.mugduohzhqkhbembbjjr:PASSWORD@aws-1-ap-southeast-1.pooler.supabase.com:5432/postgres"

### pooler-mode
postgres://postgres.apbkobhfnmcqqzqeeqss:[YOUR-PASSWORD]@aws-0-[REGION].pooler.supabase.com:5432/postgres

## for deploy
supabase login --token sbp_some_random_rext_hai_ta
supabase functions deploy ably-auth --project-ref mugduohzhqkhbembbjjr
```



```bash
supabase secrets set --project-ref mugduohzhqkhbembbjjr \
  RESEND_API_KEY=re_... \
  FROM_EMAIL=noreply@globalyapp.com \
  ABLY_API_KEY=xxxxx \
  OPENAI_API_KEY=sk-... \
  STRIPE_SECRET_KEY=sk_live_...
  # (one line per secret, or use --env-file if CLI supports it)

## migrate using file
supabase secrets set \
  --project-ref "mugduohzhqkhbembbjjr" \
  --env-file    "secrets.env"
```


superadmin user creation and checkup

```sql
-- 1. Create your profile first
INSERT INTO public.profiles (id, email, full_name)
SELECT id, email, COALESCE(raw_user_meta_data->>'full_name', email)
FROM auth.users
WHERE email = 'subash.chaudhary@globalyhub.com'
ON CONFLICT (id) DO NOTHING;

-- 2. Now grant super_admin directly from auth.users (bypass profiles)
INSERT INTO public.user_roles (user_id, role)
SELECT id, 'super_admin'::app_role
FROM auth.users
WHERE email = 'subash.chaudhary@globalyhub.com'
ON CONFLICT (user_id, role) DO NOTHING;


```

```sql

curl -X POST "https://mugduohzhqkhbembbjjr.supabase.co/auth/v1/signup"   -H "apikey: sb_publishable_e6mjdaTlsoOUBKK4Eia1Ow_N3OE7bNX"   -H "Content-Type: application/json"   -d '{                                                                                                                                 "email": "subash.chaudhary@globalyhub.com",
    "password": "YourPasswordHere123!"
  }'


-- ① Insert profile using the exact user ID from signup
INSERT INTO public.profiles (id, email, full_name)
VALUES (
  'd15dc074-6786-4889-ad0d-ca94a72ab88e',
  'subash.chaudhary@globalyhub.com',
  'Subash Chaudhary'
)
ON CONFLICT (id) DO NOTHING;

-- ② Grant super_admin using the exact user ID
INSERT INTO public.user_roles (user_id, role)
VALUES (
  'd15dc074-6786-4889-ad0d-ca94a72ab88e',
  'super_admin'::app_role
)
ON CONFLICT (user_id, role) DO NOTHING;

-- ③ Verify
SELECT 
  au.id,
  au.email,
  p.full_name,
  ur.role
FROM auth.users au
LEFT JOIN public.profiles p ON p.id = au.id
LEFT JOIN public.user_roles ur ON ur.user_id = au.id
WHERE au.id = 'd15dc074-6786-4889-ad0d-ca94a72ab88e';

-- Manually confirm the user via SQL:
UPDATE auth.users 
SET 
  email_confirmed_at = now()
WHERE id = 'd15dc074-6786-4889-ad0d-ca94a72ab88e';

curl -X POST "https://mugduohzhqkhbembbjjr.supabase.co/auth/v1/token?grant_type=password" \
  -H "apikey: sb_publishable_e6mjdaTlsoOUBKK4Eia1Ow_N3OE7bNX" \
  -H "Content-Type: application/json" \
  -d '{
    "email": "subash.chaudhary@globalyhub.com",
    "password": "YourPasswordHere123!"
  }'


```


```bash
-- Check if ANY users exist
SELECT count(*) as total_users FROM auth.users;

-- Check if ANY profiles exist  
SELECT count(*) as total_profiles FROM public.profiles;

-- Check if ANY roles exist
SELECT count(*) as total_roles FROM public.user_roles;
```
