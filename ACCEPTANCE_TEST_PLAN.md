# Acceptance Test Plan

These checks are intended to become Jest/Vitest tests during the implementation ticket. This planning task does not include feature code or an executable test suite.

## Happy path
1. Staff can load classes and see the name, start time and waitlist count.
2. Staff can open a class waitlist and see entries in queue order.
3. Staff can search/filter entries and receive consistent results.
4. Staff can add an entry with a valid name, phone number and explicit SMS consent.
5. Staff can request a mock SMS notification for an eligible entry.
6. The UI clearly labels the provider and result as simulated.
7. A successful action emits the required analytics message without personal data.

## Unhappy path and edge cases
- Empty list/search: show “No data found” or a specific empty state, not a blank panel.
- Slow/offline connection: show a loading state and a useful connectivity message with a retry option.
- Missing/malformed input: block submission, identify the field accessibly and do not create a record.
- Missing SMS consent: block notification dispatch and explain why.
- Double click/replayed request: idempotency prevents duplicate jobs.
- Mock-provider transient error: record the attempt and allow a bounded retry.
- Mock-provider permanent error: mark the job failed and stop retrying.
- Invalid status transition: return a conflict and keep the UI consistent.
- Unknown class/entry/job: return a clear 404 response.
- XSS payload in text fields: validate input and render as text, never executable markup.
- API failure: show an error state and allow a safe retry without duplicate writes.

## Non-functional requirements

### Accessibility
- Target a Lighthouse accessibility score of 100.
- Every interactive element has an accessible name/ARIA label.
- All actions are keyboard reachable and show visible focus.
- Form labels and validation messages are programmatically associated.
- Announce status changes accessibly (for example, with a polite live region).
- Do not rely on color alone to communicate status.

### Performance and resilience
- Paginate list endpoints.
- Keep the UI responsive while requests are pending.
- Use request timeouts and bounded retries.
- Show loading indicators for asynchronous operations.

### Security and privacy
- Validate input on the server and encode output.
- Encrypt phone data at rest in production and mask it in the UI.
- Keep personal data and secrets out of analytics, logs, screenshots and source control.
- Use the mock provider only; no real API keys.
- Enforce staff authorization on staff routes.

### Design handoff
- Use a clean monochromatic corporate design system.
- Keep spacing consistent with 16px/32px steps.
- Use approved design tokens rather than arbitrary colors.
- Support common staff desktop and tablet widths.
