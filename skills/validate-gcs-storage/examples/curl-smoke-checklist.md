# Curl Smoke Checklist

Use this as a command-shape guide. Replace host, filenames, and payload values with safe local test values. Do not include real credentials or full temporary URLs in saved output.

```bash
BASE_URL="http://127.0.0.1:8000"

curl -sS "$BASE_URL/healthz"
curl -sS "$BASE_URL/api/config"

curl -sS -X POST "$BASE_URL/api/objects" \
  -F "file=@./sample.txt;type=text/plain"

curl -sS -X POST "$BASE_URL/api/objects/upload-url" \
  -H "content-type: application/json" \
  -d '{"filename":"browser-sample.txt","content_type":"text/plain","size":12}'

curl -sS "$BASE_URL/api/objects"

curl -sS "$BASE_URL/api/objects/metadata?name=OBJECT_NAME"

curl -sS "$BASE_URL/api/objects/download?name=OBJECT_NAME" -o /tmp/gcs-download-check.bin

curl -sS -X POST "$BASE_URL/api/objects/signed-url" \
  -H "content-type: application/json" \
  -d '{"name":"OBJECT_NAME"}'

curl -sS -X POST "$BASE_URL/api/objects/signed-url/verify" \
  -H "content-type: application/json" \
  -d '{"name":"OBJECT_NAME"}'

curl -sS -X DELETE "$BASE_URL/api/objects" \
  -H "content-type: application/json" \
  -d '{"name":"OBJECT_NAME","generation":"GENERATION"}'
```

Expected evidence:

- Config output is redacted.
- Upload returns object name, content type, size, and generation.
- Duplicate create-only upload fails with a conflict or mapped precondition error.
- Metadata confirms generation and size.
- SDK download writes bytes locally.
- Signed download verification reports status and byte count, not the full URL.
- Delete uses generation and is treated as a real destructive operation.
