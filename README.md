# 🌿 Este & Edo — Carpool App

Wedding carpool coordinator for **Este & Edo**, 27 Giugno 2026 @ Antico Convento i Cappuccini, Montalcino.

---

## Setup in 3 steps

### Step 1 — Create Supabase project (free, takes 2 min)

1. Go to [supabase.com](https://supabase.com) → **New project**
2. Name it `esteedo-carpool` (or anything you like)
3. Once created, go to **SQL Editor** and run this:

```sql
create table offers (
  id           bigserial primary key,
  name         text not null,
  phone        text not null,
  city         text not null,
  time         text not null,
  seats        integer not null default 2,
  note         text,
  dir          text not null check (dir in ('go','return')),
  created_at   timestamptz default now()
);

-- Allow anyone to read and insert (no login required)
alter table offers enable row level security;

create policy "Anyone can read offers"
  on offers for select using (true);

create policy "Anyone can insert offers"
  on offers for insert with check (true);
```

4. Go to **Settings → API** and copy:
   - **Project URL** → looks like `https://xxxx.supabase.co`
   - **anon / public key** → long string starting with `eyJ...`

### Step 2 — Add your keys to index.html

Open `index.html` and replace lines near the top of the `<script>`:

```js
const SUPABASE_URL = 'https://xxxx.supabase.co';       // ← your URL
const SUPABASE_ANON_KEY = 'eyJhbGciOi...';             // ← your anon key
```

### Step 3 — Deploy to GitHub Pages

```bash
git init
git add .
git commit -m "Initial carpool app"
git remote add origin https://github.com/YOUR_USERNAME/esteedo-carpool.git
git push -u origin main
```

Then in GitHub → repo **Settings** → **Pages** → set source to `main` branch → **Save**.

Your URL will be: `https://YOUR_USERNAME.github.io/esteedo-carpool`

---

## How it works

| Role | What they do |
|------|-------------|
| **Driver** | Opens the link → "Offro passaggio" → fills in name, phone, city, time, seats |
| **Rider** | Opens the link → "Cerco passaggio" → picks city + time window → sees matching drivers |
| **Contact reveal** | Phone numbers are hidden by default — only visible after clicking "Mostra contatto" |

### Privacy model
- **No login required** — just a shared link
- **Phone numbers are never listed publicly** — only revealed after an active search
- The "Tutti" tab shows all drivers but hides contact details (only visible after a matching search)
- Data is stored in your own Supabase project — Anthropic/third parties have no access

### City matching logic
Nearby cities and airports are grouped:
- Firenze ↔ FLR (Florence Airport) ↔ PSA (Pisa Airport)
- Bologna ↔ BLQ (Bologna Airport)
- Torino / Milano are their own groups

### Adding the wedding location
The destination (Montalcino) is fixed and the same for everyone — so it's intentionally left out of the form. If you want to collect it, just add a "Punto di arrivo" field to the offer form.

---

## File structure

```
index.html   ← entire app, single file, no build step
README.md    ← this file
```

## Customisation

- **Names / date / venue** → edit the `<header class="hero">` section in `index.html`
- **Cities list** → edit the `<optgroup>` sections and `CITY_GROUPS` object in the JS
- **Language** → all text is in Italian, easy to change inline
- **Colors** → CSS variables at the top of `<style>`: `--olive`, `--terra`, `--sand` etc.
