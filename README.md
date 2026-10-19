# LaunchPilot AI — Lead Automation Demo

A Next.js + Supabase lead capture and admin CRM demonstration. Includes server-side validation, honeypot, per-IP hourly throttling, Supabase email/password login, RLS-protected lead access and status changes.

## Requirements
- Node.js 20+ and npm
- Supabase project with Email authentication enabled
- Hosting that explicitly permits your intended commercial use

## Local setup
1. `npm install`
2. Copy `.env.example` to `.env.local` and fill the values from Supabase **Project Settings → API**. Keep `SUPABASE_SERVICE_ROLE_KEY` server-only. Generate `LEAD_IP_HASH_SECRET` with `openssl rand -hex 32` or another cryptographically secure random generator.
3. Open Supabase **SQL Editor** and run `supabase/migrations/001_init.sql` once.
4. Under **Authentication → Users**, manually create an admin email/password user (do not enable public signups for this demo).
5. In SQL Editor, grant admin access by replacing the email:
   ```sql
   insert into public.admins(user_id)
   select id from auth.users where email = 'YOUR_ADMIN_EMAIL@example.com'
   on conflict do nothing;
   ```
6. `npm run dev` then visit `http://localhost:3000` and submit the public form.
7. Visit `http://localhost:3000/login`, sign in with your admin account, then visit `/dashboard` to view leads and change statuses.
8. Run `npm test`, `npm run typecheck`, `npm run build`.

## Deployment
Set the same environment variables in your approved commercial hosting platform. Set `NEXT_PUBLIC_SITE_URL` to the final origin (e.g. `https://yourdomain.com`) with no trailing path. Enable HTTPS. Use a project dedicated to this demo, not an existing production database.

## Workflow
Visitor -> public form -> POST /api/leads -> Zod validation + honeypot -> hashed-IP atomic hourly rate limit -> lead insert -> admin login -> dashboard review -> status update.

## Security notes
- The service role key is only imported in a server-only module and never exposed to browser code. Never prefix it with `NEXT_PUBLIC_`.
- Public form uses a trusted-proxy `x-forwarded-for` header for rate limiting. **Production deployment must configure a trusted reverse proxy and must not accept untrusted client-supplied forwarding headers**. Without that configuration, this limit can be bypassed. Add managed CAPTCHA / bot protection and platform rate limiting before public production use.
- Browser Origin validation is not an authentication mechanism. No public browser access to leads is allowed by RLS.
- Admin registration is disabled in app code; create users in Supabase and grant privileges manually.
- The public form is a demonstration; implement a privacy notice, retention/deletion workflow, audit logging, backup strategy and consent appropriate to your jurisdiction before collecting real customer data.
- The RPC rate-limit table should be pruned by a scheduled DB job in production. Rate limit is five attempts per IP hash per hour.
- Email delivery and AI classification are deliberately not implemented. Never claim automatic follow-up or AI scoring is working.
- Dashboard lists only 100 newest leads. Add server-side pagination for large deployments.

## Testing
`npm test` runs validation tests. For integration testing, submit a lead, verify the dashboard, change status, verify unauthorized users cannot query the `leads` table, and attempt more than five submissions from one trusted IP within an hour.

## Pricing / hosting
Supabase and development tooling offer free tiers with restrictions. Vercel Hobby is not intended for commercial deployment; choose hosting permitted for the intended paid-client use.
