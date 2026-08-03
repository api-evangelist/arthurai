---
name: Guardrail a GenAI task with Arthur Engine
description: Create an Arthur task, attach guardrail rules, then validate prompts and responses against it and record feedback.
api: openapi/arthur-engine-openapi.json
auth: http-bearer (API key as Bearer token)
base_url: https://engine.platform.arthur.ai
operations:
- create_task_api_v2_tasks_post
- create_task_rule_api_v2_tasks__task_id__rules_post
- validate_prompt_endpoint_api_v2_tasks__task_id__validate_prompt_post
- validate_response_endpoint_api_v2_tasks__task_id__validate_response__inference_id__post
- post_feedback_api_v2_feedback__inference_id__post
---

# Guardrail a GenAI task with Arthur Engine

Use the Arthur GenAI Engine to wrap a GenAI application in guardrails: validate inbound prompts and outbound responses against configured rules, then feed results back.

## Auth
Send your Arthur API key as a Bearer token: `Authorization: Bearer <API_KEY>`. Keys are managed in the platform under Settings > API Keys.

## Steps
1. **Create a task** — `create_task_api_v2_tasks_post` (`POST /api/v2/tasks`). A task represents the governed GenAI application. Save the returned `task_id`.
2. **Attach a rule** — `create_task_rule_api_v2_tasks__task_id__rules_post` (`POST /api/v2/tasks/{task_id}/rules`). Add a guardrail (e.g. PII, toxicity, hallucination) to the task.
3. **Validate the prompt** — `validate_prompt_endpoint_api_v2_tasks__task_id__validate_prompt_post` (`POST /api/v2/tasks/{task_id}/validate_prompt`) before sending it to your model. Save the returned `inference_id`.
4. **Validate the response** — `validate_response_endpoint_api_v2_tasks__task_id__validate_response__inference_id__post` (`POST /api/v2/tasks/{task_id}/validate_response/{inference_id}`) on the model's output.
5. **Record feedback** — `post_feedback_api_v2_feedback__inference_id__post` (`POST /api/v2/feedback/{inference_id}`) to close the loop.

## Conventions & errors
- Pagination on list endpoints uses `page_size` (page-number style).
- Some engine endpoints stream via `text/event-stream`.
- Errors are FastAPI-style JSON `{"detail": ...}`; `422` returns an `HTTPValidationError` list. See `errors/arthurai-problem-types.yml`.
- No idempotency-key contract — do not assume safe automatic retries on writes.
- Avoid the deprecated stateless `default_validate_prompt`/`default_validate_response` operations; prefer the task-scoped validate endpoints above.
