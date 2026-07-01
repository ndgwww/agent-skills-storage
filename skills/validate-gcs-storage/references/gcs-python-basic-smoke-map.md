# gcs-python-basic-smoke Map

Use this map when working with `/Users/mac004/Desktop/ndgwww/gcs-python-basic-smoke` or adapting its validation model.

The reference project validates:

```text
Python backend
  -> GCS authentication
  -> target project and bucket
  -> service-side object upload
  -> browser PUT Signed URL direct upload
  -> list and metadata
  -> SDK download
  -> GET Signed URL download
  -> generation-bound delete
```

## Important Entry Points

| Area | Path | Purpose |
| --- | --- | --- |
| App setup | `src/gcs_basic_smoke/app.py` | FastAPI app and static web serving |
| Config | `src/gcs_basic_smoke/config.py` | Required project, bucket, prefix, limits, content types, credential path redaction |
| Routes | `src/gcs_basic_smoke/api/routes_objects.py` | Upload, direct upload URL, confirm, list, metadata, download, Signed URL, verify, delete |
| Smoke route | `src/gcs_basic_smoke/api/routes_smoke.py` | End-to-end create/read/download/sign/delete smoke run |
| GCS operations | `src/gcs_basic_smoke/gcs/operations.py` | SDK calls and mapped behavior |
| Object names | `src/gcs_basic_smoke/gcs/object_names.py` | Prefix enforcement and safe filename handling |
| Schemas | `src/gcs_basic_smoke/schemas.py` | API request and response contracts |
| Tests | `tests/` | Mocked local confidence, not a substitute for real bucket smoke |

## API To SDK Map

| API | Real GCS action | SDK method or behavior |
| --- | --- | --- |
| `GET /healthz` | No | Health only |
| `GET /api/config` | No | Redacted config only |
| `POST /api/objects` | Yes | `blob.upload_from_file(..., if_generation_match=0)` |
| `POST /api/objects/upload-url` | Yes | `blob.generate_signed_url(version="v4", method="PUT")` |
| `POST /api/objects/upload-confirm` | Yes | `blob.reload()` |
| `GET /api/objects` | Yes | `client.list_blobs(bucket, prefix=..., max_results=...)` |
| `GET /api/objects/metadata` | Yes | `blob.reload()` |
| `GET /api/objects/download` | Yes | `blob.download_as_bytes()` |
| `POST /api/objects/signed-url` | Yes | `blob.generate_signed_url(version="v4", method="GET")` |
| `POST /api/objects/signed-url/verify` | Yes | Generate URL, then plain HTTP GET |
| `DELETE /api/objects` | Yes | `blob.delete(if_generation_match=generation)` |
| `POST /api/smoke/run` | Yes | End-to-end create, duplicate failure, metadata, list, download, signed download, delete |

## Behaviors To Preserve

- Startup requires project id, bucket name, and object prefix. Do not silently fall back to fake values for real smoke runs.
- Frontend filenames are sanitized and only influence the final basename.
- Backend-generated object names stay under the configured prefix.
- Service-side upload uses create-only semantics to avoid overwrite.
- Direct upload signs method `PUT`, content type, and required headers, then confirms metadata after browser upload.
- Signed URL verification performs a real HTTP GET and reports status and byte count.
- Delete requires generation to avoid deleting a newer object version.
- Tests using fake clients are useful but cannot prove real bucket access.

## Error Mapping

| HTTP | Meaning |
| --- | --- |
| 400 | Invalid object name or missing generation for delete |
| 401 | Authentication failure |
| 403 | Permission denied |
| 404 | Bucket or object not found |
| 409 | Precondition failure or direct upload metadata mismatch |
| 413 | Upload too large |
| 415 | Unsupported content type |
| 502 | GCS API or Signed URL verification failure |

Safe extraction rule: when building docs, prompts, or skills from this project, extract only API names, SDK method names, validation flow, failure mapping, and non-sensitive configuration concepts.
