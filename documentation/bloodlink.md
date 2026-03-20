# BloodLink
## Real-Time Blood Donor Network

**Project Documentation**
Database Management Systems — Course Project
Version 1.0 | 2025

| Course | Domain |
|---|---|
| Database Management Systems | Healthcare / Emergency Services |

*Engineering Course — Project Based Learning*

---

## 01 — Problem Statement

### The Blood Supply Crisis

Blood is a resource that cannot be manufactured, stockpiled indefinitely, or substituted. Every year, millions of lives depend on the ability to source compatible blood within hours — sometimes minutes — of a medical emergency. Yet the current infrastructure governing blood donation and distribution in India and many other countries remains fragmented, manually operated, and dangerously slow.

### The Core Problem

There is no unified, real-time platform that:

- Maintains a verified, up-to-date database of eligible blood donors,
- Enables hospitals or blood banks to instantly reach out to compatible donors,
- Allows donors to actively confirm or decline their availability in real time, and
- Keeps all stakeholders — donors, hospitals, and coordinators — updated on the donation status throughout the entire lifecycle.

### Why Existing Solutions Fall Short

The current ecosystem relies on a patchwork of approaches, each with critical limitations:

| Existing Approach | Key Limitation |
|---|---|
| Phone-based outreach by hospitals | Manual, slow, no tracking; heavily dependent on personal contacts |
| WhatsApp / social media appeals | Unverified donors, no eligibility check, no structured response |
| Static donor registry databases | Not real-time; no active confirmation from donors; goes stale quickly |
| Blood bank stock management systems | Focused on inventory, not on connecting banks to live donors |
| Volunteer-run community groups | Unscalable, informal, no accountability or audit trail |

### The Consequences

These gaps translate into tangible human costs every day:

- Patients in critical condition wait hours while hospitals manually search for donors.
- Eligible donors are never reached because hospitals have no way to geo-filter a live donor pool.
- Donors who wish to help cannot discover active requests in their vicinity.
- No audit trail exists for donations, making it impossible to enforce cooldown periods or track donation history.
- Hospitals have no visibility into how many donors have been notified, who has responded, or what the real-time fulfillment status is.

> *"Every 2 seconds, someone in India needs blood. Only 1% of the population donates regularly. A real-time, verified donor network could bridge the gap between willing donors and urgent need."*

---

## 02 — Core Idea

### BloodLink — What We Are Building

BloodLink is a two-sided, real-time web platform that acts as the connective tissue between blood donors and hospitals or blood banks. At its heart, it is a request-response system: when a hospital has an urgent need, BloodLink instantly identifies eligible donors nearby, notifies them, and tracks their responses — closing the loop when a donation is confirmed.

### The Two Sides of the Platform

The platform serves two distinct user types with separate interfaces and workflows:

| Donors | Hospitals / Blood Banks |
|---|---|
| Register once with verified blood type and city | Register as a verified institution (admin-approved) |
| Toggle availability status week by week | Post blood requests with type, urgency, and deadline |
| Receive notifications for matching nearby requests | View real-time list of notified donors and responses |
| Accept or decline with one tap | Confirm donation completion to close a request |
| Track personal donation history and cooldown timer | Access analytics on fulfillment rate and response time |

### The Matching Engine

The core intelligence of BloodLink is its matching engine — a database query that, given a blood request, returns a ranked list of eligible donors. Eligibility is determined by three factors:

1. **Blood type** — Blood type compatibility: primary filter (exact type or universal donor O−).
2. **Proximity** — Geographic proximity: donors within a configurable radius (default 5 km, expandable to 15 km if needed).
3. **Eligibility** — Cooldown compliance: donor's last donation date must be at least 3 months ago.

Donors are ranked by proximity first, then by donation history (frequent donors are rewarded with higher priority). Notifications go out to the top N donors first; if there is no response within a set time window, the next batch is automatically notified — an escalation mechanism that requires no manual intervention.

