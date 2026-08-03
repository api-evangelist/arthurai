---
name: Enforce org-wide monitoring with Arthur policies
description: Create an Arthur Scope policy, attach an alert rule to it, and assign it across applications so monitoring standards apply automatically.
api: openapi/arthur-scope-openapi.json
auth: oauth2 authorization_code (Keycloak realm arthur)
base_url: https://platform.arthur.ai/api
operations:
- create_policy
- create_policy_alert_rule
- create_policy_assignments
- post_model_alert_rule
---

# Enforce org-wide monitoring with Arthur policies

Use Arthur Scope Policies to define monitoring standards once at the organization level and have Arthur materialize them as concrete alert rules across every assigned application.

## Auth
Arthur Scope uses OAuth2 authorization-code against the Keycloak realm at `https://platform-auth.arthur.ai/realms/arthur` (authorize + token endpoints in `authentication/arthurai-authentication.yml`). Obtain an access token and send it as a Bearer token.

## Steps
1. **Create a policy** — `create_policy`. Defines an organization-level monitoring standard.
2. **Add an alert rule to the policy** — `create_policy_alert_rule`. Specifies the condition (threshold/metric) the policy enforces.
3. **Assign the policy** — `create_policy_assignments`. Binds the policy to applications/models so Arthur materializes the alert rules on each.
4. (optional) **Add a standalone model alert rule** — `post_model_alert_rule` — for an application-specific alert outside a policy; it can carry an `AlertRuleNotificationWebhook` to POST fired alerts to Slack/Jira/custom URLs (see `asyncapi/arthurai-webhooks.yml`).

## Conventions & errors
- OAuth2 scopes are fine-grained (e.g. `alert_rule_read`, `alert_rule_update`); see `scopes/arthurai-scopes.yml`.
- Pagination uses `page_size`.
- Errors are FastAPI-style JSON `{"detail": ...}`; `409 Conflict` signals dependent-resource constraints. See `errors/arthurai-problem-types.yml`.
- No idempotency-key contract — guard writes against duplicate submission yourself.
