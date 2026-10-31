# StudyFlow — Make your study count.

Production-ready static web app + Supabase backend. **No demo or seed data**: every account starts empty and all data is stored per-user in Postgres behind Row Level Security.

## Files
| File | Purpose |
|---|---|
| `index.html`, `app.js` | The app (no build step). `supabase-js` is loaded from jsDelivr, pinned to 2.45.4. |
| `config.js` | **Edit this**: your Supabase project URL + anon key. |
| `supabase/schema.sql` | Tables, constraints, indexes, signup trigger, RLS policies, challenge generator. |
| `vercel.json` / `netlify.toml` | Hosting config: security headers + Content-Security-Policy (allows `*.supabase.co`). |
| `manifest.webmanifest`, `favicon.svg`, `robots.txt` | Install to home screen + SEO. |

## Launch in 6 steps
1. Create a project at supabase.com.
2. **SQL Editor** → paste and run `supabase/schema.sql`.
3. **Authentication → Providers**: enable Email. **URL Configuration**: set *Site URL* to your deployed domain and add it to *Redirect URLs* (needed for confirmation + password-reset emails).
4. **Project Settings → API**: copy *Project URL* and the **anon public** key into `config.js`. Never use the `service_role` key.
5. Deploy: `npx vercel --prod` (Other / no build / output `.`), or drag the folder to Netlify Drop, or any static host.
6. Run the verification below on the live URL.

## Verify before announcing (two accounts, A and B)
- A signs up → onboarding → adds subject, task, runs a session (try pause, refresh mid-session), saves it.
- A saves one **Public**, one **Followers**, one **Private** session. B (not following A) should see only the public one; after B follows A, also the followers one; never the private one.
- B gives and removes kudos; A's task descriptions and reflections must never be visible to B (they live in owner-only tables).
- Join a challenge, finish a session, confirm progress and leaderboard update.
- Password reset email arrives and the new password works.
- Check on a phone: bottom nav, no horizontal scroll.

## How the important parts work
- **Timer:** stores a start timestamp + pause totals (also in localStorage), so it stays accurate through refresh, sleep and background tabs. The session UUID is generated client-side and saved with an upsert, so it can never be saved twice.
- **Privacy:** enforced in the database (RLS), not just the UI. Tasks and reflections are owner-only. Sessions follow public / followers / private.
- **Challenges:** weekly and monthly challenges are generated automatically by `ensure_standard_challenges()`. Progress is computed from your own saved sessions/tasks. Consistency challenges are included alongside volume ones.

## Known limitations (be upfront in release notes)
- No profile photo upload (initials avatar), no notifications feed, no account deletion UI.
- Challenge leaderboard shows top 5 by synced progress; no per-metric leaderboard switcher or "improvement vs baseline" ranking (personal records are shown instead).
- Progress syncs when the user is active in the app; it isn't recomputed server-side.
- Add a Privacy Policy and Terms page before collecting real users' emails.
