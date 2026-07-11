# Team Board — Backend Build Plan

## Goal

Give every agent a "Team" tab where they can see how the whole team is pacing — Behind Pace / On Pace / Crushing It, kanban-style — built on real shared data instead of each agent's own browser storage.

## Tech stack

- **Supabase** — hosted Postgres database with built-in login/auth and live updates. Free tier comfortably covers a team this size (500MB database, 50k monthly active users). No server to stand up or maintain.
- **Netlify** — stays as the host for the site itself. No serverless functions needed for the MVP — the browser talks directly to Supabase, and Postgres security rules (Row Level Security) enforce who can read/write what.
- **Frontend** — same single-file, no-build-step style as the current sheet, extended with a login screen and a new Team tab.

## Data model

```sql
-- One row per agent (extends Supabase's built-in auth user)
create table agents (
  id uuid primary key references auth.users(id),
  full_name text not null,
  team_id uuid not null default 'default-team',
  role text not null default 'agent', -- 'agent' | 'manager'
  created_at timestamptz default now()
);

-- One row per agent per week — mirrors what's in localStorage today
create table weekly_records (
  id uuid primary key default gen_random_uuid(),
  agent_id uuid references agents(id) not null,
  iso_week text not null,              -- e.g. '2026-W28'
  metrics jsonb not null default '{}', -- { callsMade: {actual, weeklyGoal, locked}, ... }
  combo jsonb not null default '{}',   -- { weeklyGoal, locked } for the conversations goal
  lead_gen jsonb not null default '{}',-- { mon: [...], tue: [...], ... }
  updated_at timestamptz default now(),
  unique (agent_id, iso_week)
);

-- One row per agent per month — the locked monthly goals
create table monthly_goals (
  id uuid primary key default gen_random_uuid(),
  agent_id uuid references agents(id) not null,
  year_month text not null,            -- '2026-07'
  metrics jsonb not null default '{}', -- { leadsGenerated: {goal, locked}, ... }
  combo jsonb not null default '{}',   -- { goal, locked }
  updated_at timestamptz default now(),
  unique (agent_id, year_month)
);
```

**Security rules (Row Level Security):**
- Every table restricts INSERT/UPDATE to `agent_id = auth.uid()` — an agent can only ever write their own numbers.
- SELECT is open to everyone on the same `team_id` — this is what makes the Team tab work, since every agent needs to read every other agent's current numbers.

## Phase 0 — Decisions (confirmed)

1. **Visibility**: ✅ Full team-wide visibility — every agent can see every other agent's status on the Team board, not just managers.
2. **Account creation**: ✅ Admin-invited — you or a manager sends each agent an invite email from the Supabase dashboard. No public signup form.
3. **Team scope**: ✅ Single team — everyone using the tool is on one team and sees each other on the board. No multi-office grouping needed.

## Phased implementation plan

**Phase 1 — Backend setup** (~0.5–1 day)
- Create the Supabase project, run the schema + security rules above.
- Configure auth (email/password or magic-link) and the invite flow.

**Phase 2 — Login & identity** (~1 day)
- Add a login screen to the site.
- Replace the free-text "Agent Name" field with the logged-in user's identity (fixes today's biggest fragility — a typo in the name field currently scatters an agent's history into a new, empty bucket).
- Build a one-time "import your existing data" step: on first login, read whatever's sitting in that browser's localStorage and push it into Supabase, so nobody loses their current week/month.

**Phase 3 — Rewire the data layer** (~1–2 days)
- Swap the existing `saveRecord` / `loadRecord` / `saveMonthlyGoalRecord` functions (currently reading/writing `localStorage`) over to Supabase calls instead.
- All existing UI and logic — steppers, goal-locking, the pacing caution math — stays exactly as-is; only where the data lives changes.
- Add basic loading/error handling, since network calls can lag or fail in a way localStorage never did.

**Phase 4 — Team board (new tab)** (~1–2 days)
- New view that pulls every teammate's current-week and current-month numbers.
- Runs the same pacing formulas already built into the sheet to sort each agent into Behind Pace / On Pace / Crushing It.
- Renders as kanban columns by status, each agent as a card showing the agreed 3–4 metrics (Two-Way Conversations pace %, lead-gen days met, closings this month, etc.).
- Live updates via Supabase's realtime subscriptions, or a simple manual refresh button if you'd rather keep it simpler.

**Phase 5 — Testing & rollout** (~0.5–1 day)
- Test with a handful of real agent accounts.
- Verify the data-import step preserves everyone's current numbers.
- Update the Agent/Management instructions to reflect logging in instead of typing a name.

**Total estimate: roughly 4–7 working days** of focused build time, depending on how much live-update polish you want on the Team tab.

## Ongoing cost & upkeep

- Supabase free tier should comfortably cover a small-to-mid-size team; paid tier (~$25/month) only becomes relevant if you outgrow it or want automatic backups.
- Someone needs to invite/manage agent accounts — this is a few clicks in the Supabase dashboard, no code involved.

## What doesn't change

The actual Weekly Goal Sheet experience — the steppers, the locked goals, the pacing caution, the Lead Generation tracker, the flashing X/flame — stays exactly the same for each agent. This build only changes *where the data lives* and *adds* the Team tab on top.
