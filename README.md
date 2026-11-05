# Project 13 — Class Waitlist SMS Notification System

**Ticket:** ENG-135180  
**Epic:** Core Infrastructure Overhaul  
**Priority:** P1 (High) · **Story points:** 5  
**Integration:** Twilio Mock (simulation only)

## Scope
This is an **architecture-planning handoff**, not a feature implementation. The ticket asks for the definitive database schema (ERD) and API contracts; no feature code is included.

## Deliverables
- `ERD.md` — relational data model and Mermaid ERD
- `API_CONTRACTS.md` — endpoint contracts, payloads, validation and error shapes
- `ACCEPTANCE_TEST_PLAN.md` — happy/unhappy paths, accessibility, loading, empty states, telemetry and XSS controls
- `PROMPTS.md` — prompt traceability log

## Product summary
Replace paper lists and spreadsheets with a staff-friendly digital class waitlist. Staff can find a class, view/search its waitlist, and initiate a simulated SMS notification. The system must remain usable on slow connections and must never imply that a real SMS was sent.

## Key design decisions
1. A waitlist entry belongs to one class and has an explicit lifecycle.
2. Notification jobs and individual attempts are separate records for auditability.
3. The SMS provider is behind an adapter; this ticket uses a mock adapter only.
4. Dispatch is idempotent to prevent duplicate notifications on retries/double clicks.
5. Student phone numbers are sensitive: mask in UI/logs and encrypt at rest in production.
6. API responses use stable shapes and UTC ISO-8601 timestamps.
7. UI must provide loading, empty, validation and connectivity states; controls must be keyboard accessible and labelled.

## Non-goals
- Real Twilio credentials or live SMS delivery
- Payment, enrolment or student-account management
- Bulk marketing messages
- Feature code in this architecture-only ticket

## Later implementation sequence
Confirm schema/contracts; convert acceptance checks into Jest/Vitest tests; implement migrations and API handlers; add the mock SMS adapter; build the staff UI; run lint, tests and Lighthouse accessibility audit.
