# Backend Requirement Specifications

This document outlines the technical and functional requirements for core backend features of the Airbnb Clone project. Each feature includes API endpoints, input/output specifications, validation rules, and performance considerations.

---

## 1. User Authentication

### Functional Requirements
- Users can register with email and password.
- Users can log in and receive a JWT token for authentication.
- Passwords must be securely hashed using bcrypt.
- Support role-based access (Guest, Host, Admin).

### API Endpoints
- `POST /api/v1/auth/register`
  - **Input:** `{ "email": "user@example.com", "password": "secure123", "role": "guest" }`
  - **Validation:** 
    - Email must be unique and valid format.
    - Password must be at least 8 characters with alphanumeric mix.
    - Role must be one of [guest, host].
  - **Output (201):** `{ "id": "uuid", "email": "user@example.com", "role": "guest", "token": "jwt-token" }`

- `POST /api/v1/auth/login`
  - **Input:** `{ "email": "user@example.com", "password": "secure123" }`
  - **Validation:** Credentials must match existing user.
  - **Output (200):** `{ "token": "jwt-token", "expiresIn": 3600 }`

- `GET /api/v1/auth/profile`
  - **Auth:** Requires Bearer token.
  - **Output (200):** `{ "id": "uuid", "email": "user@example.com", "role": "guest", "createdAt": "timestamp" }`

### Performance & Security
- Tokens expire after 1 hour.
- Rate limiting: max 5 login attempts per minute per IP.
- All requests must use HTTPS.

---

## 2. Property Management

### Functional Requirements
- Hosts can create, edit, and delete property listings.
- Guests can view and search properties.
- Listings must include title, description, price, and availability.

### API Endpoints
- `POST /api/v1/properties`
  - **Auth:** Host only.
  - **Input:** `{ "title": "Cozy Apartment", "description": "2 bed flat", "price": 100, "location": "Lagos", "amenities": ["wifi","ac"], "availableFrom": "2025-01-01", "availableTo": "2025-01-10" }`
  - **Validation:** 
    - Title (3–100 chars), description (max 500 chars).
    - Price must be > 0.
    - Dates must be valid ISO strings.
  - **Output (201):** `{ "id": "uuid", "title": "Cozy Apartment", "hostId": "uuid" }`

- `GET /api/v1/properties`
  - **Query params:** `?location=Lagos&priceMin=50&priceMax=200&amenities=wifi`
  - **Output (200):** Array of property objects.

- `PUT /api/v1/properties/:id`
  - **Auth:** Host can only update their own properties.
  - **Input:** `{ "price": 120 }`
  - **Output (200):** Updated property object.

- `DELETE /api/v1/properties/:id`
  - **Auth:** Host only.
  - **Output (204):** No content.

### Performance & Security
- Pagination for `GET /properties` (default 10 per page).
- Indexing on location and price fields for search optimization.

---

## 3. Booking System

### Functional Requirements
- Guests can create bookings for available properties.
- Prevent double-booking via availability validation.
- Hosts can view and manage bookings for their properties.

### API Endpoints
- `POST /api/v1/bookings`
  - **Auth:** Guest only.
  - **Input:** `{ "propertyId": "uuid", "checkIn": "2025-03-01", "checkOut": "2025-03-05", "guests": 2 }`
  - **Validation:**
    - Property must exist and be available.
    - Dates must not overlap existing bookings.
    - Guests must be > 0 and within property capacity.
  - **Output (201):** `{ "id": "uuid", "status": "pending", "propertyId": "uuid", "guestId": "uuid" }`

- `GET /api/v1/bookings/:id`
  - **Auth:** Guest (own bookings) or Host (for their properties).
  - **Output (200):** Booking details.

- `DELETE /api/v1/bookings/:id`
  - **Auth:** Guest (cancel own) or Host (cancel their property’s booking).
  - **Output (200):** `{ "status": "cancelled" }`

### Performance & Security
- Booking creation must use transaction locking to prevent race conditions.
- Enforce role-based access for bookings.

---

# Summary
This requirements specification provides:
- API design (endpoints, inputs/outputs).
- Validation and business rules.
- Security and performance considerations.

It serves as the technical reference for backend developers to implement core features consistently and securely.
