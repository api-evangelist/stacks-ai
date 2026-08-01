---
name: Run a deployed StackAI flow
description: Invoke a deployed StackAI agentic workflow (flow) with inputs and read back its outputs, then check the run in analytics.
api: openapi/stacks-ai-openapi-original.yml
operations:
  - runDeployedFlow
  - getAnalyticsDataApi
---

# Run a deployed StackAI flow

Use this skill to call a flow that has been deployed as an API in StackAI.

## Auth
Send `Authorization: Bearer <API_KEY>` on every request. Generate the key in the StackAI console
under **Settings -> API Keys** (organization or personal). See `authentication/stacks-ai-authentication.yml`.

## Steps
1. **Run the flow** — `runDeployedFlow`: `POST /inference/v0/run/{org_id}/{flow_id}`.
   - Path params: `org_id`, `flow_id`. Optional query: `version` (defaults to latest), `verbose`.
   - Body is a free-form JSON object of the flow's declared inputs, e.g. `{"in-0": "text", "url-0": "https://..."}`.
   - The response JSON contains the values of every flow output.
2. **Confirm the run** (optional) — `getAnalyticsDataApi`: `GET /analytics/org/{org_id}/flows/{flow_id}`.
   - Filter with `state` (PENDING, PAUSED, RESUMED, COMPLETED, FAILED, CANCELLED), `start_date`, `end_date`, `page`, `page_size`.
   - Match your run by `run_id` and inspect `is_flow_successful`, `latency`, and `total_tokens`.

## Rules
- Errors return HTTP 422 with a `{"detail":[{"loc","msg","type"}]}` envelope (see `errors/stacks-ai-problem-types.yml`) — not RFC 9457.
- There is no idempotency key; do not assume re-running a flow is side-effect free (see `conventions/stacks-ai-conventions.yml`).
- The inference API auto-scales; no rate-limit headers are documented.
