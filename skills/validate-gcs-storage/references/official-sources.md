# Official Sources

Use these official sources before making claims about Google Cloud Storage behavior. Do not copy long passages into user output; summarize the relevant rule and link the source.

| Topic | Official source | Check before claiming |
| --- | --- | --- |
| Signed URLs | https://cloud.google.com/storage/docs/access-control/signed-urls | Supported operations, endpoint restrictions, expiration, and the security meaning of a temporary URL |
| V4 Signed URL helpers | https://cloud.google.com/storage/docs/access-control/signing-urls-with-helpers | Helper behavior, signing requirements, language examples, and required inputs |
| PUT Signed URL sample | https://cloud.google.com/storage/docs/samples/storage-generate-upload-signed-url-v4 | Required method, content type handling, and upload request shape |
| CORS | https://cloud.google.com/storage/docs/using-cors | Bucket CORS format, allowed origins, methods, request headers, response headers, and preflight behavior |
| Request preconditions | https://cloud.google.com/storage/docs/request-preconditions | Generation and metageneration semantics for create-only, stale-write, and stale-delete protection |
| Python Blob reference | https://cloud.google.com/python/docs/reference/storage/latest/google.cloud.storage.blob.Blob | `upload_from_file`, `download_as_bytes`, `reload`, `delete`, and `generate_signed_url` parameters |
| Application Default Credentials | https://cloud.google.com/docs/authentication/application-default-credentials | Local and deployed authentication behavior |
| Cloud Storage authentication | https://cloud.google.com/storage/docs/authentication | Auth approaches and credential expectations |

## Fact-Check Prompts

- For Signed URL bugs, verify the method, signed headers, expiration, content type, endpoint restrictions, and whether the URL is being treated as a temporary credential.
- For browser upload bugs, verify bucket CORS against the real `Origin`, request method, and request headers seen in browser Network details.
- For overwrite or delete safety, verify whether generation or metageneration preconditions are used at the SDK/API call boundary.
- For auth or permission bugs, separate local ADC discovery, deployed service account identity, and bucket/object IAM permissions.
- For Python SDK questions, check the current Blob reference for method names and parameter behavior before relying on memory.
