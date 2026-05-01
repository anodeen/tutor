# Growth Circle — Product & MVP Blueprint

## 1) Product Overview
Growth Circle is a private mastermind-style community platform where members gather to grow personally and professionally through structured plans, peer accountability, and admin-led guidance.

### Core Outcomes
- Help members set and achieve measurable goals.
- Build consistency with weekly check-ins, streaks, and accountability circles.
- Give admins clear tools to moderate quality, lead growth programs, and monitor engagement.

---

## 2) Suggested Technical Architecture

## 2.1 Stack
- **Frontend:** Next.js (React + TypeScript) + Tailwind CSS + shadcn/ui
- **Backend/API:** Next.js Route Handlers (or dedicated Express service if split later)
- **Auth:** NextAuth (Credentials + Google OAuth) with JWT session strategy
- **Database:** PostgreSQL + Prisma ORM
- **Realtime:** Socket.io (self-hosted) or Supabase Realtime for discussion updates and notifications
- **Background jobs:** BullMQ + Redis (for reminders, digests, streak calc)
- **File storage:** S3-compatible (profile images, resource attachments)
- **Deployment:** Vercel (app) + Railway/Render (Postgres + Redis)

## 2.2 High-Level Modules
- **Auth & Access Control** (RBAC: ADMIN, MEMBER)
- **Community & Content** (rooms, threads, comments, reactions)
- **Growth Engine** (plans, goals, check-ins, streaks)
- **Events** (schedule, RSVP, reminders, notes/resources)
- **Directory & Matching** (member profiles, interests, accountability partner requests)
- **Admin Console** (applications, moderation, analytics, audit log)
- **Notifications Service** (in-app + email digest)

## 2.3 System Design (logical)
- Client app calls authenticated API routes.
- API validates role/ownership, writes to Postgres via Prisma.
- Realtime events broadcast for room activity + notifications.
- Worker queue processes scheduled reminders and analytics aggregates.

---

## 3) Role-Based Access Model

## 3.1 Admin (Owner)
- Manage community settings and onboarding questions.
- Approve/reject applications.
- Create announcements, plans, weekly challenges, events.
- Moderate or remove posts/comments.
- View analytics and audit logs.

## 3.2 Member
- Apply to join.
- Maintain profile (bio, goals, interests).
- Participate in rooms/discussions.
- Create goals/milestones and progress updates.
- RSVP for events and join accountability groups.

---

## 4) Database Schema (PostgreSQL)

