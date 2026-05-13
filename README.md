# KidStreet — Parent Portal
## Business Requirements Document (BRD)
### Version 1.0 · Phase 1 (MVP) + Phase 2 (Future) · May 2026

---

## Document Control

| Field | Detail |
|---|---|
| Product | KidStreet Parent Portal |
| Phase | Phase 1 — Parent MVP + Phase 2 roadmap |
| Version | 1.0 |
| Author | KidStreet Product Team |
| Status | Draft — For Developer Review |
| Related Doc | KidStreet Provider Portal BRD v1.0 |
| Target Release | TBC |

---

## 1. Executive Summary

KidStreet is a two-sided marketplace connecting parents with local kids' activity providers across Australia. The **Parent Portal** is the consumer-facing half of the platform — the surface through which parents discover activities, book classes, pay providers, manage their children's schedules, communicate with providers, and share great experiences with their network.

Phase 1 delivers a responsive web application covering end-to-end discovery, booking, and communication. Phase 2 extends this to native mobile apps, AI-powered search, group payments, and richer parent community features.

### Design Principles

- **Trust first.** Every screen reinforces that KidStreet is a safe, verified, and parent-endorsed platform. For a product involving children, trust signals are existential — not decorative.
- **Frictionless booking.** A parent should be able to find, evaluate, and confirm a booking for their child in under 3 minutes.
- **WOM-native.** The product is designed to make sharing and referral a natural outcome of a great booking experience, not an afterthought.
- **Inclusive by default.** Accessibility and inclusion filters (NDIS, sensory-friendly, WWCC verified, etc.) are prominent, not buried. Every Australian family should feel KidStreet was built for them.
- **Mobile-first responsive.** The Phase 1 web app must be fully usable on a smartphone. Native apps follow in Phase 2.

### Relationship to Provider Portal

The Parent Portal consumes the class schedule feed published by providers through the Provider Portal. When a parent books a class, payment is processed via Stripe and flows directly to the provider. KidStreet deducts a platform fee automatically. The two portals share a unified data model but are separate application surfaces.

---

## 2. User Personas

### 2.1 The Busy Parent (Primary)
A parent of 1–3 school-age children, searching for activities nearby we are Time-poor. Makes decisions on their phone. Relies on peer recommendations and reviews. Won't tolerate a clunky booking experience. Wants one place to manage all kids' activities.

### 2.2 The NDIS/Inclusion-Aware Parent (High Priority Segment)
A parent of a child with additional needs (autism, sensory processing, ADHD, physical disability). Currently faces significant friction finding inclusive, verified activities. Will become a highly loyal user and vocal advocate if KidStreet solves this for them. Specific filter requirements and trust signals apply.

### 2.3 The Research-First Parent
Compares multiple providers before deciding. Reads all reviews. Wants detailed instructor profiles, qualification badges, and transparent pricing. High-value customer once they convert.

### 2.4 The New-to-Area Parent
Recently moved suburb or city. Has no local network yet. Relies heavily on search, map view, and ratings to find trusted local options. Referral features less relevant initially; SEO and map discovery most important.

---

## 3. System Overview

```
Parent Portal (Web App — Phase 1)
│
├── Landing Page (shared with Provider acquisition)
├── Account & Registration
│   ├── Parent Profile
│   └── Child Profiles (multiple)
├── Search & Discovery
│   ├── Keyword / Category / Location Search
│   ├── Filters (age, accessibility, inclusion, schedule, price)
│   └── Map View or Map View (TBA)
├── Activity & Provider Listings
│   ├── Provider Profile Page
│   ├── Class Detail Page
│   └── Instructor Profile
├── Booking Flow
│   ├── Single Session Booking
│   ├── Recurring / Term Enrolment
│   ├── Trial Class Booking
│   └── Waitlist
├── Payments
│   ├── Checkout (Stripe / Apple Pay / Google Pay)
│   ├── Saved Payment Methods
│   └── Invoice & Receipt Management
├── My KidStreet (My Activities)
│   ├── Upcoming Bookings
│   ├── Booking History
│   └── Child Activity Summary
├── Messaging / Inbox
│   └── Conversations with Providers
├── Reviews & Ratings
├── ├── Notifications
└── Referral & Sharing
```

---

## 4. Phase 1 — Functional Requirements (MVP)

---

### FR-01: Landing Page

**Priority:** P0

#### FR-01.1 — Purpose
The landing page serves two audiences: parents (searching for activities) and providers (signing up to list). It is the primary conversion surface for both sides of the marketplace.

#### FR-01.2 — Parent-Facing Elements
- Hero section: headline, subheadline, suburb/activity search bar (primary CTA).
- Social proof bar: "Trusted by X,XXX families · X,XXX activities listed · Verified providers".
- Category quick-links (icons): Sports, Arts, Music, Dance, Academic, Drama, Outdoors, Coding.
- "How it works" — 3-step explainer: Search → Book → Go.
- Featured / trending activities (curated by KidStreet editorial or algorithmic).
- Trust signals section: WWCC Verified, Stripe Payments, Secure Booking guarantee, "As seen in [press logos]".
- Parent testimonials (3–5, with photo and child age).
- Footer: About, How it works, For Providers, Privacy Policy, Terms of Service, Contact.

#### FR-01.3 — Provider-Facing Elements (separate section or tab)
- "List your classes in 10 minutes" CTA with link to Provider Portal sign-up.
- Provider benefit highlights: free listing, direct payments, scheduling tools.
- Link to provider sign-up / login.

#### FR-01.4 — Technical Notes
- Page must load in < 2s on mobile (LCP target).
- SEO-optimised: structured data (schema.org/LocalBusiness, schema.org/Event) for discovery via Google.
- Meta title/description per city/suburb landing page variant (Phase 1: Melbourne; Phase 2: national).

