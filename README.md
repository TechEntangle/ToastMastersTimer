# ToastMasters Timer

[Live Demo → ToastMasters Timer](https://techentangle.github.io/ToastMastersTimer/)

A modern Toastmasters meeting toolkit that keeps time, captures speaker rosters, and collects live audience feedback across devices using Supabase for real-time sync.

👉 **Live Demo:** https://techentangle.github.io/ToastMastersTimer/

## Highlights

- **Polished Timer UI** – Bootstrap-styled single-page timer with large digits, responsive layout, and an inline “Session ID” badge for quick sharing.
- **Role Presets & 30s Grace Window** – One-click presets for Table Topics, Evaluations, and Speeches plus a visual grace-period indicator (with a double “ding”) so evaluators know when a talk still counts.
- **Speaker Log & CSV Export** – Each “End Talk” logs name, topic, total time, and status (Under / Within / Over). Download the full session as CSV at any time.
- **Supabase Sync** – Speaker rosters and audience ratings are stored in Supabase tables (`sessions`, `speakers`, `ratings`) so multiple devices stay in sync without sharing a browser window.
- **Ratings Dashboard** – A Bootstrap dashboard that shows the roster, a live-updating leaderboard, and a QR code pointing to the voting form.
- **QR-Based Rating Form** – Multi-speaker rating page supports remote attendees; votes appear instantly on the dashboard thanks to Supabase realtime plus a 3-second polling fallback.
- **Keyboard & Audio Enhancements** – Space toggles start/pause, “B” plays the cue ding, and the built-in audio is preloaded for consistent alerts.
- **LinkedIn Footer Credit** – Every page includes a subtle credit linking to Tushar Vartak’s profile.

## Pages in This Repo

| File | Purpose |
| ---- | ------- |
| `index.html` | Main timer and meeting console. Generates the Session ID and pushes the speaker roster to Supabase. |
| `ratings-dashboard.html` | Presenter-facing dashboard with leaderboard, always-on QR code, and Supabase-driven roster. |
| `multi-speaker-rating.html` | Audience voting form accessed via QR. Reads the roster from Supabase (or local snapshot) and submits ratings back to Supabase. |
| `favicon.ico` | Custom icon used by all pages. |

## Usage

1. Open the [hosted timer](https://techentangle.github.io/ToastMastersTimer/) (or clone and open `index.html` locally).
2. Enter optional speaker name/topic and choose a preset. The timer switches backgrounds as thresholds are crossed and shows the 30-second grace window when relevant.
3. Click **End Talk** to log the result. The roster is saved locally and pushed to Supabase for remote clients.
4. Once at least one speaker is logged, click **Ratings Dashboard**. The dashboard loads the roster and displays a QR code that points to the voting form.
5. Attendees scan the QR code. The `multi-speaker-rating.html` page fetches the roster, lets them rate each speaker (1–5 stars), and posts results to Supabase. Votes appear on the dashboard in near real time.
6. Use **Download CSV** on the timer page to export the full speaker log if you need to archive the meeting.

### Notes about the Hosted Demo

- The public demo uses a shared **Supabase anon key**. **Data is purged periodically** to keep the free instance tidy, so rosters and ratings from previous meetings will disappear.
- Because the demo runs on GitHub Pages, the Supabase config (`supabase-config.js`) is visible. This is normal for anon keys, but please be aware that anyone can inspect/submit data while the session is active.

## Using Your Own Supabase Project

If you want full control (or don’t want the hosted demo wiped out underneath you):

1. **Create a Supabase project** and note the URL and anon key.
2. **Create tables** (Postgres schema `public`):
   ```sql
   create table public.sessions (
     session_id text primary key,
     created_at timestamptz default now(),
     updated_at timestamptz default now(),
     roster_size integer default 0
   );

   create table public.speakers (
     session_id text references public.sessions(session_id) on delete cascade,
     speaker_id text,
     name text,
     topic text,
     total_time text,
     status text,
     order_index integer,
     inserted_at timestamptz default now(),
     primary key (session_id, speaker_id)
   );

   create table public.ratings (
     session_id text references public.sessions(session_id) on delete cascade,
     speaker_id text,
     rating integer check (rating between 1 and 5),
     submitted_at timestamptz default now()
   );

   create index ratings_session_id_idx on public.ratings(session_id);
   ```
3. **Enable Row Level Security** and permissive policies for the anon key (adjust to taste):
   ```sql
   alter table public.sessions enable row level security;
   alter table public.speakers enable row level security;
   alter table public.ratings enable row level security;

   create policy "sessions rw" on public.sessions for all using (true) with check (true);
   create policy "speakers rw" on public.speakers for all using (true) with check (true);
   create policy "ratings insert" on public.ratings for insert with check (true);
   create policy "ratings select" on public.ratings for select using (true);
   create policy "ratings delete" on public.ratings for delete using (true);
   ```
4. **Update `supabase-config.js`** with your project URL and anon key:
   ```javascript
   window.tmSupabaseConfig = {
     url: 'https://your-project.supabase.co',
     anonKey: 'your-anon-key'
   };
   ```
5. If you deploy on another domain, add that domain to **Supabase → Project Settings → API → Allowed Origins** so CORS succeeds.
6. (Optional) Implement your own cleanup routine. The hosted demo uses manual purges; automated deletion jobs require a paid tier or external tooling.

## Local Development

- The project is static HTML/JS/CSS; no build step is required. Any HTTP server (even `python -m http.server`) works.
- Favorites shortcuts:
  - `index.html` – timer console
  - `ratings-dashboard.html?session=<id>` – dashboard (auto-populates if opened from the timer)
  - `multi-speaker-rating.html?session=<id>` – voting form
- All Supabase calls are made through the browser using the anon key supplied in `supabase-config.js`.

## License

This project is licensed under the Apache License 2.0. See [`LICENSE`](LICENSE) for details.