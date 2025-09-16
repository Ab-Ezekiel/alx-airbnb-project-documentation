# Use Case Diagram — Airbnb Clone (Backend)

## Objective
This folder contains the Use Case diagram that visualizes interactions between actors and the Airbnb Clone backend for core features such as user registration, property booking, and payments.

## Diagram
File: `use-case-diagram.png` (exported PNG of the use case diagram).

## Actors
- **Guest** — browses/searches properties, books properties, pays, leaves reviews, messages hosts.
- **Host** — lists properties, manages availability, accepts/rejects bookings, receives payouts, messages guests.
- **Admin** — moderates listings/users, views reports, resolves disputes.
- **Payment Provider** — external actor that processes payments and sends webhooks (e.g., Stripe/PayPal).
- **System / Background Jobs** — performs async tasks (notifications, payouts, image processing).

## Key Use Cases (captured in diagram)
- Register / Login
- Manage Profile
- Create / Manage Listing
- Search Properties
- View Property Details
- Make Booking / Check Availability
- Process Payment / Refund
- Leave Review
- Message (guest <-> host)
- Admin Moderation
- Payouts & Transactions
- Notifications & Webhooks

## Notes
- The diagram is a use-case view for high-level understanding; implementation details (endpoints, DB schema) are in other docs.
- Exported PNG should be placed here as `use-case-diagram.png`.
