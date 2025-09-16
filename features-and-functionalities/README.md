# Features & Functionalities — Airbnb Clone (Backend)

## Objective
Document the key backend features and functionalities required to build a scalable, secure, and robust Airbnb Clone backend, per the project requirements.

---

## Summary
The backend must support secure authentication & authorization, property listing management, booking/reservation handling, payments, reviews & ratings, search & filtering, notifications, admin operations, and integrations with third-party services (payments, email, cloud storage). The design must meet both functional requirements and non-functional constraints (scalability, security, performance, and testing).

---

## Core Functionalities (detailed)

### 1. User Management
- **Sign up / Login**
  - Guests and Hosts sign up using email + password.
  - Support OAuth (Google/Facebook) as optional.
  - JWT-based tokens (access + refresh) for session management.
- **Profile Management**
  - Profile photo, contact info, verification status, preferences.
- **Roles & Permissions**
  - Role-based access control (Guest, Host, Admin).

### 2. Property Listings Management
- **Create / Read / Update / Delete (CRUD)**
  - Title, description, location (address + lat/long), price, amenities, rules.
  - Photo uploads stored in cloud (S3/Cloudinary).
- **Availability**
  - Calendar-based availability and seasonal pricing adjustments.

### 3. Search & Filtering
- Search by location, date range, guests count, price range.
- Filtering by amenities, property type, rating, instant-book option.
- Pagination and sorting (price, rating, distance).

### 4. Booking Management
- **Booking Creation**
  - Reserve property for date range, with availability validation to prevent double-booking.
- **Lifecycle**
  - Status: pending → confirmed → completed → cancelled.
  - Host approval workflow (optional) and cancellation rules.
- **Concurrent Safety**
  - Use DB transactions/row locks or application-level locks to prevent race conditions.

### 5. Payment Integration
- Integrate with Stripe/PayPal:
  - Create payment intents, capture payments, refunds, host payouts.
  - Multi-currency support and transaction history.
- **Security**
  - Tokenize card data; do not store raw card details.

### 6. Reviews & Ratings
- Only guests who completed bookings can review.
- Link reviews to booking IDs to prevent abuse.
- Property aggregated rating calculation.

### 7. Notifications & Messaging
- Email & in-app notifications: bookings, cancellations, payment receipts.
- Optional in-app messaging between guest and host.
- Webhooks: handle payment provider events and other external callbacks.

### 8. Admin Dashboard & Controls
- Monitor users, listings, bookings, payments.
- Moderation tools: ban/unban users, remove listings, dispute resolution.

### 9. Background Jobs & Scheduling
- Asynchronous tasks: emails, image processing, payouts (Celery / RQ).
- Scheduled tasks for housekeeping and periodic jobs.

---

## Technical Requirements

### Database
- Relational DB (Postgres or MySQL).
- Required tables: `users`, `properties`, `bookings`, `reviews`, `payments`, `media`, `roles`, `transactions`.

### API Design
- RESTful endpoints (recommended) with proper HTTP verbs & status codes.
- GraphQL optional for advanced/query-heavy client needs.
- Example endpoints:
  - `POST /api/v1/auth/signup`
  - `POST /api/v1/auth/login`
  - `GET /api/v1/properties`
  - `POST /api/v1/properties`
  - `POST /api/v1/properties/:id/bookings`
  - `POST /api/v1/payments/intents`
  - `POST /webhooks/payments`

### Security
- JWT auth, RBAC, TLS/HTTPS enforced.
- Input validation & sanitization, parameterized queries/ORM usage.
- Rate limiting, CORS configured, CSRF protection for stateful endpoints.
- Secrets management (do not commit credentials).

### File Storage
- Cloud storage (AWS S3 or Cloudinary) for images and media.

### Third-Party Services
- Email: SendGrid / Mailgun
- Payment: Stripe / PayPal
- Optional: Maps API for geocoding

### Error Handling & Logging
- Centralized structured logs and global error handler for APIs.

---

## Non-Functional Requirements

- **Scalability:** Modular architecture, horizontal scaling via load balancers.
- **Performance:** Caching with Redis for frequent queries (search results), DB query optimization.
- **Security:** Encrypt sensitive fields at rest where needed, follow PCI / GDPR considerations.
- **Testing:** Unit tests, integration tests (pytest), and automated API tests in CI.
- **Observability:** Metrics, alerting, and monitoring (Prometheus/Grafana or hosted equivalents).

---

## Deliverables for this task
1. `features-diagram.png` — visual mapping (actors → features).  
2. `README.md` in this directory (this file).  

---

## Notes for implementation
- Keep business logic in service layers, controllers should be thin.
- Use database transactions for booking & payment flows to ensure consistency.
- Schedule regular dependency & security scans as part of CI.
