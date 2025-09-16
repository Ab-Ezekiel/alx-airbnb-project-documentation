# Flowcharts — Airbnb Clone (Backend)

This folder contains flowcharts visualizing key system processes. The main flowchart included here shows the **Property Booking** backend process, from availability checks through payment and notifications.

Files:
- `data-flow-diagram.png` — Booking process flowchart (exported PNG).

Notes:
- The flowchart models backend responsibilities: availability locking, transactional booking creation, payment intent creation, payment capture/verification, updating availability calendar, notifications, and payout scheduling.
- For payment provider webhooks (async), the webhook handler should update booking/payment records and trigger confirmations/notifications accordingly.
