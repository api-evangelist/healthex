---
name: Retrieve a patient's health summary via MCP
description: Use the HealthEx MCP server to pull a consented patient's current health snapshot and drill into conditions, medications, labs, and vitals.
api: mcp/healthex-mcp.yml
source: https://docs.healthex.io/mcp-server/mcp-tools
operations: [get_health_summary, get_conditions, get_medications, get_labs, get_vitals, search]
---

# Retrieve a patient's health summary via MCP

HealthEx exposes a hosted MCP server at `https://api.healthex.io/mcp` (JSON-RPC 2.0)
with per-patient data isolation. Every call requires a patient-scoped Bearer token
(patient OAuth 2.0, scope `patient/*.read`); test patients are exempt from the OAuth flow.

## Steps
1. Obtain a patient access token via the OAuth authorization-code + PKCE flow
   (`https://api.healthex.io/oauth/authorize` -> `https://api.healthex.io/oauth/token`),
   or use a test patient for development.
2. Call `get_health_summary` for the current snapshot (age, DOB, gender, active
   conditions, current medications).
3. Drill in as needed: `get_conditions`, `get_medications`, `get_labs`, `get_vitals`.
4. For free-text needs, call `search` to semantically search across all record
   categories with a natural-language query.

## Rules
- Only patients who have consented to share with your organization are accessible;
  a missing/expired token or absent consent returns 401 (see errors/healthex-problem-types.yml).
- Data is FHIR R4 / USCDIv3. Prefer `check_records_status` before assuming freshness;
  call `update_records` to request a background refresh from connected locations.