---

## 03 — Core Features

### Donor-Facing Features

- Account registration with blood type, city, date of birth, and contact details.
- Identity and blood type verification flow (upload medical document or lab report).
- Weekly availability toggle — donors opt-in to be contactable during a given week.
- Push / SMS / email notification when a compatible request is raised nearby.
- One-tap accept or decline response to a request, with optional reason for decline.
- Personal donation history log with dates, hospitals, and blood units donated.
- Automatic cooldown enforcement — system marks the donor ineligible for 90 days post-donation.
- Gamification layer — badges and a points system for frequent donors (Bronze / Silver / Gold tiers).

### Hospital / Blood Bank Features

- Institution registration with name, address, license number, and contact person.
- Admin-gated approval — institutions become active only after admin verification.
- Blood request creation: specify blood type, units required, urgency level (Routine / Urgent / Critical), and fulfillment deadline.
- Real-time response dashboard — see which donors were notified, who has accepted, and current fulfillment count.
- Escalation control — manually trigger a wider search radius if initial pool is insufficient.
- Donation confirmation button — closes the request, updates donor history, and starts cooldown.
- Historical analytics — fulfillment rate, average time to first response, most-needed blood types.

### Admin Features

- Verify and approve hospital and blood bank registrations.
- View system-wide analytics: donor density by city, blood type distribution, underserved areas.
- Flag or deactivate suspicious or fraudulent accounts.
- Monitor real-time platform health — active requests, response rates, escalation frequency.

---

## 04 — Request Lifecycle

### From Request to Fulfilled Donation

Every blood request follows a structured lifecycle tracked entirely in the database. Each state transition is a database write, providing a full audit trail and enabling real-time status queries.

| Status | Triggered By | Updates To | What Happens |
|---|---|---|---|
| **POSTED** | Hospital | NOTIFIED | Request created in DB. Matching engine queries eligible donors. |
| **NOTIFIED** | System | ACCEPTED | Eligible donors receive push/SMS notification. Timer starts. |
| **ACCEPTED** | Donor | IN PROGRESS | Donor confirms intent. Hospital is alerted. Response is logged. |
| **IN PROGRESS** | System | FULFILLED | Donor is en route. Hospital dashboard shows confirmed donor details. |
| **FULFILLED** | Hospital | CLOSED | Donation confirmed. Donor history updated. 90-day cooldown begins. |
| **ESCALATED** | System (auto) | NOTIFIED | No response within time window. Next donor batch is notified. |
| **CANCELLED** | Hospital | CLOSED | Hospital cancels request (stock acquired elsewhere). Logged. |

### Escalation Logic

If no donor accepts within the configured response window (e.g., 20 minutes), the system automatically escalates. Escalation works in two dimensions:

- **Radius expansion** — the search radius increases from 5 km to 15 km, then to 25 km.
- **Batch expansion** — more donors per batch are notified simultaneously as urgency grows.
- **Priority escalation** — Critical-level requests bypass queuing and notify all eligible donors in the radius at once.

All escalation events are logged in the Notifications table for audit and analytics.

---

## 05 — Summary

### Project at a Glance

| Dimension | Detail |
|---|---|
| Problem | Fragmented, manual blood donor outreach with no real-time visibility |
| Solution | Verified two-sided platform: donors + hospitals, matched in real time |
| Core DB Tables | 7 primary tables: Users, Donors, Hospitals, BloodRequests, RequestResponses, DonationHistory, Notifications |
| Differentiators | Geo-matching, eligibility scoring, inventory module, gamification, analytics |
| Target Users | Blood donors (general public), hospitals, blood banks, platform admins |

---

*BloodLink is designed to be more than a course project. The problem it addresses is real, the architecture is production-grade in its thinking, and every design decision traces back to a demonstrable DBMS concept. The team is encouraged to implement the core request lifecycle and matching engine first, then layer in differentiating features as time permits.*

---

*— End of Document —*