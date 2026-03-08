# Product Requirements Document (PRD)
## Product: Givr
## Version: 1.0 (Planning Draft)
## Author: Product Team
## Date: 2026-03-08

---

## 1) Executive Summary
Givr is a managed marketplace that connects families and learners with vetted tutors and learning programs for in-home and online sessions. Inspired by the service model observed on Tuteria (high-trust positioning, exam-focused offerings, and managed tutor matching), Givr will prioritize measurable academic outcomes, fast onboarding, and high parent confidence.

The first release (MVP) will focus on:
- Parent/student onboarding and needs capture.
- Intelligent tutor matching and assisted assignment.
- Session booking, payments, and progress tracking.
- Exam-prep tracks and WhatsApp-assisted support.

---

## 2) Background & Opportunity
### Market Signals (from v2.tuteria.com)
The reference product emphasizes:
- Trust and social proof (thousands of parents, large tutor base).
- Multiple service lines (home tutoring, online, exam prep, language, study-abroad prep, premium tier).
- Outcome-driven messaging (success rates, score improvements, exam outcomes).
- High-touch conversion CTAs (Get Started, Book Tutor, Chat on WhatsApp).

### Opportunity for Givr
Givr can differentiate by combining:
1. Stronger productized progress visibility (goal plans + weekly measurable reports).
2. Faster matching SLA (e.g., tutor shortlist in < 24 hours in key cities).
3. Family-centric subscription options with transparent pricing.
4. Better retention loops through milestone-based nudges and parent dashboards.

---

## 3) Product Vision
"Help every learner find the right tutor quickly, learn consistently, and achieve measurable outcomes with full parent visibility."

---

## 4) Goals & Non-Goals
### Goals (MVP: first 6 months)
- Acquire first 1,000 registered families.
- Convert at least 20% of qualified leads to paid sessions.
- Achieve tutor matching within 24 hours for 80% of requests.
- Reach 70% month-2 learner retention.
- Deliver weekly progress reports for at least 90% of active learners.

### Non-Goals (MVP)
- Building a fully open tutor marketplace with no vetting.
- Launching all verticals at once (e.g., healthcare certifications, full study-abroad stack).
- Native mobile apps at launch (responsive web first).

---

## 5) Target Users & Personas
1. **Parent Planner (Primary Buyer)**
   - Ages 30–50, pays for child learning support.
   - Needs trusted tutors, predictable outcomes, minimal coordination stress.

2. **Exam-Focused Student (Primary End User)**
   - Secondary school to pre-university exam candidates.
   - Needs structured prep plans, strong accountability, high score outcomes.

3. **Young Adult Skill Learner (Secondary End User)**
   - Learns language, test prep, or professional courses.
   - Needs flexible schedule and online-first sessions.

4. **Professional Tutor (Supply Side)**
   - Wants high-quality leads, stable teaching opportunities, prompt payouts.

---

## 6) Problem Statements
- Families struggle to find trustworthy tutors with verified quality.
- Learners often lack individualized plans and progress accountability.
- Existing options are fragmented (discovery, booking, payment, and monitoring in separate channels).
- Tutor performance visibility is weak, causing low retention and low confidence.

---

## 7) Core Value Proposition
For families and learners, Givr offers a trusted end-to-end tutoring experience: quick needs assessment, vetted tutor matching, seamless booking/payment, and transparent progress tracking—leading to better outcomes with less effort.

---

## 8) Scope
### In Scope (MVP)
- Web landing and conversion funnels.
- Lead capture + learner needs assessment.
- Tutor profile management and vetting workflow (internal).
- Match recommendation engine (rules-based in MVP).
- Booking and schedule management.
- Payments (session bundles + subscriptions).
- Parent dashboard with progress reports.
- Notifications via email, SMS, and WhatsApp.
- Admin console for operations and quality assurance.

### Out of Scope (MVP)
- AI live classroom generation.
- Public tutor bidding/auction model.
- Multi-country tax and legal localization beyond initial launch region.

---

## 9) User Journey (Primary)
1. Parent lands on Givr homepage and selects learning need (home tutoring, online, exam prep, language).
2. Parent completes quick assessment form:
   - Student grade/level, subject/exam, location, preferred format, budget range, preferred times.
3. Givr creates request ticket and shows ETA for match.
4. Matching system + ops team shortlist 1–3 tutors.
5. Parent reviews tutor profiles and confirms preferred tutor.
6. Trial session is booked and paid.
7. After trial, parent confirms continuation package.
8. Tutor follows weekly plan; parent receives periodic progress updates.
9. Renewal prompt triggers at package completion with performance summary.

---

## 10) Functional Requirements
### 10.1 Marketing Website
- Dynamic service categories with dedicated pages.
- Outcome-focused social proof blocks (ratings, testimonials, success stats).
- Prominent CTAs: "Get Started", "Book Tutor", "Chat on WhatsApp".

### 10.2 Onboarding & Assessment
- Multi-step form with validation.
- Save-and-resume ability for incomplete intake.
- Auto-routing request by subject, location, and urgency.

