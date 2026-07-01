# GCS Validation Workflows

## Configuration And Authentication

Check:

- Project id, bucket name, and object prefix are required by startup or deployment checks.
- Local credentials use ADC or a secure local credential path, not committed files.
- Deployed environments prefer platform service accounts or secret-mounted credentials.
- Config responses redact credential paths and never expose credential content.

Failure mapping:

- Missing project, bucket, or prefix: configuration error before object operations.
- Invalid local credentials: authentication failure.
- Valid credentials with insufficient bucket permissions: permission denied.
- Wrong bucket name or deleted object: not found.

## Object Naming And Prefix Safety

- Backend code should own bucket name, object prefix, object name generation, and destructive operation checks.
- Frontend code should not submit full object paths for writes unless the product has a deliberate authorization model for that.
- User-provided filenames are treated as display names or basename input only.
- Reads, downloads, deletes, and metadata operations must verify the object is under the configured prefix.
- Prefix changes are data isolation changes, not just code behavior changes.

Risk: accepting arbitrary object paths from the frontend can allow cross-tenant or cross-prefix access unless a separate authorization model prevents it.

## Service-Side Upload

Recommended flow:

1. Receive file in the backend.
2. Validate content type and size.
3. Generate object name under the configured prefix.
4. Upload with a create-only generation precondition when overwrite is not intended.
5. Return bucket, name, generation, size, and content type.

Verify:

- Duplicate create-only upload fails with a precondition error.
- Upload result contains object generation when available.
- Server logs do not print file content or credential material.

## PUT Signed URL Direct Upload

Recommended flow:

1. Frontend submits filename, content type, and size to the backend.
2. Backend validates those values and generates the final object name.
3. Backend creates a V4 Signed URL with method `PUT`.
4. Backend returns the upload URL plus required request headers.
5. Browser sends `PUT` directly to GCS with the exact required headers.
6. Backend confirmation endpoint reloads metadata and verifies expected size and content type.

Check:

- Browser content type matches the value used when signing.
- Required signed headers are present on the browser `PUT`.
- Create-only behavior uses a generation precondition when overwrite is forbidden.
- Direct upload is not complete until metadata confirmation succeeds.
- Complete upload URLs are treated as temporary credentials and redacted in logs, tickets, and chat.

Common failure causes:

- Browser uses a different content type from the signed value.
- Missing signed header on the browser `PUT`.
- Bucket CORS does not allow the browser origin.
- Bucket CORS does not allow `PUT` or required request headers.
- Object already exists and create-only precondition is active.
- Confirmation uses the wrong object name, expected size, or expected content type.

## GET Signed URL Download

Recommended flow:

1. Backend verifies object name and optional generation.
2. Backend generates a short-lived GET Signed URL.
3. Caller or backend verification performs HTTP GET without GCS credentials.
4. Verification records HTTP status and byte count, not the full URL.

Check:

- The object exists before or during signing when the product needs user-friendly errors.
- Generation is used when stable version access matters.
- Logs redact the URL query string.

## Metadata, List, SDK Download, And Delete

- List operations are scoped by prefix and max result limits.
- Metadata reads return generation, metageneration, size, content type, hashes, and update time when available.
- SDK download confirms backend service account read access.
- Delete requires generation when stale delete protection matters.
- Delete is destructive and requires explicit user approval when run against a real bucket.

## CORS Debugging

Use browser Network details:

- Confirm the real `Origin`.
- Confirm whether the failure is preflight or the actual `PUT`.
- Compare request method and headers with bucket CORS policy.
- Do not validate browser CORS from local file pages; use a local HTTP origin.

Bucket CORS policy should include:

- The local or deployed frontend origin.
- Required methods, especially `PUT` for direct upload.
- Required request and response headers used by browser direct upload.
- A reasonable max age for browser preflight caching.
