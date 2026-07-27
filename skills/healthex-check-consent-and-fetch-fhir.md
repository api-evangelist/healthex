---
name: Check consent then fetch the full FHIR record
description: Verify a patient has consented to a study, then pull their entire medical record from the HealthEx FHIR R4 server via $everything.
api: authentication/healthex-authentication.yml
source: https://docs.healthex.io/fhir-server/
operations: [generate-token, has-patient-consented-to-study, get-consent-record, everything]
---

# Check consent then fetch the full FHIR record

## Steps
1. Mint an organization token: `POST https://api.healthex.io/v1/auth/token` with
   `{"apiKey": "...", "apiSecret": "..."}`; use the returned JWT as
   `Authorization: Bearer <JWT>` (valid 24h).
2. Confirm consent: `GET /api/has-patient-consented-to-study` (and
   `GET /api/get-consent-record` for the audit trail).
3. Fetch the record: `GET https://api.healthex.io/FHIR/R4/Person/{patientId}/$everything`
   returning a FHIR R4 Bundle.
4. Page and filter with `_count`, `_offset`, `_type` (resource type), and `_since`
   (ISO 8601) for incremental delta pulls; follow Bundle `link` elements for next pages.

## Rules
- Never fetch a record before confirming consent — tokens only authorize consented patients.
- A 401 means the token is missing/expired or the patient has not consented.
- Errors surface as FHIR OperationOutcome resources (see conventions/healthex-conventions.yml).