```sql
-- USERS & AUTH
users (
  id uuid pk,
  email text unique not null,
  password_hash text null,
  google_id text unique null,
  role text not null check (role in ('ADMIN','MEMBER')),
  status text not null check (status in ('ACTIVE','PENDING','SUSPENDED')),
  created_at timestamptz not null,
  updated_at timestamptz not null
)

profiles (
  user_id uuid pk references users(id),
  full_name text not null,
  avatar_url text,
  bio text,
  timezone text default 'UTC',
  interests text[],
  growth_focus text[],
  created_at timestamptz not null,
  updated_at timestamptz not null
)

member_applications (
  id uuid pk,
  email text not null,
  full_name text not null,
  why_join text not null,
  goals text,
  interests text[],
  status text not null check (status in ('SUBMITTED','APPROVED','REJECTED')),
  reviewed_by uuid references users(id),
  reviewed_at timestamptz,
  created_at timestamptz not null
)

-- COMMUNITY CONTENT
rooms (
  id uuid pk,
  name text not null,
  slug text unique not null,
  description text,
  is_private boolean default true,
  created_by uuid references users(id),
  created_at timestamptz not null
)

threads (
  id uuid pk,
  room_id uuid references rooms(id),
  author_id uuid references users(id),
  title text not null,
  body text not null,
  is_pinned boolean default false,
  is_locked boolean default false,
  moderation_state text default 'VISIBLE',
  created_at timestamptz not null,
  updated_at timestamptz not null
)

comments (
  id uuid pk,
  thread_id uuid references threads(id),
  author_id uuid references users(id),
  parent_comment_id uuid references comments(id),
  body text not null,
  moderation_state text default 'VISIBLE',
  created_at timestamptz not null,
  updated_at timestamptz not null
)

reactions (
  id uuid pk,
  user_id uuid references users(id),
  target_type text not null check (target_type in ('THREAD','COMMENT')),
  target_id uuid not null,
  emoji text not null,
  created_at timestamptz not null,
  unique(user_id, target_type, target_id, emoji)
)

announcements (
  id uuid pk,
  title text not null,
  message text not null,
  created_by uuid references users(id),
  created_at timestamptz not null
)

-- GROWTH PLANS & GOALS
growth_plans (
  id uuid pk,
  title text not null,
  description text,
  duration_days int,
  created_by uuid references users(id),
  starts_at date,
  ends_at date,
  created_at timestamptz not null
)

plan_steps (
  id uuid pk,
  plan_id uuid references growth_plans(id),
  title text not null,
  description text,
  week_number int,
  sort_order int,
  created_at timestamptz not null
)

member_plan_enrollments (
  id uuid pk,
  plan_id uuid references growth_plans(id),
  member_id uuid references users(id),
  status text check (status in ('ACTIVE','COMPLETED','DROPPED')),
  enrolled_at timestamptz not null,
  completed_at timestamptz
)

goals (
  id uuid pk,
  member_id uuid references users(id),
  title text not null,
  description text,
  metric_name text,
  target_value numeric,
  current_value numeric default 0,
  due_date date,
  status text check (status in ('NOT_STARTED','IN_PROGRESS','AT_RISK','DONE')),
  created_at timestamptz not null,
  updated_at timestamptz not null
)

check_ins (
  id uuid pk,
  member_id uuid references users(id),
  goal_id uuid references goals(id),
  week_start date not null,
  progress_note text,
  confidence_score int check (confidence_score between 1 and 5),
  submitted_at timestamptz not null,
  unique(member_id, goal_id, week_start)
)

streaks (
  member_id uuid pk references users(id),
  current_streak int default 0,
  longest_streak int default 0,
  last_check_in_date date
)

-- EVENTS
events (
  id uuid pk,
  title text not null,
  description text,
  starts_at timestamptz not null,
  ends_at timestamptz not null,
  meeting_url text,
  created_by uuid references users(id),
  created_at timestamptz not null
)

event_rsvps (
  id uuid pk,
  event_id uuid references events(id),
  member_id uuid references users(id),
  status text check (status in ('GOING','MAYBE','DECLINED')),
  created_at timestamptz not null,
  unique(event_id, member_id)
)

event_resources (
  id uuid pk,
  event_id uuid references events(id),
  title text,
  url text,
  notes text,
  created_at timestamptz not null
)

-- NETWORKING & DIRECTORY
accountability_requests (
  id uuid pk,
  requester_id uuid references users(id),
  target_id uuid references users(id),
  message text,
  status text check (status in ('PENDING','ACCEPTED','REJECTED','CANCELLED')),
  created_at timestamptz not null,
  updated_at timestamptz not null
)

-- ADMIN / NOTIFICATIONS / AUDIT
notifications (
  id uuid pk,
  user_id uuid references users(id),
  type text not null,
  title text not null,
  body text,
  action_url text,
  is_read boolean default false,
  created_at timestamptz not null
)

audit_logs (
  id uuid pk,
  actor_id uuid references users(id),
  action text not null,
  entity_type text not null,
  entity_id uuid,
  metadata jsonb,
  created_at timestamptz not null
)
```

---

## 5) API Endpoint List (MVP)

## 5.1 Auth
- `POST /api/auth/register`
- `POST /api/auth/login`
- `POST /api/auth/logout`
- `GET /api/auth/me`
- `GET /api/auth/google/callback`

## 5.2 Applications & Onboarding
- `POST /api/applications` (public)
- `GET /api/admin/applications` (admin)
- `PATCH /api/admin/applications/:id` approve/reject (admin)

## 5.3 Dashboard
- `GET /api/dashboard/member`
- `GET /api/dashboard/admin`

## 5.4 Rooms & Discussions
- `GET /api/rooms`
- `POST /api/rooms` (admin)
- `GET /api/rooms/:roomId/threads`
- `POST /api/rooms/:roomId/threads`
- `GET /api/threads/:threadId`
- `POST /api/threads/:threadId/comments`
- `POST /api/reactions`
- `PATCH /api/admin/threads/:id/moderate`
- `PATCH /api/admin/comments/:id/moderate`

## 5.5 Growth Plans & Goals
- `POST /api/admin/plans`
- `GET /api/plans`
- `POST /api/plans/:planId/enroll`
- `POST /api/goals`
- `PATCH /api/goals/:goalId`
- `POST /api/goals/:goalId/check-ins`
- `GET /api/goals/me`
- `GET /api/check-ins/me`

## 5.6 Events
- `POST /api/admin/events`
- `GET /api/events`
- `POST /api/events/:eventId/rsvp`
- `POST /api/admin/events/:eventId/resources`

## 5.7 Directory
- `GET /api/members?search=&interest=&goal=`
- `POST /api/accountability-requests`
- `PATCH /api/accountability-requests/:id`

## 5.8 Notifications
- `GET /api/notifications`
- `PATCH /api/notifications/:id/read`

## 5.9 Admin Analytics + Audit
- `GET /api/admin/analytics/overview`
- `GET /api/admin/analytics/engagement`
- `GET /api/admin/audit-logs`

---

## 6) Wireframe-Level UI Structure

## 6.1 Landing Page
- Hero: “Grow Better, Together.” + CTA buttons (Apply / Login)
- Feature blocks: Mastermind rooms, goal tracking, live sessions
- Testimonials/community values
- Footer with privacy, terms, contact

## 6.2 Login/Register
- Email/password form
- Google sign-in button
- Link to join application

