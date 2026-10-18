# JayTeam CS2 — Mobile PWA MVP

Mobile-first bilingual (RU/EN) team site with public news, match results, roster, and Supabase-backed manager area.

## Included
- Responsive black / acid-lime design
- Russian / English toggle
- PWA manifest and service-worker support
- Supabase Auth email/password sign-in
- Owner/manager roles checked in the database
- Row Level Security (RLS) policies for news, matches, roster, and roles
- Admin CRUD for news, match results, and player profiles

## Requirements
- Node.js 20+ recommended
- A free Supabase account
- A free static hosting account (for example, Cloudflare Pages or Netlify)

## 1. Create the database
1. Create a project at https://supabase.com/.
2. Open **SQL Editor** in the Supabase dashboard.
3. Paste and run all of `supabase/schema.sql`.
4. Open **Project Settings → API** (or the project's Connect/API panel) and copy the project URL and publishable/anon key.

## 2. Create the owner account
1. In Supabase, open **Authentication → Users**.
2. Create the owner account with an email you control and a strong unique password. Keep public sign-ups disabled.
3. Copy that user's UUID.
4. In SQL Editor run this, replacing the UUID:
   ```sql
   insert into public.user_roles (user_id, role)
   values ('PASTE-OWNER-USER-UUID-HERE', 'owner');
   ```
5. For each trusted manager, create their Auth user in the dashboard and add a role:
   ```sql
   insert into public.user_roles (user_id, role)
   values ('PASTE-MANAGER-USER-UUID-HERE', 'manager');
   ```
   Only the owner should assign roles. Never expose the Supabase `service_role` secret in this app.

## 3. Configure and run locally
1. Install Node.js from https://nodejs.org/.
2. Copy `.env.example` to `.env`.
3. Put your Supabase project URL and publishable/anon key in `.env`:
   ```env
   VITE_SUPABASE_URL=https://YOUR_PROJECT_ID.supabase.co
   VITE_SUPABASE_ANON_KEY=YOUR_SUPABASE_PUBLISHABLE_OR_ANON_KEY
   ```
4. In a terminal, from this folder:
   ```bash
   npm install
   npm run dev
   ```
5. Open the local URL printed by Vite. Without Supabase configured, the public site renders empty states in demo mode; admin content needs the database.

## 4. Build and test production output
```bash
npm run build
npm run preview
```
The built site is created in `dist/`.

## 5. Free deployment (static hosting)
Choose one provider and follow its current dashboard instructions:
- Cloudflare Pages: https://pages.cloudflare.com/
- Netlify: https://www.netlify.com/

Recommended Git-based flow:
1. Create a GitHub repository and upload this project (do not upload `.env`).
2. Import the repository into your host.
3. Build command: `npm run build`
4. Output directory: `dist`
5. Add these environment variables in the host's project settings:
   - `VITE_SUPABASE_URL`
   - `VITE_SUPABASE_ANON_KEY`
6. Deploy, then open the generated HTTPS URL on your phone.
7. For installable PWA behavior, use HTTPS. On supported mobile browsers, choose **Add to Home Screen** / **Install app**.

If the host builds from Git, configure the environment variables before the first build. A Vite `VITE_*` value is embedded in the client bundle; that is expected for the Supabase publishable/anon key, but never put secret keys there.

## 6. Configure Supabase Auth URLs
In Supabase **Authentication → URL Configuration**:
- Set the Site URL to your deployed HTTPS URL.
- Add both local development and deployed URLs to Redirect URLs if required by the project's Auth configuration.
- Keep public sign-ups disabled; create accounts yourself from the dashboard.
- Use a unique password and turn on MFA for the owner account if available.

## 7. Using the admin area
1. Open the site and click the login icon.
2. Sign in with an owner/manager account created in Supabase.
3. Open **Admin**.
4. Use the News, Matches, and Roster tabs to add/edit/delete entries.
5. For news, fill Russian and/or English fields; the UI falls back to the other language if one is blank.
6. Use absolute `https://` links for images, FACEIT profiles, and match links. Image upload storage is not included in this zero-budget MVP.

## Security notes
- The anon/publishable key is designed to be public. RLS policies protect the database.
- Never use the Supabase `service_role` key in browser code or commit secrets.
- The `user_roles` table does not allow users to read other users' role rows. Role checks happen through a `SECURITY DEFINER` function.
- Keep sign-ups disabled and only create trusted manager accounts.
- Test access with a logged-out browser: drafts should not appear, and write operations should be denied.
- This is an MVP, not a security audit. Review policies and enable MFA before using it for a larger organization.

## Current limitations
- No automatic FACEIT match sync
- No push notifications
- No image uploads (external HTTPS image URLs only)
- No tournament API integration
- News detail is displayed inline on the news page rather than using individual article URLs
- No custom domain required; add one later if budget allows

## Project layout
```
src/
  App.jsx
  main.jsx
  styles.css
  lib/supabase.js
supabase/
  schema.sql
public/
  jt.svg
.env.example
vite.config.js
```