### 10.3 Tutor Management
- Tutor application intake.
- Verification checklist (identity, credentials, demo lesson score).
- Tutor tags: subjects, level, mode (online/home), geography, price band.

### 10.4 Matching & Assignment
- Rule-based matching for MVP:
  - Hard filters: subject, level, location, availability.
  - Soft ranking: performance score, response speed, parent ratings.
- SLA tracker and escalation queue for unassigned requests.

### 10.5 Booking & Payments
- Calendar slots, timezone-aware.
- Trial session payments.
- Session package purchase (e.g., 8, 12, 24 sessions).
- Payment status and automated receipts.

### 10.6 Learning Operations
- Weekly lesson objectives set by tutor.
- Session attendance tracking.
- Parent progress report template (strengths, gaps, action plan).
- Flagging flow for no-shows and quality concerns.

### 10.7 Communication
- Automated event messages:
  - Match found
  - Session reminders
  - Missed class alerts
  - Progress updates
  - Renewal reminders
- WhatsApp handoff for high-intent leads.

### 10.8 Admin Console
- Lead pipeline board (new, matching, trial, active, churn risk).
- Tutor quality dashboard.
- Refunds/credits handling.
- Manual overrides for pricing/matching.

---

## 11) Non-Functional Requirements
- **Performance:** Core pages load under 2.5s on 4G.
- **Reliability:** 99.5% uptime for learner-facing app.
- **Security:** Role-based access, encrypted PII at rest and in transit.
- **Privacy:** Consent and data retention controls for student information.
- **Observability:** Full funnel and operations event logging.

---

## 12) Success Metrics (KPIs)
### Acquisition
- Landing page conversion rate.
- Cost per qualified lead.

### Activation
- % leads completing full assessment.
- Time from lead to tutor shortlist.

### Revenue
- Trial-to-paid conversion.
- Average revenue per learner (ARPU).
- Subscription/package renewal rate.

### Retention & Outcomes
- Month-1 and month-2 learner retention.
- Session completion rate.
- Parent NPS/CSAT.
- Exam-score improvement and goal attainment rate.

### Supply Health
- Tutor acceptance rate.
- Tutor response time.
- Tutor quality score distribution.

---

## 13) Assumptions & Risks
### Assumptions
- Families prefer managed matching over self-serve tutor search.
- WhatsApp remains a strong conversion/support channel.
- Exam-focused segments will drive early traction.

### Risks
- Supply-demand mismatch in specific subjects/locations.
- Tutor quality inconsistency impacting trust.
- Price sensitivity in key segments.

### Mitigations
- Start with priority cities and high-demand subjects.
- Enforce strict tutor onboarding and probation periods.
- Introduce tiered offerings (standard vs premium plans).

---

## 14) Go-to-Market (GTM) Plan
### Phase 1: Launch Wedge
- Focus categories: home tutoring + exam prep.
- Target segments: parents of JSS/SSS students and entrance-exam candidates.
- Channels: parent communities, school partnerships, referral loops, WhatsApp campaigns.

### Phase 2: Expansion
- Add language and professional test prep verticals.
- Launch corporate/expat tutoring packages.

### Phase 3: Premiumization
- "Givr Plus" with premium tutor pool and concierge learning management.

---

## 15) Rollout Plan /plan
### Sprint 0 (2 weeks) – Discovery & Foundations
- Finalize product scope and KPI baselines.
- Create UX wireframes for landing, intake, dashboard.
- Define tutor vetting rubric and operations playbook.

### Sprint 1 (2 weeks) – Lead Capture & Intake
- Build homepage and service pages.
- Implement multi-step learner needs form.
- Add CRM lead ingestion and admin pipeline views.

### Sprint 2 (2 weeks) – Tutor Ops & Matching
- Build tutor onboarding portal and verification workflow.
- Implement rules-based matching service and SLA monitoring.
- Add shortlist + parent confirmation workflow.

### Sprint 3 (2 weeks) – Booking & Payment
- Calendar scheduling and trial booking.
- Session packages and checkout integration.
- Notification templates for booking lifecycle.

### Sprint 4 (2 weeks) – Progress Tracking & Retention
- Tutor session logs + parent reports.
- Renewal automation and churn-risk flags.
- KPI dashboard for product/ops.

### Sprint 5 (2 weeks) – Hardening & Launch
- QA, analytics validation, load and security checks.
- Staff training for support/escalation SOPs.
- Public beta launch with controlled geography.

---

## 16) Open Questions
- Should Givr pricing be standardized by category or tutor-specific in MVP?
- How much of matching should be automated vs human-assisted at launch?
- Which single geography should be launch market #1?
- What minimum progress-report cadence is realistic for tutor compliance?
- Should trial sessions be paid, discounted, or free with deposit?

---

## 17) Appendix: Source Review Notes
This PRD was informed by reviewing publicly visible positioning and flows on:
- https://v2.tuteria.com/
- https://v2.tuteria.com/hometutors
- https://v2.tuteria.com/services

Key observed patterns used for planning include trust-led messaging, exam-focused service bundles, strong CTA placement, and high-touch chat-based support.