## 6.3 Join Application
- Multi-step form: profile basics, growth goals, interests, motivation
- Submission success state + review timeline

## 6.4 Member Dashboard
- Welcome card + weekly focus
- Progress snapshot: active goals, streak, last check-in
- Upcoming events widget
- Announcement feed

## 6.5 Mastermind Rooms
- Left sidebar: rooms/topics
- Main feed: threads with filters (new, top, pinned)
- Thread detail with nested comments and reactions

## 6.6 Goal Tracker
- SMART goal cards with progress bars
- Add/update goal modal
- Weekly check-in timeline + streak badge

## 6.7 Events Calendar
- Calendar/list toggle
- Event detail drawer: description, meeting link, resources, RSVP

## 6.8 Member Directory
- Search + tag filters
- Member cards (bio snippet, interests, goals)
- Accountability partner request action

## 6.9 Admin Dashboard
- KPI cards: active members, weekly participation, completion rate
- Pending applications queue
- Moderation alerts
- Trend charts (engagement by week)

## 6.10 Admin Moderation Panel
- Reported content table
- Action buttons: hide, warn, remove, ban
- Audit history panel

## 6.11 Profile Settings
- Editable bio, interests, goals, timezone
- Notification preferences
- Security settings (password update, connected accounts)

---

## 7) MVP Implementation Plan

## Phase 1 (Foundation: Weeks 1–3)
- Project setup (Next.js, Tailwind, Prisma, Postgres)
- Auth + RBAC
- Join application flow + admin review
- Basic member/admin dashboards
- Initial seed data

## Phase 2 (Core Community: Weeks 4–7)
- Mastermind rooms, threads, comments, reactions
- Announcements
- Goal tracker (goals + progress updates + weekly check-ins)
- Notifications (in-app)
- Admin moderation actions + audit log

## Phase 3 (Engagement & Operations: Weeks 8–10)
- Events calendar + RSVP + reminders
- Member directory + accountability requests
- Admin analytics (participation, completion, actives)
- QA hardening, accessibility pass, launch checklist

---

## 8) Demo Seed Data (1 Admin + 5 Members)

## Users
1. **Admin**
   - Name: Ava Mentor
   - Email: `admin@growthcircle.app`
   - Role: ADMIN

2. **Member 1**
   - Name: Liam Builder
   - Email: `liam@growthcircle.app`
   - Interests: Business, Productivity
   - Goal: Launch MVP in 30 days

3. **Member 2**
   - Name: Sofia Clarity
   - Email: `sofia@growthcircle.app`
   - Interests: Mindset, Wellness
   - Goal: Daily journaling streak (60 days)

4. **Member 3**
   - Name: Noah Focus
   - Email: `noah@growthcircle.app`
   - Interests: Deep Work, Career
   - Goal: 2 portfolio projects in 8 weeks

5. **Member 4**
   - Name: Maya Motion
   - Email: `maya@growthcircle.app`
   - Interests: Health, Routines
   - Goal: 5 workouts/week for 12 weeks

6. **Member 5**
   - Name: Ethan Growth
   - Email: `ethan@growthcircle.app`
   - Interests: Leadership, Communication
   - Goal: Weekly speaking practice for 10 weeks

## Seed Rooms
- Business
- Mindset
- Health
- Productivity

## Seed Events
- Weekly Planning Sprint (every Monday)
- Midweek Accountability Call (every Wednesday)

---

## 9) Security & Compliance Baseline
- Passwords hashed with Argon2/Bcrypt.
- JWT/session rotation and HTTP-only cookies.
- Server-side authorization checks on every protected route.
- Rate limiting on auth/application endpoints.
- Input validation with Zod.
- Audit logs for admin actions.
- CSRF protection for state-changing requests.
- Basic WCAG-friendly UX: color contrast, keyboard nav, form labels.

---

## 10) Advanced Product Roadmap

## 10.1 Paid Memberships / Subscriptions
- Stripe integration for monthly/annual plans
- Tiered access (Core, Pro, Elite circles)
- Trial periods, promo codes, dunning and invoicing

## 10.2 AI Coach for Member Check-ins
- AI-generated weekly reflection prompts
- Personalized action suggestions based on goal history
- Risk scoring for drop-off + proactive nudges

## 10.3 Private 1:1 Rooms
- Member-to-member private rooms
- Shared goals/check-ins for accountability pairs
- Optional coach-led private pods

## 10.4 Additional Future Enhancements
- Native mobile app (React Native)
- Video snippets/asynchronous updates
- Community leaderboard and badges
- Integrations (Notion, Google Calendar, Slack)

---

## 11) Suggested Repository Structure (Implementation)

```txt
/apps
  /web (Next.js app)
/packages
  /ui
  /config
  /types
/services
  /worker (BullMQ jobs)
  /realtime (Socket gateway)
/prisma
  schema.prisma
  seed.ts
/docs
  architecture.md
  api-spec.md
```

This structure keeps frontend and backend cohesive for MVP while allowing modular growth toward service separation as traffic scales.
