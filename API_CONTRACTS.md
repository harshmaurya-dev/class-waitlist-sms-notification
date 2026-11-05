# API Contracts

Base path: `/api/v1`  
Content type: `application/json` · Timestamps: ISO-8601 UTC  
Authentication: staff session/JWT required (mechanism to be confirmed by platform owner).  
Provider: `twilio_mock` only.

## Standard error shape

```json
{
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "Please correct the highlighted fields.",
    "fields": { "phone": "Enter a valid phone number." },
    "requestId": "req_example"
  }
}
```

Use 400/422 for validation, 401 unauthenticated, 403 forbidden, 404 missing resource, 409 conflict/idempotency, 500 unexpected failure and 503 temporary dependency failure. Never return stack traces or secrets.

## 1. List classes
`GET /classes?status=scheduled&search=math&page=1&pageSize=20`

**200**
```json
{
  "data": [{
    "id": "class_uuid",
    "courseCode": "MATH-101",
    "name": "Foundations of Mathematics",
    "startsAt": "2026-12-01T09:00:00Z",
    "capacity": 25,
    "status": "scheduled",
    "waitingCount": 4
  }],
  "pagination": { "page": 1, "pageSize": 20, "total": 1 }
}
```

## 2. Get a class waitlist
`GET /classes/{classId}/waitlist?status=waiting&search=alex&page=1&pageSize=25`

**200**
```json
{
  "classId": "class_uuid",
  "data": [{
    "id": "entry_uuid",
    "studentName": "Alex Morgan",
    "phoneMasked": "******1234",
    "position": 1,
    "status": "waiting",
    "smsConsent": true,
    "createdAt": "2026-11-05T10:30:00Z"
  }],
  "pagination": { "page": 1, "pageSize": 25, "total": 1 }
}
```
An empty list returns `200` with `data: []`; UI shows “No waitlist entries found”.

## 3. Add a waitlist entry
`POST /classes/{classId}/waitlist`

Request:
```json
{ "studentName": "Alex Morgan", "phone": "+15551231234", "smsConsent": true }
```
Return `201` with the created entry, masked phone and assigned queue position. Reject missing/malformed fields, oversized values and absent consent. Normalize and validate phone server-side; never rely on client validation alone.

## 4. Create/dispatch a mock SMS notification
`POST /waitlist-entries/{entryId}/notifications`

Header: `Idempotency-Key: <unique-client-generated-key>`

Request:
```json
{ "templateKey": "class-seat-available-v1" }
```
Return `202` with `{ "data": { "jobId": "job_uuid", "status": "queued", "channel": "sms", "provider": "twilio_mock" } }`. Return 409 for key reuse with a different request. Reject entries without recorded SMS consent or not eligible for notification. Mock adapter must clearly mark results as simulated.

## 5. Get notification job status
`GET /notifications/{jobId}`

Return `200` with job ID, status, provider, attempt count and processed timestamp. Example status: `sent_mock`.

## 6. Update waitlist entry status
`PATCH /waitlist-entries/{entryId}`

Request:
```json
{ "status": "accepted" }
```
Return `200` with updated entry status and timestamp. Allow only documented state transitions; return 409 for invalid transitions.

## Operational behavior
- Show loading indicators during async requests; disable duplicate submit/dispatch while pending.
- Use bounded retries for transient mock-provider failures; never retry permanent validation/consent errors.
- Include request IDs for support diagnostics.
- Telemetry example: `[Analytics] User interacted with Class Waitlist SMS Notification Job`. Include event/action metadata only; never include names, phone numbers, message contents or other PII.
- Sanitize inputs server-side and encode output at render time; avoid unsafe HTML injection.
- Use explicit UI wording such as “Simulated notification queued/sent”; never claim a real SMS was sent.
