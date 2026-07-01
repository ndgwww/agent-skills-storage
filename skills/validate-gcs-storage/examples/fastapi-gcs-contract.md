# FastAPI GCS Contract Example

This is a compact contract shape for review. Adapt names to the project; do not treat it as a drop-in implementation.

```python
from fastapi import APIRouter, File, HTTPException, UploadFile

router = APIRouter(prefix="/api/objects")


@router.post("")
async def upload_object(file: UploadFile = File(...)):
    if file.content_type not in {"text/plain", "image/png", "image/jpeg"}:
        raise HTTPException(status_code=415, detail="unsupported content type")

    object_name = object_names.create(prefix=settings.object_prefix, filename=file.filename)
    result = await storage.upload_file(
        name=object_name,
        file=file.file,
        content_type=file.content_type,
        if_generation_match=0,
    )
    return {
        "bucket": settings.bucket_name,
        "name": result.name,
        "generation": result.generation,
        "size": result.size,
        "content_type": result.content_type,
    }


@router.post("/upload-confirm")
async def confirm_upload(payload: ConfirmUploadRequest):
    object_names.require_under_prefix(payload.name, settings.object_prefix)
    metadata = await storage.get_metadata(payload.name)
    if metadata.size != payload.expected_size:
        raise HTTPException(status_code=409, detail="size mismatch")
    if metadata.content_type != payload.expected_content_type:
        raise HTTPException(status_code=409, detail="content type mismatch")
    return metadata
```

Contract review points:

- Backend owns object names and prefix enforcement.
- Frontend filenames influence only the basename or display metadata.
- Create-only uploads use generation preconditions when overwrite is not allowed.
- Direct upload has a confirmation endpoint; issuing an upload URL is not completion evidence.
- Delete requests include generation when stale-delete protection matters.
