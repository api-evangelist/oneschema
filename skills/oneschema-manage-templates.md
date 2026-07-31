---
name: Manage OneSchema templates
description: Create, export, update, and promote OneSchema Templates (the output schema and validation rules) across environments via the API.
api: openapi/oneschema-templates-openapi.yml
operations:
  - get-templates
  - import-template
  - export-template
  - update-template
  - push-template-environment
  - delete-template
---

# Manage OneSchema templates

Templates define the columns, data types, and validation rules that determine what clean data looks
like. Base URL `https://api.oneschema.co`; authenticate with `X-API-KEY`.

## Steps

1. **List templates** — `GET /v1/templates` (`get-templates`). Paginate with `offset` and `count`.
2. **Create a template** — `POST /v1/templates` (`import-template`) with the template JSON.
3. **Inspect a template** — `GET /v1/templates/{key}` (`export-template`) to export it as JSON.
4. **Update a template** — `PUT /v1/templates/{key}` (`update-template`).
5. **Promote across environments** — `POST /v1/templates/{key}/environment-push`
   (`push-template-environment`) to push the template to one or more environments
   (Development / Staging / Production).
6. **Remove** — `DELETE /v1/templates/{key}` (`delete-template`).

## Rules

- Use environments to stage schema changes safely; each environment has its own Client ID/Secret.
- Pagination is offset-based (`offset` + `count`); see `conventions/oneschema-conventions.yml`.
- Errors use the custom `ErrorResponse` envelope; a `404` means the `{key}` is wrong.