---

### FR-02: Account & Login

**Priority:** P0

#### FR-02.1 — Registration Options
- Email + password registration.
- Google OAuth (one-click).
- Fields on email registration: first name, last name, email, password (min 8 chars, 1 uppercase, 1 number), suburb/postcode (used to personalise initial search results).
- Email verification required before booking (not before browsing).

#### FR-02.2 — Login
- Email + password login.
- Google OAuth login.
- "Forgot password" flow via email reset link.
- "Remember me" (persistent session, 30 days).

#### FR-02.3 — Parent Profile
Fields:
- First and last name
- Email address
- Mobile number (used for booking SMS reminders — Phase 2)
- Suburb / postcode
- Profile photo (optional)
- Communication preferences: email notifications (on/off per type), off-platform email (configured; used for message forwarding to parent's inbox)
- Referral code (generated on registration; used for WOM referral program)

#### FR-02.4 — Account Security
- Passwords stored as bcrypt hashes. KidStreet never stores plaintext passwords.
- All sessions use JWT tokens with expiry.
- Option to enable two-factor authentication via email OTP (Phase 2 for SMS 2FA).

---

### FR-03: Child Profiles

**Priority:** P0

#### FR-03.1 — Multiple Children
- A parent account can hold unlimited child profiles.
- Child profiles are separate from the parent account and cannot be made public.

#### FR-03.2 — Child Profile Fields

| Field | Required | Notes |
|---|---|---|
| First name | Yes | |
| Last name | Yes | Stored but not displayed publicly |
| Date of birth | Yes | Used to auto-filter age-appropriate activities; displayed as age on bookings |
| Gender | No | Optional; used for relevant activity filtering (e.g. girls-only classes) |
| Profile photo | No | Optional; shown on booking confirmations |
| Allergies | No | Free text + common allergy checkboxes (nuts, dairy, gluten, bee stings) |
| Medical notes | No | Free text (e.g. asthma, epilepsy, EpiPen required) |
| Additional needs | No | Multi-select: ADHD, Autism Spectrum, Sensory Processing, Physical Disability, Vision Impairment, Hearing Impairment, Other (free text) |
| NDIS participant | No | Yes/No; used to surface NDIS-eligible providers |
| Emergency contact | No | Name + phone; shared with provider on booking confirmation |
| School / year level | No | Optional; used for school-related search |

#### FR-03.3 — Privacy & Data Handling
- Medical notes and additional needs data are classified as **sensitive personal information** under the Australian Privacy Act 1988.
- This data is encrypted at rest (AES-256) and is visible only to:
  1. The parent who entered it.
  2. The provider, in context of an active enrollment (read-only, on the class roster).
- KidStreet staff do not have routine access to medical/additional needs fields.
- Parent can delete or edit any child profile field at any time.
- Deleting a child profile anonymises (not hard-deletes) historical booking data for compliance.

#### FR-03.4 — Child Selection in Booking Flow
- At the point of booking, the parent selects which child(ren) the booking is for from their saved profiles.
- Age is auto-validated against the class's age group (soft warning, not hard block — some parents book across age boundaries with provider consent).

---

### FR-04: Search & Discovery

**Priority:** P0

#### FR-04.1 — Search Bar
- Present on the landing page and persistent in the nav header across all parent-facing pages.
- Fields:
  - **What**: keyword (free text) or activity category (dropdown/typeahead).
  - **Where**: suburb/postcode/location (autocomplete via Google Places API), or "Use my location".
  - **When** (optional at initial search): day of week, time of day.
- Search returns a results page (list + map toggle).

#### FR-04.2 — Natural Language / AI Search (Phase 1 basic, enhanced Phase 2)
- Phase 1: keyword matching with category inference (e.g. "soccer for my 8 year old on weekends" parses to: category=Sports/Soccer, age=8, schedule=weekend).
- Phase 2: full natural language search powered by LLM (e.g. "indoor activities near me for a sensory-sensitive 6 year old on rainy days").

#### FR-04.3 — Search Results Page
- Default view: list of matching classes/providers.
- Toggle: List view / Map view.
- Sort options: Relevance (default) · Distance · Rating · Price (low to high) · Newest.
- Pagination: infinite scroll (list view); continuous map (map view).

#### FR-04.4 — Search Result Card (List View)
Each card shows:
- Provider photo / class photo
- Provider name
- Class name
- Activity category tag
- Age group (e.g. "Ages 5–8")
- Day(s) + time
- Suburb + distance from search location
- Price (per session or from $X/term)
- Star rating + review count (e.g. ★ 4.9 · 47 reviews)
- Available spots indicator: "4 spots left" / "Waitlist" / "Full"
- Trust badge(s): WWCC Verified ✓, Top Provider ⭐, Accessibility icons (wheelchair, sensory)
- Social proof nudge (if applicable): "12 families enrolled this term"
- CTA: "Book Now" or "View Details" or "Join Waitlist"

#### FR-04.5 — Filters Panel
Filters appear as a collapsible panel on the left (desktop) or as a bottom sheet (mobile). All filters are multi-select unless noted.

**Schedule Filters**
- Day of week (Mon–Sun, multi-select)
- Time of day: Morning / Afternoon / Evening
- Start date (date picker)

**Activity Filters**
- Activity category (multi-select from taxonomy)
- Age group (slider: 0–18)
- Session type: Single session / Recurring / Trial / Camp / Holiday Program

**Price Filters**
- Price range (slider: $0 – $200+)
- Free or trial sessions only (toggle)

**Inclusion & Accessibility Filters** *(surfaced prominently; not buried)*
- WWCC Verified ✓ (toggle — on by default)
- Neuro-diversity friendly
- Sensory-friendly environment
- Autism-friendly
- Quiet hours offered
- Wheelchair accessible
- NDIS registered / NDIS eligible
- Social story available
- Indoor only *(auto-promoted to top of filter panel when device weather forecast shows rain — see FR-04.6)*

**Provider Filters**
- Minimum rating (★ 4.0+, ★ 4.5+, ★ 4.8+)
- Verified providers only
- Has video intro
- Accepts online payments (vs. enquire only)

#### FR-04.6 — Weather-Aware Indoor Filter
- The platform checks the weather forecast for the parent's search location using a weather API (Bureau of Meteorology or OpenWeatherMap).
- If the forecast for the searched date (or next weekend if no date specified) shows rain probability > 60%, the "Indoor only" filter is automatically promoted to the top of the filter panel with a prompt: "🌧 Rain forecast this weekend — showing indoor activities first."
- The parent can dismiss this promotion. The filter remains available regardless of weather.

#### FR-04.7 — Accessibility Filter Definitions & Provider Self-Declaration
- Providers self-declare inclusion attributes in their Provider Portal profile (see Provider BRD FR-07).
- Each declared attribute is shown on the provider's public listing with a "Provider declared" label.
- Community verification: if ≥3 parents confirm an attribute via review (e.g. "Yes, this venue is wheelchair accessible"), the label upgrades to "Community Verified ✓".
- KidStreet does not independently audit provider declarations in Phase 1. A report flag is available for parents to flag inaccurate declarations.

#### FR-04.8 — Personalised Search (Logged-in Parents)
- When logged in, search results are personalised based on:
  - Child profile(s) — age-appropriate results surfaced first.
  - Suburb from parent profile.
  - Past bookings — "You've booked with Elite Soccer Academy before" shown on card.
  - Saved / favourited providers.

---

### FR-05: Map Search

**Priority:** P0

#### FR-05.1 — Map View
- Interactive map (Google Maps API) showing class/provider pins within the searched area.
- Each pin represents a provider location; clicking opens a mini-card with class summary and "View Details" CTA.
- Map updates continuously as the parent pans/zooms (no need to click "Search this area" — continuous search).

#### FR-05.2 — Map Pin Styling
- Pins are colour-coded by activity category (consistent with category taxonomy colours).
- Pin shows availability state: green (spots available), orange (≤ 2 spots), red (full/waitlist).
- Cluster pins when multiple providers are in close proximity; click to expand.

#### FR-05.3 — Map–List Sync
- Hovering a list result highlights its pin on the map.
- Clicking a map pin highlights the corresponding card in the list.
- Filters applied to the list view instantly update which pins are shown on the map.

#### FR-05.4 — "Near Me" Location
- "Use my location" uses the browser Geolocation API (permission prompt).
- If permission denied, defaults to suburb entered at search.

---

### FR-06: Provider & Class Listings

**Priority:** P0

#### FR-06.1 — Provider Profile Page
A dedicated page for each provider, containing:

**Header Section**
- Cover photo + provider logo/avatar
- Provider name, category tag(s)
- Star rating + review count (e.g. ★ 4.9 · 138 reviews)
- Suburb + distance from parent's location
- Trust badges (WWCC Verified, Top Provider, Accessibility badges)
- Response rate ("Typically responds within 2 hours")
- "Message Provider" button
- "Share" button (generates shareable URL with referral tracking)

**Trust & Verification Section** *(prominent, not buried)*
- WWCC Verified ✓ (with verification date)
- ABN Registered ✓
- Public Liability Insurance ✓
- First Aid Certified (if declared)
- KidStreet tier badge (Verified / Top Provider / KidStreet Select)

**About Section**
- Provider description
- Years in operation (if provided)
- Languages offered

**Classes / Timetable Section**
- List or grid of all active classes offered by this provider.
- Each class card shows: class name, age group, day/time, price, spots available, CTA.
- Filterable by age group, day, class type.

**Instructors / Coaches Section**
- Coach profile cards: photo, name, role, brief bio, qualifications.
- Clicking a coach card opens the Instructor Profile (FR-06.3).

**Photos & Videos Section**
- Photo gallery (grid, lightbox on click).
- Intro video embed (YouTube/Vimeo) if provided.

**Reviews Section**
- Star rating breakdown (distribution chart: 5★ to 1★).
- Individual reviews sorted by most recent (default) or most helpful.
- Each review shows: parent first name + suburb, child age at time of review, star rating, review text, date, and provider reply (if any).
- "Verified Booking" badge on reviews from confirmed enrollments.
- Photo reviews (if parent uploaded photo with review).
- "Was this helpful?" upvote on each review.
- Flag review option.

**Location & Venues Section**
- Embedded map showing venue location(s).
- Address, parking notes (if provided by provider).
- Link to Google Maps directions.

#### FR-06.2 — Class Detail Page
A dedicated page for each class, containing:
- Class name, provider name (link to provider profile)
- Activity category, age group
- Class description
- Schedule (recurring days/times, term dates)
- Venue (map embed)
- Price: per session and/or term/block pricing
- Capacity + spots available (real-time)
- Instructor (link to instructor profile)
- What to bring (if provided by provider)
- Cancellation / refund policy
- Inclusion attributes applicable to this class
- Reviews specific to this class (filtered from provider reviews)
- "Book Now" / "Join Waitlist" CTA
- "Save to Favourites" toggle
- Share button

#### FR-06.3 — Instructor Profile
- Photo, name, title/role
- Bio (written by provider)
- Qualifications and certifications (e.g. "Football Federation Australia Licensed Coach", "First Aid Certified")
- Classes they teach (links to class pages)
- WWCC status: "WWCC Verified ✓" (sourced from provider-declared data)

---

### FR-07: Booking Flow

**Priority:** P0

#### FR-07.1 — Booking Entry Points
A parent can initiate a booking from:
- The "Book Now" button on a class card (search results).
- The "Book Now" CTA on the Class Detail Page.
- A direct booking link shared by a provider or another parent (referral flow).

#### FR-07.2 — Single Session Booking
Step 1: **Select Session**
- Calendar or date-list showing available sessions for the class.
- Each session shows date, time, available spots.
- Parent selects the desired session(s).

Step 2: **Select Child**
- Parent selects from their saved child profiles, or adds a new child profile inline.
- Age validation: soft warning if the child's age is outside the class's stated age group.
- Medical notes / additional needs for the selected child are shown to the parent with a prompt: "Your notes will be shared with the provider."

Step 3: **Review & Pay**
- Order summary: class name, date/time, venue, child name, price.
- Transparent fee breakdown: `$25.00 class fee + $0.95 KidStreet booking fee = $25.95 total`.
- Cancellation policy shown before payment.
- Payment method: saved card / new card / Apple Pay / Google Pay.
- "Book Now" confirmation button.

Step 4: **Booking Confirmation**
- Confirmation screen: booking reference, class details, venue map, add to calendar (Google / Apple / .ics download).
- Email confirmation sent to parent.
- Provider notified of new booking.
- **WOM prompt**: "Love KidStreet? Share with a friend and you'll both get $5 off your next booking." (referral link pre-populated with parent's referral code).

#### FR-07.3 — Recurring / Term Enrolment
- Available on classes set up with term/block pricing by the provider.
- Parent selects a term start date; the system shows all sessions included in the term.
- Price displayed as a lump sum or instalment option (if enabled by provider — Phase 2 for instalments).
- On confirmation, all term sessions are added to the parent's upcoming bookings calendar.
- Cancellation of a recurring enrolment: governed by provider's cancellation policy; shown prominently before payment.

#### FR-07.4 — Trial Class Booking
- Providers can mark a class session as "Trial" (free or at a reduced price).
- Trial booking flow is identical to single session booking but with $0 or discounted price.
- If using Stripe zero-dollar authorisation: parent's card is saved for future bookings but not charged.
- A trial booking does not consume a regular class spot unless the provider configures it to do so.
- After the trial session, the parent receives an automated prompt: "How was it? Book the full term now — [link]."

#### FR-07.5 — Waitlist Booking
- When a class is full, "Book Now" is replaced with "Join Waitlist".
- Parent joins the waitlist by completing Steps 2–3 of the standard booking flow (child selection + contact details). Payment is NOT taken at waitlist join.
- Parent receives a waitlist confirmation with their position (e.g. "You're #2 on the waitlist").
- When a spot opens, the parent receives an in-app notification and email: "A spot opened up in [Class Name]! You have 24 hours to confirm your booking."
- The parent clicks through, completes payment, and is enrolled.
- If the parent does not respond within 24 hours, the offer moves to the next waitlist position.

#### FR-07.6 — Booking Management (My KidStreet)
- Parent can view all upcoming bookings from their dashboard.
- Per booking, the parent can:
  - View booking details.
  - Add to calendar.
  - Message the provider.
  - Cancel the booking (subject to provider's cancellation policy; refund calculated and shown before confirmation).
  - Move to a different session (if provider allows class swaps — shows available sessions within the same class).

---

### FR-08: Payments

**Priority:** P0

#### FR-08.1 — Payment Methods Supported
- Credit / debit card (Visa, Mastercard, Amex) via Stripe.
- Apple Pay (Safari / iOS devices).
- Google Pay (Chrome / Android devices).
- PayPal (Phase 2 — evaluate demand).

#### FR-08.2 — Payment Processing
- All payments processed via Stripe.
- Payment is taken at the time of booking.
- Funds are routed directly to the provider's Stripe Connect account.
- KidStreet's platform fee is deducted as a Stripe application fee at the time of the charge.
- KidStreet never holds parent funds beyond the transaction settlement.

#### FR-08.3 — Transparent Fee Disclosure
- The KidStreet booking fee is shown as a separate line item at checkout (e.g. "$0.95 KidStreet booking fee").
- The fee is not hidden or disclosed only in the T&Cs.
- The total amount charged is shown prominently as the final line.

#### FR-08.4 — Saved Payment Methods
- Parents can save up to 3 payment methods to their account.
- Saved cards are stored in Stripe (tokenised); KidStreet stores only the Stripe `payment_method_id`, last 4 digits, card type, and expiry.
- Parent can set a default payment method.
- Parent can delete saved payment methods at any time.
- Stripe's 3D Secure (SCA) flow is triggered where required by the card issuer.

#### FR-08.5 — Booking Fee & Invoice Management
- Each completed booking generates a receipt/invoice (PDF downloadable).
- Receipt shows: booking reference, provider name, class name, session date(s), child name, itemised costs, payment method (masked), payment date.
- All receipts accessible from "My KidStreet → Booking History".
- Email copy of receipt sent automatically on booking confirmation.

#### FR-08.6 — Refunds
- Cancellation refunds are governed by the provider's stated refund policy.
- KidStreet's booking fee is non-refundable (disclosed at checkout).
- Refunds are processed via Stripe back to the original payment method.
- Parent receives email confirmation of refund with expected return timeline (typically 5–10 business days depending on card issuer).
- If a provider cancels a session, the parent receives an automatic full refund (including KidStreet booking fee) within 48 hours.

---

### FR-09: My KidStreet (My Activities)

**Priority:** P0

#### FR-09.1 — Dashboard Overview
The parent dashboard ("My KidStreet") is the logged-in home screen for parents. It provides:
- Greeting with parent's first name.
- Child profile switcher (quick-access pills for each child profile).
- "Upcoming this week" — next 7 days of booked sessions.
- Quick actions: Search Activities, Manage Children, Messages.
- Recent activity feed: new messages, confirmed bookings, reminders.

#### FR-09.2 — Upcoming Bookings
- List of all future confirmed bookings across all children.
- Each booking card shows: class name, provider name, date/time, venue, child name, session type.
- Actions per booking: View details, Add to calendar, Message provider, Cancel booking.
- Filter by child profile.

#### FR-09.3 — Saved / Favourites
- Parents can "Save" any class or provider to a favourites list.
- Accessible from "My KidStreet → Saved".
- Saved items show current availability status (updates in real-time).

---

### FR-10: Reviews & Ratings

**Priority:** P0

#### FR-10.1 — Review Eligibility
- Only parents with a confirmed, completed booking can leave a review for that class/provider.
- One review per booking (not per class). A parent who attends 10 sessions can leave one review per term enrolment.
- Review prompt is sent via email 24 hours after the booked session date.

#### FR-10.2 — Review Submission
Fields:
- Star rating (1–5, required)
- Written review (optional, min 20 chars if submitted)
- What went well (optional free text)
- Photo upload (optional, max 5 photos, 5MB each)
- Tags (multi-select from list): Great coach, Welcoming, Well-organised, Good value, My child loved it, Safe environment, Inclusive, Would recommend

#### FR-10.3 — Review Display Rules
- Reviews are shown on the provider's public profile and class detail page.
- Reviews are sorted by: Most Recent (default) / Most Helpful (sorted by upvotes).
- Reviews include: reviewer first name + suburb, child age at time of review (e.g. "Parent of a 7-year-old"), star rating, review date, review text, tags, photos.
- "Verified Booking ✓" badge on all reviews.
- Providers can publicly reply to reviews (reply appears below review on listing).
- KidStreet does not allow providers to delete reviews. Providers can flag reviews for moderation.

#### FR-10.4 — Moderation
- Reviews containing offensive, defamatory, or spam content can be flagged by providers or other users.
- Flagged reviews enter a moderation queue reviewed by KidStreet within 48 hours.
- If a review violates the Review Policy, it is removed. The reviewer is notified.

#### FR-10.5 — Rating Aggregation
- Displayed rating = weighted average of all verified reviews (last 24 months weighted more heavily).
- Rating is shown on search result cards, provider profile page, and class detail pages.

---

### FR-11: Messaging / Inbox

**Priority:** P0

#### FR-11.1 — Messaging Entry Points
- From My Activites, Booking details page: "Message Provider" button (creates an Enquiry thread).
- From a confirmed booking: "Message Provider" from the booking detail (creates a Booking thread).
- From "My KidStreet → Messages".

#### FR-11.2 — Thread Types

| Type | When Created | Parent Action |
|---|---|---|
| Booking — Schedule Change | Post-booking | Request to move to a different session |
| Booking — Catch-up Request | Post-booking | Request a catch-up class for a missed session |
| Booking — Absence Notice | Post-booking | Notify provider of an upcoming absence |
| Booking — General | Post-booking | Any other communication |

#### FR-11.3 — Inbox Layout
- Split-pane: thread list (left) / active conversation (right).
- Thread list shows: provider avatar, provider name, message preview, timestamp, unread indicator, thread type tag.
- Parent receives in-app notification and email when provider replies.

#### FR-11.4 — Message Features
- Text messages (up to 2,000 chars).
- File/image attachment (max 10MB per attachment).
- Read receipts.
- Parent can view their booking details from within the conversation.

#### FR-11.5 — Email Notifications for Messages
- Parent receives an email notification when a provider replies.
- Email contains the message content and a deep link back into the conversation.
- Parents can opt out of email notifications for messages in their notification settings.

---

### FR-12: Notifications

**Priority:** P0 (email) / Phase 2 (push)

#### FR-12.1 — Email Notifications (Phase 1)

| Event | Email to Parent | Timing |
|---|---|---|
| Booking confirmed | Yes | Immediately |
| Booking receipt / invoice | Yes | Immediately |
| Booking reminder | Yes | 24 hours before session |
| Booking reminder | Yes | 2 hours before session |
| Class cancelled by provider | Yes | Immediately on cancellation |
| Waitlist spot offer | Yes | Immediately on spot opening |
| Waitlist offer expiry warning | Yes | 4 hours before 24hr deadline |
| New message from provider | Yes | Within 5 minutes |
| Payment reminder (unpaid) | Yes | As triggered by provider |
| Refund processed | Yes | Within 1 hour of Stripe processing |
| Review prompt | Yes | 24 hours after session date |
| Trial follow-up prompt | Yes | 24 hours after trial session |

#### FR-12.2 — In-App Notifications (Phase 1)
- Notification bell in the nav header shows unread count.
- Notification panel: list of recent notifications with type icon, message, timestamp, and link to relevant page.

#### FR-12.3 — Notification Preferences
- Parent can toggle email notifications on/off per category (booking reminders, messages, marketing) from account settings.
- Transactional notifications (booking confirmation, cancellation, refund) cannot be turned off.

---

### FR-13: Trust Signals & WOM / Referral Mechanic

**Priority:** P0

Trust signals are treated as a first-class product requirement, not a design afterthought. For a marketplace involving children, trust is the primary driver of conversion and retention.

#### FR-13.1 — Provider Trust Badges
The following badges are shown on provider cards (search results), provider profile pages, and class detail pages:

| Badge | Trigger | Label |
|---|---|---|
| WWCC Verified ✓ | Provider has uploaded WWCC and KidStreet has confirmed (admin verification in Phase 1, automated in Phase 2) | "WWCC Verified" |
| ABN Registered ✓ | ABN validated via ABN Lookup API | "ABN Verified" |
| Insurance ✓ | Provider has declared and uploaded public liability insurance | "Insured" |
| Top Provider ⭐ | Rating ≥ 4.8, ≥ 20 reviews, response time < 24h | "Top Provider" |
| KidStreet Select 🏆 | Editorially curated by KidStreet team | "KidStreet Select" |
| Inclusion Verified 💙 | ≥ 3 community-confirmed accessibility attributes | "Inclusion Verified" |

#### FR-13.2 — Social Proof Nudges on Class Cards
- "X families enrolled this term" — shown when enrolments > 5.
- "Booked X times this week" — shown when bookings > 2 in the last 7 days.
- "X spots left" — shown when available spots ≤ 3 (urgency signal).
- "X parents saved this class" — shown when saves > 10.

#### FR-13.3 — "Referred by [Name]" Personalisation
- When a parent arrives via a referral link (shared by another parent), the search results page and first provider page visited shows a banner: "You were referred by [Referring Parent First Name] 👋 — they think you'll love KidStreet."
- This activates social commitment and increases first-booking conversion.

#### FR-13.4 — Referral Program
- Every parent account has a unique referral link (e.g. `kidstreet.com.au/ref/sarah-m`).
- Referral link can be shared via WhatsApp, SMS, email, or copied to clipboard.
- When a referred parent completes their first booking, both the referring and referred parent receive a $5 credit applied to their next booking.
- Referral credits are shown in "My KidStreet → Wallet" (Phase 2 for full wallet UI; Phase 1: credit applied at checkout automatically).
- Referral dashboard: "You've referred X friends · Earned $X in credits."

#### FR-13.5 — Post-Booking WOM Prompt
- Immediately after booking confirmation, the parent is shown: "Know another parent who'd love [Provider Name]? Share and you'll both get $5 off your next booking."
- Sharing pre-populates a message: "I just booked [Child Name] into [Class Name] with [Provider Name] on KidStreet — you should check it out! [referral link]"

#### FR-13.6 — Verified Booking on Reviews
- All reviews show a "Verified Booking ✓" badge confirming the reviewer made a real booking.
- Review count shown as total (e.g. "138 reviews") not just a rating, to signal volume of real humans.

#### FR-13.7 — Response Rate Signal
- Provider profile shows: "Typically responds within [X hours/minutes]" — calculated from median response time of the last 10 provider replies.
- Shown on both provider cards (in search) and provider profile pages.

---

### FR-14: Responsive Website (Mobile-First)

**Priority:** P0

- The Phase 1 Parent Portal is a **responsive web application**, fully usable on mobile browsers (Safari iOS, Chrome Android) without a native app.
- Key mobile UX requirements:
  - Search bar accessible within one tap from any page.
  - Booking flow completable with one thumb on a small screen.
  - Filters accessible as a bottom sheet (not a sidebar) on mobile.
  - Map view uses full-screen on mobile.
  - Apple Pay and Google Pay shown as primary payment options on mobile (reduces typing friction).
  - Bottom navigation bar on mobile: Home, Search, My Bookings, Messages, Profile.
- Performance targets (mobile): LCP < 2.5s, FID < 100ms, CLS < 0.1 (Core Web Vitals).

---

## 5. Non-Functional Requirements

| Ref | Requirement | Target |
|---|---|---|
| NFR-01 | Booking completion time | A logged-in parent with a saved payment method can complete a booking in < 3 minutes |
| NFR-02 | Page load time (desktop) | < 2s for landing, search results, and provider profile pages (p95) |
| NFR-03 | Page load time (mobile) | Core Web Vitals: LCP < 2.5s, FID < 100ms, CLS < 0.1 |
| NFR-04 | Search response time | Search results returned in < 1.5s after form submission |
| NFR-05 | Real-time spot count | Spot count on class cards updates within 5 seconds of a new booking |
| NFR-06 | Uptime | 99.5% monthly uptime |
| NFR-07 | Browser support | Chrome, Firefox, Safari, Edge — latest 2 versions; Safari iOS 15+; Chrome Android |
| NFR-08 | Data security | All PII encrypted at rest (AES-256) and in transit (TLS 1.2+) |
| NFR-09 | Sensitive data | Medical notes / additional needs data encrypted with separate key; access-logged |
| NFR-10 | Payment security | PCI-DSS compliant via Stripe; no raw card data touches KidStreet servers |
| NFR-11 | Privacy Act 1988 | Compliant with Australian Privacy Act; Privacy Policy accessible from all pages |
| NFR-12 | WCAG Accessibility | WCAG 2.1 AA minimum — especially for inclusion-focused features |
| NFR-13 | SEO | Server-side rendering (SSR) or static generation for all public-facing pages (landing, search, provider/class pages) for Google indexability |
| NFR-14 | Email deliverability | Transactional email via SendGrid or Postmark with DKIM/SPF/DMARC configured |
| NFR-15 | GDPR-adjacent | Data deletion on request within 30 days; data portability on request |

---

## 6. Integration Requirements

| Integration | Purpose | Phase |
|---|---|---|
| **Stripe** | Payment processing, Apple Pay, Google Pay, refunds, saved cards | P1 |
| **Google OAuth 2.0** | Parent sign-in with Google | P1 |
| **Google Maps / Places API** | Location autocomplete, map view, venue maps | P1 |
| **Google Maps Geocoding API** | Convert suburb/postcode to lat/lng for proximity search | P1 |
| **Bureau of Meteorology / OpenWeatherMap API** | Weather forecast for indoor-only filter promotion | P2 |
| **ACARA / MySchool data** | NAPLAN scores and school data for schools directory | P1 |
| **SendGrid / Postmark** | Transactional email delivery | P1 |
| **ABN Lookup API** (abr.business.gov.au) | Validate provider ABN (referenced from Provider BRD) | P1 |
| **Apple OAuth** | Sign in with Apple | P2 |
| **Twilio** | SMS booking reminders and notifications | P2 |
| **Google Calendar API** | Two-way calendar sync | P2 |
| **Apple Calendar (CalDAV)** | Calendar sync for iOS | P2 |
| **PayPal** | Additional payment method | P2 |
| **Firebase Cloud Messaging** | Push notifications for mobile app | P2 |

---

## 7. User Stories — Priority Matrix

| ID | User Story | Phase | Priority |
|---|---|---|---|
| US-P-01 | As a parent, I can find kids' activities near me by suburb, type and age group | P1 | P0 |
| US-P-02 | As a parent, I can see activities on a map and search continuously as I pan | P1 | P0 |
| US-P-03 | As a parent, I can filter results by WWCC verified, sensory-friendly, wheelchair accessible, NDIS, and indoor only | P1 | P0 |
| US-P-04 | As a parent, I see the "indoor only" filter promoted automatically when rain is forecast | P1 | P0 |
| US-P-05 | As a parent, I can create multiple child profiles with age, allergies and medical notes | P1 | P0 |
| US-P-06 | As a parent, I can book a single casual class session for my child in under 3 minutes | P1 | P0 |
| US-P-07 | As a parent, I can enrol my child in a recurring weekly term | P1 | P0 |
| US-P-08 | As a parent, I can book a free or paid trial class | P1 | P0 |
| US-P-09 | As a parent, I can join a waitlist when a class is full and be notified when a spot opens | P1 | P0 |
| US-P-10 | As a parent, I can pay with a saved card, Apple Pay, or Google Pay | P1 | P0 |
| US-P-11 | As a parent, I can see a transparent fee breakdown before I pay | P1 | P0 |
| US-P-12 | As a parent, I can save payment methods securely for future bookings | P1 | P0 |
| US-P-13 | As a parent, I receive a booking confirmation email and PDF receipt | P1 | P0 |
| US-P-14 | As a parent, I can view all my upcoming bookings in one place | P1 | P0 |
| US-P-15 | As a parent, I can cancel a booking and receive a refund per the provider's policy | P1 | P0 |
| US-P-16 | As a parent, I can see WWCC, insurance and ABN verification badges on provider listings | P1 | P0 |
| US-P-17 | As a parent, I can read verified reviews from other parents with real bookings | P1 | P0 |
| US-P-18 | As a parent, I can leave a star rating and written review after my child attends a class | P1 | P0 |
| US-P-19 | As a parent, I can message a provider before booking with an enquiry | P1 | P0 |
| US-P-20 | As a parent, I can message a provider after booking (schedule change, catch-up, absence) | P1 | P0 |
| US-P-21 | As a parent, I can receive provider messages in my email inbox as well as in-app | P1 | P0 |
| US-P-22 | As a parent, I can search for and view local school NAPLAN scores | P1 | P0 |
| US-P-23 | As a parent, I can register and log in with Google | P1 | P0 |
| US-P-24 | As a parent, I can share a referral link and earn a $5 credit when a friend books | P1 | P1 |
| US-P-25 | As a parent, I see a "Referred by [Name]" personalisation when I arrive via a referral link | P1 | P1 |
| US-P-26 | As a parent, I receive email reminders 24 hours and 2 hours before a session | P1 | P0 |
| US-P-27 | As a parent, I can view instructor/coach profiles and qualifications | P1 | P0 |
| US-P-28 | As a parent, I can see real-time spot availability and urgency signals on class cards | P1 | P1 |
| US-P-29 | As a parent, I can save providers / classes to a favourites list | P1 | P1 |
| US-P-30 | As a parent, I can view my full booking history and download receipts | P2 | — |
| US-P-31 | As a parent, I can receive mobile push notifications | P2 | — |
| US-P-32 | As a parent, I can co-book a class with another parent and split the payment | P2 | — |
| US-P-33 | As a parent, I can use natural language search to find activities | P2 | — |

---

## 8. Acceptance Criteria — Critical Flows

### AC-01: Search & Discovery
- [ ] Searching "soccer 8 year old Melbourne" returns relevant class results within 1.5s.
- [ ] All results show WWCC status; results without verified WWCC do not show the WWCC badge.
- [ ] When weather API returns rain > 60% probability, "Indoor only" filter is promoted to first position in the filter panel.
- [ ] Enabling the "Sensory-friendly" filter removes all providers who have not self-declared this attribute.

### AC-02: Child Profiles
- [ ] A parent can create, edit, and delete a child profile.
- [ ] Medical notes and additional needs fields are not visible in any public-facing UI.
- [ ] When a child is selected in the booking flow, their additional needs note is shown with a "Shared with provider" disclosure.

### AC-03: Booking Flow
- [ ] A logged-in parent with a saved card can complete a single-session booking in < 3 minutes.
- [ ] The checkout shows an itemised fee breakdown with a total.
- [ ] A booking confirmation email is received within 2 minutes of completion.
- [ ] The booking appears in "My KidStreet → Upcoming Bookings" within 30 seconds.
- [ ] The provider's enrollment list shows the new booking within 30 seconds.

### AC-04: Waitlist
- [ ] When a class is full, the "Book Now" button is replaced with "Join Waitlist" on all entry points.
- [ ] Parent receives a waitlist confirmation showing their position number.
- [ ] When a provider cancels an enrollment, the first waitlisted parent receives an email and in-app notification within 60 seconds.
- [ ] 24 hours after the offer is sent with no response, the next waitlisted parent is automatically notified.

### AC-05: Payments
- [ ] Apple Pay and Google Pay are shown as the primary payment options on mobile Safari and Chrome respectively.
- [ ] A completed Stripe payment appears in the provider's transaction list within 30 seconds.
- [ ] A PDF receipt is emailed to the parent within 2 minutes of payment.
- [ ] A refund initiated by the provider reflects as "Refunded" on the parent's booking within 1 hour.

### AC-06: Reviews
- [ ] Only parents who have a confirmed completed booking can submit a review.
- [ ] A review prompt email is sent 24 hours after the session date.
- [ ] A submitted review appears on the provider's public listing within 5 minutes.
- [ ] A provider cannot delete a review; they can only flag it or reply.

### AC-07: Trust Badges
- [ ] WWCC Verified badge only appears when KidStreet has confirmed the provider's WWCC upload.
- [ ] Top Provider badge appears only when: rating ≥ 4.8, review count ≥ 20, and response time < 24h.

### AC-08: Referral
- [ ] Each parent account has a unique, functional referral URL on registration.
- [ ] When a referred parent completes their first booking, both accounts are credited $5 within 24 hours.
- [ ] A parent who arrived via a referral link sees the "Referred by [Name]" banner on their first visit.

---

## 9. Data Model — Key Entities (Parent-Side, High Level)

```
Parent
 └── has many: ChildProfiles
 └── has many: Bookings
 └── has many: SavedPaymentMethods (via Stripe)
 └── has many: InboxThreads
 └── has many: Reviews
 └── has many: Favourites (Class | Provider)
 └── has one: ReferralCode
 └── has one: NotificationPreferences

ChildProfile
 └── belongs to: Parent
 └── sensitive fields: allergies, medicalNotes, additionalNeeds [encrypted]
 └── has many: Bookings

Booking
 └── belongs to: Parent, ChildProfile, ClassSession
 └── type: SingleSession | Recurring | Trial | Waitlist
 └── has one: Payment
 └── status: Confirmed | Pending | Waitlisted | Cancelled | Completed

Payment
 └── belongs to: Booking
 └── references: Stripe PaymentIntent
 └── fields: amount, platformFee, currency, status, method

Review
 └── belongs to: Parent, Provider, Class (optional)
 └── requires: completed Booking (verified)
 └── fields: rating, text, photos[], tags[], providerReply

InboxThread
 └── belongs to: Parent, Provider
 └── type: Enquiry | ScheduleChange | CatchupRequest | AbsenceNotice | General
 └── has many: Messages

ReferralCode
 └── belongs to: Parent
 └── tracks: referred_parents[], creditsEarned
```

---

## 10. Open Questions for Developer Team

| # | Question | Owner | Due |
|---|---|---|---|
| OQ-P-01 | Weather API: BOM doesn't have a clean public API. Recommend OpenWeatherMap (free tier sufficient for Phase 1). Confirm. | Tech Lead | — |
| OQ-P-02 | Natural language search (FR-04.2 Phase 1 basic): is intent parsing done server-side via OpenAI function calling, or a custom classifier? | Tech Lead | — |
| OQ-P-03 | Medical / additional needs data: confirm encryption key management strategy (envelope encryption with KMS?). Data access logging required. | Tech Lead + Security | — |
| OQ-P-04 | Apple Pay domain verification: requires `.well-known/apple-developer-merchantid-domain-association` file on the domain. Confirm infra setup. | Tech Lead | — |
| OQ-P-05 | WWCC verification: Phase 1 is manual (KidStreet admin confirms). What is the SLA for verification? Does this gate listing going live? | Product / Ops | — |
| OQ-P-06 | Referral credit: implemented as Stripe coupon / promotion code, or as a KidStreet internal credit ledger? | Tech Lead | — |
| OQ-P-07 | Community verification for accessibility attributes (FR-04.7): what is the threshold for upgrading from "provider declared" to "community verified"? Recommend 3 confirmed reviews. | Product | — |
| OQ-P-08 | Schools directory: should school pages be indexable by Google? If yes, SSR/SSG is required. Confirm rendering strategy for school pages. | Tech Lead | — |
| OQ-P-09 | KidStreet booking fee passed to parent vs. absorbed: confirm commercial decision — is the $0.95 fee shown to parent or absorbed into the class price shown? | Product / Commercial | — |

---

## 11. Glossary

| Term | Definition |
|---|---|
| Parent | The adult user of the parent-facing portal who books activities on behalf of their child(ren) |
| Child Profile | A sub-profile under a parent account representing one child, including sensitive medical/needs data |
| Provider | A business listed on KidStreet offering kids' activities (see Provider BRD) |
| Class | A recurring or one-off activity offered by a provider |
| Session | A single occurrence of a Class (e.g. one Saturday at 9am) |
| Booking | A confirmed reservation linking a Child to a Class Session, with payment |
| Waitlist | A queue of parents who want to enrol when a class is full |
| Enrolment | Synonymous with Booking; used interchangeably in provider context |
| Stripe | Payment infrastructure used for all transactions on KidStreet |
| Platform Fee | KidStreet's charge per transaction (currently shown as booking fee to parent) |
| WWCC | Working With Children Check — a government-issued safety screening for those working with children in Australia |
| NDIS | National Disability Insurance Scheme (Australia) |
| Trust Badge | A visual indicator on a provider listing confirming a verified attribute (WWCC, insurance, etc.) |
| Referral Code | A unique URL/code per parent used to track word-of-mouth referrals and apply credits |
| WOM | Word of Mouth — a primary growth mechanic for KidStreet |
| Community Verified | An accessibility attribute confirmed by ≥3 parent reviews, upgrading from provider self-declaration |
| Natural Language Search | Search using conversational input (e.g. "indoor soccer for a 7-year-old on weekends") rather than structured fields |

---

*End of Document — KidStreet Parent Portal BRD v1.0*
*For questions, contact the KidStreet Product Team.*
*Related: KidStreet Provider Portal BRD v1.0*
