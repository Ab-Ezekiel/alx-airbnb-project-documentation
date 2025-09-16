# Backend Requirement Specifications — Airbnb Clone

> **Auto-check checklist**
> - [x] File: `requirements.md` at repository root
> - [x] Sections: Functional Requirements, Technical Requirements, Non-Functional Requirements
> - [x] At least 3 features specified with endpoints, inputs, outputs, validation, performance
> - [x] Short acceptance criteria included for each feature

---

## Summary
This document specifies functional and technical requirements for the Airbnb Clone backend. It covers core features, API endpoints, input/output examples, validation rules, and performance/security expectations. The three primary features specified below are: **User Authentication**, **Property Management**, and **Booking System**.

---

## Functional Requirements

### Feature: User Authentication
**Purpose:** Provide secure sign-up, login, profile management, and role-based access.

**API Endpoints**
- `POST /api/v1/auth/register`
  - Input: `{ "email": "user@example.com", "password": "string", "role": "guest" }`
  - Validation:
    - `email`: required, RFC-5322 format, unique
    - `password`: required, min 8 chars, includes letters and numbers
    - `role`: one of `guest`, `host`
  - Output (201): `{ "id": "uuid", "email": "user@example.com", "role": "guest", "createdAt": "ISO8601" }`
  - Errors: 400 (validation), 409 (email exists)
- `POST /api/v1/auth/login`
  - Input: `{ "email": "user@example.com", "password": "string" }`
  - Output (200): `{ "access": "jwt", "refresh": "jwt", "expires_in": 3600 }`
  - Errors: 401 (invalid credentials), 429 (too many attempts)
- `GET /api/v1/auth/profile`
  - Auth: Bearer token required
  - Output (200): `{ "id": "uuid", "email": "user@example.com", "role": "guest", "profile": { ... } }`

**Acceptance Criteria**
- Users can register, receive verification, and log in to obtain JWT tokens.
- Passwords are stored hashed (bcrypt or Argon2).
- Rate limit login attempts (example: 5/min per IP).

**Performance & Security**
- Token expiry: access 1h, refresh 7d.
- All endpoints require TLS.
- Use strong hashing and salting; never store raw passwords.

---

### Feature: Property Management
**Purpose:** Allow hosts to create and manage property listings; allow guests to view/search listings.

**API Endpoints**
- `POST /api/v1/properties` (Host only)
  - Input: `{ "title": "...", "description": "...", "price": 100.0, "location": {"address":"", "lat":0.0, "lng":0.0}, "amenities": ["wifi"] }`
  - Validation:
    - `title`: required, 3–100 chars
    - `price`: required, numeric > 0
    - `location.lat/lng`: required, floats
  - Output (201): `{ "id": "uuid", "hostId": "uuid", "createdAt": "ISO8601" }`
- `GET /api/v1/properties`
  - Query params: `?location=&check_in=&check_out=&price_min=&price_max=&amenities=`
  - Output (200): paginated list: `{ "items": [...], "page": 1, "per_page": 10, "total": 123 }`
- `PUT /api/v1/properties/:id` (Host only)
  - Auth: host must own property
  - Output (200): updated property object
- `DELETE /api/v1/properties/:id` (Host only)
  - Output (204): no content

**Acceptance Criteria**
- Hosts can CRUD listings with validation and image upload links.
- Searches exclude unavailable dates and are paginated.

**Performance & Security**
- Use DB indices on location and price.
- Cache common queries (Redis) to improve response times.
- Store media in cloud storage (S3/Cloudinary), not in DB.

---

### Feature: Booking System
**Purpose:** Enable guests to reserve properties, prevent double-bookings, manage booking lifecycle.

**API Endpoints**
- `POST /api/v1/bookings`
  - Auth: Guest only
  - Input: `{ "propertyId": "uuid", "checkIn": "YYYY-MM-DD", "checkOut": "YYYY-MM-DD", "guests": 2 }`
  - Validation:
    - Dates: checkIn < checkOut, valid ISO dates
    - Availability: dates must not overlap existing confirmed bookings
    - Guests: positive integer <= property capacity
  - Output (201): `{ "id": "uuid", "propertyId":"uuid", "status":"pending", "createdAt":"ISO8601" }`
  - Errors: 409 (date conflict), 400 (invalid data)
- `GET /api/v1/bookings/:id`
  - Auth: guest (owner) or host (owner of property)
  - Output (200): booking details
- `PATCH /api/v1/bookings/:id/cancel`
  - Auth: guest or host (per cancellation policy)
  - Output (200): `{ "id":"uuid", "status":"cancelled" }`

**Concurrency & Consistency**
- Use DB transactions & row-level locks to reserve date ranges atomically.
- Alternatively, use optimistic locking with retries.
- For availability checks, re-validate before committing payment.

**Performance & Security**
- Booking creation should complete within ~2 seconds under normal load (target).
- Log booking events for auditability.

---

## Technical Requirements (cross-feature)
- DB: PostgreSQL or MySQL; use UUID primary keys.
- Auth: JWT (access + refresh), RBAC for endpoints.
- File Storage: S3 or Cloudinary for images.
- Payments: integrate Stripe or PayPal; use tokenization and webhooks.
- Testing: unit tests (pytest), API integration tests; include in CI.
- CI/CD: GitHub Actions with lint, tests, security scans (bandit, safety).

---

## Non-Functional Requirements
- **Scalability:** stateless app servers; sessions via JWT; horizontal scaling behind load balancer.
- **Security:** TLS enforced, secret management, SAST/SCA scans, rate limiting.
- **Performance:** use Redis cache for search responses; database indices on search fields.
- **Observability:** structured logs, metrics, and alerts (Prometheus/Grafana or equivalent).
- **Compliance:** do not store raw card data; follow PCI guidelines when handling payments.

---

## Appendix — Example Error Codes (common)
- `400` Bad Request — validation errors
- `401` Unauthorized — missing/invalid token
- `403` Forbidden — insufficient permission
- `404` Not Found — resource not found
- `409` Conflict — booking dates overlap or duplicate resource
- `500` Internal Server Error — unexpected errors (log and alert)

