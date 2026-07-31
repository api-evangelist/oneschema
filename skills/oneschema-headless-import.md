---
name: Run a headless OneSchema import
description: Programmatically drive a OneSchema Importer embed session end to end — create, upload a file, set headers, map columns, import, and retrieve validated rows — without the UI.
api: openapi/oneschema-importer-embeds-openapi.yml
operations:
  - create-embed
  - upload-a-csv-or-excel-file
  - set-header-row-on-an-embed-file
  - set-column-mapping-on-an-embed-file
  - import-embed-file
  - get-imported-rows-for-embed
---

# Run a headless OneSchema import

Use the OneSchema Importer Embeds API to ingest and validate a CSV/Excel file server-side.
Base URL: `https://api.oneschema.co` (regional: `api.eu`, `api.ca`, `api.au`). Authenticate every
request with the header `X-API-KEY: <your api key>` (from dashboard Settings > API Keys).

## Steps

1. **Create the session** — `POST /v1/embeds` (`create-embed`). Supply the template and
   `import_config`. The embed returns in the `initialized` state.
2. **Upload the file** — `POST /v1/embeds/{embed_id}/upload` (`upload-a-csv-or-excel-file`) as
   `multipart/form-data`. Requires state `initialized`; returns `uploaded`, `headers_set`, or
   `columns_mapped`.
3. **Set the header row** — `POST /v1/embeds/{embed_id}/set-header`
   (`set-header-row-on-an-embed-file`), either auto-detected or by index. Requires `uploaded`.
4. **Map columns** — `POST /v1/embeds/{embed_id}/map` (`set-column-mapping-on-an-embed-file`) to
   map sheet columns to template columns and trigger validation. Requires `headers_set`.
5. **Import** — `POST /v1/embeds/{embed_id}/import` (`import-embed-file`). Requires
   `columns_mapped`; returns `import-pending` or `imported` per `import_config`.
6. **Retrieve results** — `GET /v1/embeds/{embed_id}/imported-rows` (`get-imported-rows-for-embed`)
   for validated rows (paginated), or `GET /v1/embeds/{embed_id}/imported-file-url` for a
   15-minute presigned CSV URL.

## Rules

- Respect the state machine: each step only accepts the prior state. On a `409`/`422`, re-fetch the
  embed with `GET /v1/embeds/{embed_id}` (`get-embed`) and check `status`.
- Errors use a custom `ErrorResponse` JSON envelope (not RFC 9457). See
  `errors/oneschema-problem-types.yml`.
- No request-level idempotency key exists — do not blindly retry `import`; check state first.
- For production end-user sessions, mint an HS256 JWT (claims `iss` = Client ID, `user_id`) signed
  with your Client Secret; see `conventions/oneschema-conventions.yml`.
