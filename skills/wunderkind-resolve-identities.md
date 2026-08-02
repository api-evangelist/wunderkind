---
name: Resolve WKND device IDs to emails
description: >-
  Use the Wunderkind Identity API to resolve anonymous WKND device IDs to
  best-matched hashed emails in batch, and decrypt encrypted email hashes
  from realtime lookups with the client encryption key.
api: openapi/wunderkind-identity-openapi.yml
operations:
  - post_id-resolutionv1identitylookupbatch
  - post_id-resolutionv1identityenckey
---

# Resolve WKND device IDs to emails

## Auth

Create a private API key in Wunderkind Platform (Settings > API Credentials,
select the **id-resolution** API product; Admin or Manager role required).
Send it on every request as:

    Authorization: apikey {apikey}

Never use this key client-side. A 400 means the key is invalid or missing;
403 means the key is not authorized for this product.

## Steps

1. **Batch lookup** — `post_id-resolutionv1identitylookupbatch`
   (`POST https://api.wunderkind.co/id-resolution/v1/identity/lookup/batch`).
   Body: `{"website_id": "<your numeric site id>", "device_ids": [<one or
   more WKND device IDs>]}`. The 200 response is `results[]` of
   `{device_id, email}` where `email` is a SHA-256 hash of the lowercased
   address. A 404 means no identity matched; 429 means you are being rate
   limited — back off before retrying.
2. **Decrypt realtime results (only if needed)** — realtime ID lookup
   responses reference an `encryption_key_name`. Exchange it via
   `post_id-resolutionv1identityenckey`
   (`POST https://api.wunderkind.co/id-resolution/v1/identity/enckey`) with
   body `{"enc_key_name": "<name>"}`; the 200 response carries
   `key_base64`. Treat this key as secret material: fetch it server-side
   only, never log it. See
   https://developer.wunderkind.co/reference/encryption-key-usage for
   interpreting responses.

## Rules

- Emails come back hashed (SHA-256, lowercased input) — match them against
  your own hashed CRM emails; you cannot reverse them.
- Error envelope on this API is `{"name": "..."}` (no RFC 9457).
- No idempotency contract: lookups are read-only and safe to retry.
