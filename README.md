# ToastMasters Timer

[Live Demo → ToastMasters Timer](https://techentangle.github.io/ToastMastersTimer/)

A modern Toastmasters meeting toolkit that keeps time, captures speaker rosters, and collects live audience feedback across devices using Supabase for real-time sync.

👉 **Live Demo:** https://techentangle.github.io/ToastMastersTimer/

## ⚡ Quick Start

1. **Open the timer:** Visit the [live demo](https://techentangle.github.io/ToastMastersTimer/)
2. **Enter speaker details:** Type name and topic (fields lock once timer starts)
3. **Start timing:** Click "Start" or press `Space` - timer shows in fullscreen with mini controls
4. **Control during speech:** Use floating buttons (⏸️ Pause, 🛑 End Talk, 🔊 Ding) or press `Space`/`B`
5. **End talk:** Click "End Talk" to log the speaker - fields unlock for next speaker
6. **Open ratings:** Once speakers are logged, click "Ratings Dashboard"
7. **Share QR code:** Attendees scan the QR to rate all speakers on their devices
8. **Watch live results:** Dashboard updates in real-time as votes come in (5-second refresh)
9. **Export data:** Click "Download CSV" to save the session log

## ✨ Key Features

### Timer & Controls
- **Bootstrap UI** – Clean, modern interface with responsive layout and session ID badge
- **Mini Control Bar** – Floating controls (Pause/End/Ding) visible during active timer
- **Locked Fields** – Speaker/topic display as read-only cards during session to prevent accidental edits
- **Role Presets** – One-click timing for Table Topics (1-2min), Evaluations (2-3min), Speeches (5-7min)
- **30s Grace Window** – Visual indicator and double-ding alert when speaker hits red time
- **Keyboard Shortcuts** – `Space` toggles start/pause, `B` plays ding sound
- **Speaker Log** – Auto-logs each talk with name, topic, time, and status (Under/Within/Over)

### Ratings & Collaboration
- **Supabase Sync** – Real-time data sync across all devices via Supabase backend
- **Ratings Dashboard** – Live leaderboard with average scores and vote counts
- **QR Code Voting** – Attendees scan to access multi-speaker rating form on their phones
- **Real-Time Updates** – Dashboard refreshes every 5 seconds plus instant WebSocket updates
- **Cross-Device** – Works on desktop, tablet, and mobile - all data stays synced
- **CSV Export** – Download complete session log for record-keeping

### Smart Session Management
- **Session IDs** – Auto-generated unique IDs for each meeting session
- **Auto-Cleanup** – Database trigger removes sessions older than 24 hours (on hosted demo)
- **Offline Fallback** – Works without Supabase using browser localStorage

## Pages in This Repo

| File | Purpose |
| ---- | ------- |
| `index.html` | Main timer and meeting console. Generates the Session ID and pushes the speaker roster to Supabase. |
| `ratings-dashboard.html` | Presenter-facing dashboard with leaderboard, always-on QR code, and Supabase-driven roster. |
| `multi-speaker-rating.html` | Audience voting form accessed via QR. Reads the roster from Supabase (or local snapshot) and submits ratings back to Supabase. |
| `favicon.ico` | Custom icon used by all pages. |

## 📖 Detailed Usage Guide

### Running the Timer (Main Page)

1. **Access the timer:**
   - Live: https://techentangle.github.io/ToastMastersTimer/
   - Local: Open `index.html` in any modern browser or run a local server (`python -m http.server`)

2. **Prepare for a speaker:**
   - Enter speaker name and topic in the form fields
   - Choose a role preset (Table Topics, Evaluation, Speech) or set custom green/yellow/red thresholds
   - Fields will lock once you start the timer to prevent accidental changes

3. **Time the speech:**
   - Click "Start" or press `Space` to begin
   - Timer displays fullscreen with color transitions (green → yellow → red)
   - **Mini control bar appears** at the top with quick-access buttons:
     - ⏸️ **Pause** (blue) - Stop the clock temporarily
     - 🛑 **End Talk** (red) - Stop and log the current speaker
     - 🔊 **Ding** (green) - Play alert sound manually
   - Press `Space` again to pause/resume
   - Press `B` to trigger ding sound
   - If grace window is enabled, see it displayed in the mini control bar

4. **End the talk:**
   - Click "End Talk" (requires confirmation) or press `Space` then click "End Talk"
   - Speaker is logged with final time and status (Under/Within/Over/Grace)
   - Fields unlock for next speaker entry
   - Session ID and roster sync to Supabase automatically

5. **View session log:**
   - Appears below the timer once you've logged at least one speaker
   - Shows all speakers with their times and status
   - Click "Download CSV" to export the complete session log

### Using the Ratings Dashboard

1. **Open the dashboard:**
   - Click "Ratings Dashboard" button on the timer page (visible after logging first speaker)
   - Or navigate to: `ratings-dashboard.html?session=YOUR_SESSION_ID`

2. **Share with attendees:**
   - QR code displays automatically in the header (always visible)
   - Attendees scan it to access the rating form on their devices
   - Session ID is shown for manual entry if needed

3. **Monitor votes:**
   - Leaderboard updates automatically as votes come in
   - Shows average rating and vote count for each speaker
   - Updates happen via WebSocket (instant) + 5-second polling (fallback)
   - Speakers appear as soon as they receive their first rating

4. **Session controls:**
   - Use "Share" buttons to copy rating link or session ID
   - Click "Back to Timer" to return to main page

### Rating Form (Audience View)

1. **Access via QR code or direct link:**
   - Scan QR from dashboard, or
   - Navigate to: `multi-speaker-rating.html?session=YOUR_SESSION_ID`

2. **Submit ratings:**
   - All logged speakers appear automatically
   - Rate each speaker 1-5 stars (1=Needs Work, 5=Outstanding)
   - Ratings are required for all speakers
   - Click "Submit All Ratings" when done

3. **After submission:**
   - Success message appears briefly
   - Page can be closed (votes are already recorded)
   - Submit another rating by refreshing or rescanning QR

### Important Notes about the Hosted Demo

- **Automatic Data Cleanup:** The public demo uses a shared Supabase instance with a database trigger that **automatically deletes sessions older than 24 hours**. This means rosters and ratings from previous meetings will disappear daily.
- **Visible Config:** The Supabase anon key is visible in `supabase-config.js` (standard for public demos). Anyone can inspect or submit data while a session is active.
- **Free Tier Limits:** The hosted demo runs on Supabase free tier - performance may vary with heavy concurrent usage.
- **For Production Use:** Consider setting up your own Supabase instance (see below) for permanent data storage and better control.

## 🔧 Using Your Own Supabase Instance

If you want full control, permanent data storage, or need to customize the setup:

### Step 1: Create Supabase Project

1. Sign up at [supabase.com](https://supabase.com) (free tier is fine)
2. Create a new project and note your **Project URL** and **anon/public key**
3. Go to **SQL Editor** in the Supabase dashboard

### Step 2: Create Database Tables

Run this SQL in the Supabase SQL Editor:

```sql
-- Sessions table (stores meeting metadata)
create table public.sessions (
  session_id text primary key,
  created_at timestamptz default now(),
  updated_at timestamptz default now(),
  roster_size integer default 0
);

-- Speakers table (stores speaker roster for each session)
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

-- Ratings table (stores audience votes)
create table public.ratings (
  session_id text references public.sessions(session_id) on delete cascade,
  speaker_id text,
  rating integer check (rating between 1 and 5),
  submitted_at timestamptz default now()
);

-- Index for faster ratings queries
create index ratings_session_id_idx on public.ratings(session_id);

-- Enable replica identity for realtime deletes (required for Supabase realtime)
alter table public.ratings replica identity full;
alter table public.speakers replica identity full;
alter table public.sessions replica identity full;
```

### Step 3: Configure Row Level Security (RLS)

Enable RLS and create permissive policies (adjust for your security needs):

```sql
-- Enable RLS on all tables
alter table public.sessions enable row level security;
alter table public.speakers enable row level security;
alter table public.ratings enable row level security;

-- Create permissive policies (allows all operations with anon key)
-- For production, consider more restrictive policies
create policy "sessions rw" on public.sessions for all using (true) with check (true);
create policy "speakers rw" on public.speakers for all using (true) with check (true);
create policy "ratings insert" on public.ratings for insert with check (true);
create policy "ratings select" on public.ratings for select using (true);
create policy "ratings delete" on public.ratings for delete using (true);
```

**Note:** If policies already exist, drop them first:
```sql
drop policy if exists "sessions rw" on public.sessions;
drop policy if exists "speakers rw" on public.speakers;
-- etc.
```

### Step 4: (Optional) Auto-Cleanup Old Sessions

To automatically delete sessions older than 24 hours (requires free tier or above):

```sql
-- Function to delete old sessions
create or replace function delete_old_sessions()
returns void language plpgsql as $$
begin
  delete from public.sessions
  where created_at < now() - interval '24 hours';
end;
$$;

-- Trigger to run cleanup after each session insert/update
create or replace trigger cleanup_old_sessions_trigger
after insert or update on public.sessions
for each statement
execute function delete_old_sessions();
```

**Alternative for paid tiers:** Use `pg_cron` extension for scheduled cleanup (not available on free tier).

### Step 5: Enable Realtime (for instant updates)

1. Go to **Database → Replication** in Supabase dashboard
2. Add tables to publication:
   - `public.sessions`
   - `public.speakers`
   - `public.ratings`
3. This enables WebSocket subscriptions for real-time updates

### Step 6: Update Application Config

Edit `supabase-config.js` with your project credentials:

```javascript
window.tmSupabaseConfig = {
  url: 'https://YOUR_PROJECT_ID.supabase.co',
  anonKey: 'YOUR_ANON_KEY'
};
```

**Find your credentials:**
- Supabase Dashboard → Project Settings → API
- Copy "Project URL" and "anon/public" key

### Step 7: Configure CORS (if deploying to custom domain)

1. Go to **Project Settings → API** in Supabase dashboard
2. Scroll to **Allowed Origins (CORS)**
3. Add your deployment domain(s):
   - `https://yourdomain.com`
   - `http://localhost:8000` (for local testing)
   - `https://your-username.github.io` (for GitHub Pages)

### Step 8: Deploy & Test

1. Commit your `supabase-config.js` changes
2. Deploy to your hosting (GitHub Pages, Netlify, Vercel, etc.)
3. Test the timer → log speakers → open dashboard → scan QR → submit ratings
4. Verify data appears in Supabase Dashboard → Table Editor

### Troubleshooting

- **CORS errors:** Make sure your domain is in Allowed Origins (Step 7)
- **"Could not find column" errors:** Verify all table columns were created (Step 2)
- **Realtime not working:** Check replication publication includes all tables (Step 5)
- **"Cannot delete" errors:** Ensure replica identity is set to FULL (Step 2)
- **RLS blocking requests:** Check policies allow operations for anon role (Step 3)

## 🛠️ Local Development

### Running Locally

The project is pure static HTML/JS/CSS with no build step required:

```bash
# Clone the repository
git clone https://github.com/TechEntangle/ToastMastersTimer.git
cd ToastMastersTimer

# Start a local web server (choose one):
python -m http.server 8000
# or
python3 -m http.server 8000
# or
npx http-server -p 8000

# Open in browser
# Navigate to http://localhost:8000
```

### URL Patterns

- **Timer:** `index.html` (main entry point)
- **Dashboard:** `ratings-dashboard.html?session=SESSION_ID`
- **Rating Form:** `multi-speaker-rating.html?session=SESSION_ID`

The session ID is auto-generated on the timer page and passed via URL parameters.

### How It Works

1. **Timer page** (`index.html`):
   - Generates unique session IDs
   - Logs speaker data to localStorage and Supabase
   - Provides controls for timing and session management

2. **Dashboard** (`ratings-dashboard.html`):
   - Fetches roster from Supabase using session ID
   - Subscribes to realtime rating updates via WebSocket
   - Falls back to 5-second polling if realtime fails
   - Generates QR code for easy mobile access

3. **Rating form** (`multi-speaker-rating.html`):
   - Loads roster from Supabase (or localStorage fallback)
   - Submits ratings to Supabase with foreign key retry logic
   - Handles offline scenarios gracefully

### Tech Stack

- **Frontend:** Vanilla JavaScript, Bootstrap 5, CSS3
- **Backend:** Supabase (PostgreSQL + Realtime WebSockets)
- **Storage:** Browser localStorage (offline fallback)
- **QR Codes:** `qrcodegen.js` library
- **No frameworks:** Pure HTML/JS for maximum portability

### Browser Compatibility

- Modern browsers with ES6+ support
- Chrome, Firefox, Safari, Edge (latest versions)
- Mobile browsers (iOS Safari, Chrome Mobile)
- Requires JavaScript enabled

## 📄 License

This project is licensed under the Apache License 2.0. See [`LICENSE`](LICENSE) for details.

---

**Created by Tushar Vartak** | [LinkedIn](https://www.linkedin.com/in/tushar-vartak/)