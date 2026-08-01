---
name: Monitor StackAI flow-run analytics
description: Read per-flow run logs and org-wide project run summaries from the StackAI analytics API.
api: openapi/stacks-ai-openapi-original.yml
operations:
  - getAnalyticsDataApi
  - get_org_analytics_data_api_organizations_analytics_projects_run_summary_get
---

# Monitor StackAI flow-run analytics

Use this skill to observe flow health, token usage, and failures.

## Auth
Send `Authorization: Bearer <API_KEY>` on every request. See `authentication/stacks-ai-authentication.yml`.

## Steps
1. **Per-flow run logs** — `getAnalyticsDataApi`: `GET /analytics/org/{org_id}/flows/{flow_id}`.
   - Query: `page`, `page_size`, `start_date`, `end_date`, `user_id`, `state`
     (comma-separated from PENDING, PAUSED, RESUMED, COMPLETED, FAILED, CANCELLED).
   - Each row (`ProjectRunSummary`) gives `run_id`, `date`, `is_flow_successful`, `latency`,
     `total_tokens`, `llm_usage[]`, and `ran_by_type` (user / personal_api_key / org_api_key / system).
2. **Org-wide summary** — `get_org_analytics_data_api_organizations_analytics_projects_run_summary_get`:
   `GET /organizations/analytics/projects-run-summary`.
   - Query: `page`, `page_size`, `start_date`, `end_date`.
   - Each row gives `project_id`, `project_name`, `total_runs`, `total_errors`, `total_tokens`, `total_users`.

## Rules
- Analytics endpoints use the OAuth2 password-grant token scheme; offset pagination via `page`/`page_size` (default 25).
- Errors return HTTP 422 with the FastAPI `detail[]` envelope (see `errors/stacks-ai-problem-types.yml`).
