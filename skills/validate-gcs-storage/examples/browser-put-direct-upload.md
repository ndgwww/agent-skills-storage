# Browser PUT Direct Upload Example

This example is a shape reference only. Never paste a real temporary upload URL into docs, logs, issues, or prompts.

```js
async function uploadWithSignedPut({ createUploadUrl, file }) {
  const initResponse = await fetch(createUploadUrl, {
    method: "POST",
    headers: { "content-type": "application/json" },
    body: JSON.stringify({
      filename: file.name,
      content_type: file.type || "application/octet-stream",
      size: file.size,
    }),
  });

  if (!initResponse.ok) {
    throw new Error(`Upload URL request failed: ${initResponse.status}`);
  }

  const upload = await initResponse.json();

  const putHeaders = {
    ...(upload.headers || {}),
    "content-type": upload.content_type,
  };

  const putResponse = await fetch(upload.upload_url, {
    method: "PUT",
    headers: putHeaders,
    body: file,
  });

  if (!putResponse.ok) {
    throw new Error(`GCS upload failed: ${putResponse.status}`);
  }

  const confirmResponse = await fetch("/api/objects/upload-confirm", {
    method: "POST",
    headers: { "content-type": "application/json" },
    body: JSON.stringify({
      name: upload.name,
      expected_size: file.size,
      expected_content_type: file.type || "application/octet-stream",
    }),
  });

  if (!confirmResponse.ok) {
    throw new Error(`Upload confirmation failed: ${confirmResponse.status}`);
  }

  return confirmResponse.json();
}
```

Review checklist:

- The browser sends the same content type that the backend used while signing.
- Any signed request headers returned by the backend are sent exactly once.
- The upload is treated as incomplete until the backend confirmation endpoint verifies object metadata.
- Browser testing is served from an HTTP origin such as `http://localhost:8000`, not a local file page.
