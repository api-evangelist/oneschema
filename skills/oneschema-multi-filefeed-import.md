---
name: Automate a Multi FileFeed import
description: Create a Multi FileFeed, upload a file via the direct presigned-URL flow, submit the import, and retrieve validated output files.
api: openapi/oneschema-multi-filefeeds-openapi.yml
operations:
  - create-multi-file-feed
  - create-multi-file-feed-import
  - create-direct-upload-multi-file-feed-import-file
  - submit-direct-upload-multi-file-feed-import-file
  - submit-multi-file-feed-import
  - get-multi-file-feed-import-files
---

# Automate a Multi FileFeed import

Multi FileFeeds automate recurring, transformation-heavy file ingestion. Base URL
`https://api.oneschema.co`; authenticate with `X-API-KEY`. Requires Multi FileFeed transforms to be
enabled for the organization.

## Steps

1. **Create the feed** — `POST /v1/multi-file-feeds` (`create-multi-file-feed`) with at least one
   template (keys unique). Optionally set a source/destination (S3 or SFTP), an event webhook, and
   metadata.
2. **Open an import** — `POST` the import-create endpoint (`create-multi-file-feed-import`).
3. **Direct upload (large files)** —
   `create-direct-upload-multi-file-feed-import-file` returns `upload_id`, `presigned_url`, and
   `content_type`. `PUT` the file bytes to `presigned_url` with that exact `Content-Type`, then call
   `submit-direct-upload-multi-file-feed-import-file` with the `upload_id` to finalize.
4. **Submit** — `submit-multi-file-feed-import` to run the transforms/validation pipeline.
5. **Collect output** — `get-multi-file-feed-import-files` lists produced files; fetch each via its
   download-URL endpoint.

## Rules

- The `object_uri` and file basename must be unique within an import — a duplicate returns `409`.
- Set `Content-Type` on the presigned `PUT` to the returned `content_type` or the file store rejects
  the upload.
- Transforms use optimistic locking (`last_known_commit_id`); a dirty HEAD returns `409` unless you
  pass `force: true`. See `conventions/oneschema-conventions.yml`.
