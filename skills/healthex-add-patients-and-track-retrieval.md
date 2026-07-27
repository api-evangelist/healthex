---
name: Add patients to a study and track record retrieval
description: Enroll patients into a HealthEx project/study, trigger outreach, and monitor per-patient data-retrieval progress to completion.
api: authentication/healthex-authentication.yml
source: https://docs.healthex.io/api
operations: [generate-token, add-patients, get-patient-flow-progress, get-study-by-id, search, get-usage-summary]
---

# Add patients to a study and track record retrieval

## Steps
1. Mint an organization JWT: `POST https://api.healthex.io/v1/auth/token`.
2. Enroll patients: `POST /api/add-patients` (initiates patient outreach and record location).
3. Look up study context with `GET /api/get-study-by-id` and locate records via `GET /api/search`.
4. Track progress with the Patient flow progress API (introduced v28, supersedes the
   Data retrieval status API) — it returns unified consent status and retrieval data.
5. Watch per-facility location statuses; handle `AUTHENTICATION_EXPIRED` and
   `AUTHENTICATION_NEEDED_NEW_FACILITY` by prompting re-authentication.
6. Review consumption with `GET /api/get-usage-summary`.

## Rules
- Use the Patient flow progress API rather than the deprecated Data retrieval status API.
- Development/QA: create synthetic patients with `POST /api/create-a-test-patient`
  (see sandbox/healthex-sandbox.yml) instead of enrolling real people.
