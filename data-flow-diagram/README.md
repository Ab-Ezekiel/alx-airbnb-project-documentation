# Data Flow Diagram — Airbnb Clone (Backend)

## Objective
This folder contains Data Flow Diagram(s) that map how data moves through the Airbnb Clone backend for core operations (user registration/login, property listing, booking, and payments).

## Deliverables
- `data-flow.png` — PNG export of the DFD (Level 0 or Level 1).  
- Optional: `data-flow-level1.png` — more detailed DFD breaking processes into services & data stores.

## Diagram notes
- External entities: Guest, Host, Admin, Payment Provider.
- Core processes: Authentication Service, Property Service, Booking Service, Payment Service, Notification Service, Background Jobs.
- Data stores: Users DB, Properties DB, Bookings DB, Payments DB, Media/File Storage.
- Show data inputs (e.g., booking request), processes (e.g., check availability), and outputs (e.g., booking confirmation, payment receipt).

## How to produce the PNG
1. Render the Mermaid code (provided in `mermaid` block in this README) using https://mermaid.live/ or Draw.io’s Mermaid plugin.
2. Export as PNG (File → Export PNG) and save the file as `data-flow.png`.
3. Copy/move the PNG into this folder.

