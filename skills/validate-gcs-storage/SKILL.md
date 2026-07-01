---
name: validate-gcs-storage
description: Guides and validates GCS and Google Cloud Storage integration work for Python and FastAPI services, including Signed URL flows, PUT direct upload, CORS, ADC, IAM, metadata, generation preconditions, and smoke tests. Use when the user asks to design, review, debug, or verify GCS storage logic and requires official docs verification before conclusions.
---

# Validate GCS Storage

Use this skill to design, review, debug, or verify Google Cloud Storage integration logic. It focuses on Python/FastAPI services, Signed URL upload and download flows, browser direct upload, CORS, ADC/IAM, metadata, generation preconditions, and real-bucket smoke evidence.

## Required Source Discipline

1. Treat Google Cloud official documentation and the Python client reference as the primary source of truth.
2. Re-check the relevant official page before finalizing claims about Signed URLs, CORS, request preconditions, ADC, IAM, or SDK method behavior.
3. Do not expose credentials, service account files, dotenv content, full Signed URLs, signature query strings, or usable temporary authorization links.
4. Do not mutate real buckets, CORS settings, IAM bindings, lifecycle rules, or production configuration unless the user explicitly approves that action.
5. Separate mock/unit-test confidence from real-bucket confidence. Unit tests validate code paths; real GCS conclusions require configured credentials and a real smoke run.

## Resource Routing

- For official documentation links and fact-check prompts, read [references/official-sources.md](references/official-sources.md).
- For review, debugging, and smoke-test procedures, read [references/gcs-validation-workflows.md](references/gcs-validation-workflows.md).
- For `/Users/mac004/Desktop/ndgwww/gcs-python-basic-smoke` or projects derived from it, read [references/gcs-python-basic-smoke-map.md](references/gcs-python-basic-smoke-map.md).
- For browser direct upload examples, read [examples/browser-put-direct-upload.md](examples/browser-put-direct-upload.md).
- For API smoke command sequencing, read [examples/curl-smoke-checklist.md](examples/curl-smoke-checklist.md).
- For FastAPI contract examples, read [examples/fastapi-gcs-contract.md](examples/fastapi-gcs-contract.md).

## Standard Workflow

1. Identify the user's intent: design, implementation review, debugging, local smoke verification, production hardening, or documentation.
2. Inspect the current repository first. Locate config loading, object naming, API routes, GCS client creation, upload/download methods, Signed URL generation, CORS docs, and tests.
3. Verify official source facts for the exact topic. Do not rely on memory for current SDK signatures, IAM requirements, or documented GCS behavior.
4. Map the flow across authentication, bucket selection, object naming, upload path, direct upload confirmation, metadata/list/download/delete, and error handling.
5. Check failure paths before success claims: auth failure, permission denied, not found, precondition failed, unsupported content type, upload too large, CORS preflight, signature mismatch, and stale generation.
6. End with concrete verification commands or browser/API steps. State which checks are mock-only and which prove real GCS behavior.

## Evidence Levels

- Unit tests: prove local validation, routing, object naming, and mock SDK behavior.
- API smoke against a configured app: proves backend routes and error mapping.
- Browser direct upload: proves CORS plus Signed URL upload behavior.
- Real bucket smoke: proves credentials, IAM, GCS object operations, Signed URLs, and cleanup.

Do not say a real GCS flow works unless the evidence includes a real bucket operation.

## User-Facing Output

When using this skill, return:

1. Official source checks performed, with links.
2. Current repo findings, with file paths if applicable.
3. GCS flow assessment grouped by configuration, auth/IAM, object naming, upload, Signed URL, CORS, metadata/list/download/delete, and tests.
4. Concrete verification steps, clearly marking mock-only checks versus real-bucket checks.
5. Safety notes for credentials, temporary URLs, and destructive operations.
