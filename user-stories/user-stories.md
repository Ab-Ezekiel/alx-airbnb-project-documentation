
# User Stories — Airbnb Clone (Backend)

This document converts the project use cases into user stories to guide implementation and testing. Each story includes a brief acceptance criteria and a suggested priority.

---

## US-001 — User Registration & Authentication
**Story:** As a user, I want to register and log in so that I can access the platform and perform actions according to my role (guest or host).  
**Acceptance Criteria:**
- User can sign up with email + password.
- User receives an email verification link (or token) after signup.
- User can log in and receive JWT access and refresh tokens.
- Login fails with appropriate error messages for invalid credentials.
**Priority:** High

---

## US-002 — Create and Manage Property Listings (Host)
**Story:** As a host, I want to create, update, and delete property listings so that I can advertise spaces for guests to book.  
**Acceptance Criteria:**
- Host can create a property with title, description, location (address + lat/long), price, amenities, and photos (upload or link).
- Host can update property details and availability calendar.
- Host can delete a listing; deleted listings become unavailable to guests.
**Priority:** High

---

## US-003 — Search & Filter Properties (Guest)
**Story:** As a guest, I want to search and filter properties by location, date range, guest count, price range, and amenities so that I can find accommodations that match my needs.  
**Acceptance Criteria:**
- API supports queries by location, check-in/check-out dates, number of guests, price range, and amenities.
- Results are paginated and sortable (price, rating, distance).
- Search excludes properties that are not available for the requested date range.
**Priority:** High

---

## US-004 — Booking Creation and Availability Check (Guest + Host)
**Story:** As a guest, I want to make a booking for a property and receive confirmation so that my reservation is reserved for my selected dates. As a host, I want to view and manage incoming bookings.  
**Acceptance Criteria:**
- Guest can create a booking request for specific dates.
- System checks availability and prevents double-bookings (transactional/locking behavior).
- Booking status transitions (pending → confirmed → completed → cancelled) are tracked.
- Host can accept or reject bookings (if host-approval workflow enabled).
**Priority:** High

---

## US-005 — Payment Processing & Payouts
**Story:** As a guest, I want to pay securely for my booking; as a host, I want to receive payouts after completed stays.  
**Acceptance Criteria:**
- Payment intents are created using a PCI-compliant provider (e.g., Stripe).
- Payment success/failure events are handled via webhooks.
- Payouts to hosts are scheduled according to the payout rules and logged as transactions.
- Raw card data is never stored on the platform.
**Priority:** High

---

## US-006 — Reviews & Ratings (Guest)
**Story:** As a guest, I want to leave a review and rating for a property after my stay so future guests can make informed choices.  
**Acceptance Criteria:**
- Only guests with a completed booking for the property can submit a review.
- Reviews are tied to booking IDs to prevent abuse (one review per booking).
- Property average rating updates after a new review.
**Priority:** Medium

---

## US-007 — Notifications & Messaging
**Story:** As a user (guest or host), I want to receive notifications (email/in-app) about booking confirmations, cancellations, and payment updates; I also want to message the other party.  
**Acceptance Criteria:**
- Booking confirmations and payment receipts trigger email or in-app notifications.
- A simple messaging endpoint allows guest and host to exchange messages related to a booking.
- Webhooks from payment provider produce notification events.
**Priority:** Medium

---

## US-008 — Admin Tools & Moderation
**Story:** As an admin, I want to moderate listings and users so that the platform stays safe and compliant.  
**Acceptance Criteria:**
- Admin can suspend or ban users and remove listings.
- Admin can view reports on bookings, revenue, and flagged content.
- Audit logs capture moderation actions.
**Priority:** Medium

---

## US-009 — Background Jobs & Reliability
**Story:** As a system operator, I want long-running or asynchronous tasks (emails, image processing, payouts) handled reliably so that foreground APIs remain responsive.  
**Acceptance Criteria:**
- Background worker(s) process jobs and retry on transient failures.
- Scheduled jobs (e.g., nightly cleanup, payout processing) run as configured.
- Failed jobs are logged with retry and alerting information.
**Priority:** Medium

---

## Notes & Next Steps
- Convert high-priority stories into sprint-sized tasks with specific API endpoints, request/response examples, and acceptance tests.
- For each story, write unit and integration tests (pytest) and include test coverage in CI.
