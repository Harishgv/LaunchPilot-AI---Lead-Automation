# LaunchPilot AI — Lead Automation

Next.js + TypeScript + Supabase lead capture and management application.

## Source package
The application source was generated in ChatGPT as `launchpilot-ai-day1.zip`. Extract it and upload the contents of its `launchpilot-ai/` directory into the root of this repository (not the containing directory). Never upload `.env.local`, service-role keys, passwords, or other secrets.

## Setup
1. Install dependencies with `npm install`.
2. Create a Supabase project and execute `supabase/migrations/001_init.sql`.
3. Copy `.env.example` to `.env.local` and configure the environment variables privately.
4. Follow the source package's full README to create an authorized admin account.
5. Run `npm run typecheck`, `npm test`, `npm run build`, and `npm run dev`.
6. Deploy only after successful checks and configuration of the required Vercel environment variables.

**Status:** repository initialized; application files not yet uploaded. No live website is available. This README is not a working deployment.
