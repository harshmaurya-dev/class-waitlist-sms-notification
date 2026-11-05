# Prompt Traceability Log — Project 13

This log records the prompts used to prepare the architecture handoff. It does not claim that implementation prompts or code-generation steps have been run.

## Prompt 1 — Requirements review
“Review the ENG-135180 specification for the Class Waitlist SMS Notification Job using a Twilio mock. Pull out the scope, happy paths, unhappy paths, non-functional requirements and definition-of-done items. Keep this ticket at architecture-planning level; do not write feature code.”

## Prompt 2 — Data model
“Propose a relational schema for classes, waitlist entries, notification jobs and notification attempts. Include field types, keys, constraints, indexes, lifecycle states and privacy considerations. Add a Mermaid ER diagram. Use a mock SMS provider and account for idempotent dispatch and retry history.”

## Prompt 3 — API contracts
“Draft REST API contracts for listing classes, viewing/searching a class waitlist, adding an entry, requesting a mock notification, checking job status and updating entry status. Include example requests/responses, status codes, validation, idempotency, authorization and a consistent error format.”

## Prompt 4 — Quality checks
“Turn the supplied requirements into an acceptance test plan. Cover empty states, slow connections, invalid input, duplicate requests, provider failures, XSS, keyboard navigation, ARIA labels and the Lighthouse accessibility target. Do not implement the feature.”

## Prompt 5 — Final review
“Check the README, ERD, API contracts and acceptance plan against ENG-135180. Look for missing requirements, contradictions, scope creep, real-provider assumptions and accidental exposure of personal data or secrets. Keep the result suitable for an architecture-only handoff.”

## Note
If more prompts are used in the AI coding environment, add their exact wording here before submission. Keep this log accurate rather than claiming prompts that were not actually used.
