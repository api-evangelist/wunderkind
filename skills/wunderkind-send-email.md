---
name: Send an email through Wunderkind
description: >-
  Use the Wunderkind Email API to send HTML or plain-text email to
  subscribers of a Wunderkind website ID.
api: openapi/wunderkind-email-openapi.yml
operations:
  - sendEmail
---

# Send an email through Wunderkind

## Auth

Bearer token (JWT): `Authorization: Bearer <token>`.

## Steps

1. **Send** — `sendEmail` (`POST https://api.wunderkind.co/email/send`).
   Required body fields: `to_emails` (1-100 addresses), `subject`, `from`,
   `body` (HTML or plain text). Optional: `from_name`, `reply_to`.

## Rules

- Recipients must be **subscribed** to the website ID configured by
  Wunderkind; unsubscribed addresses are **silently ignored** — a Success
  response does not guarantee delivery to every recipient.
- The sender address must be verified for the website ID.
- Rate limit: 1000 requests per minute per API key; there are no
  rate-limit response headers, so meter client-side.
- Responses: 200 `{status_code: "Success"|"Error", message}`; errors use
  `{error: {message, detail}}` with 400 (bad input), 401 (bad token),
  422 (validation), 500.
- No idempotency-key contract: a retried request can double-send. Retry
  only on network failure where no response was received, and log the
  request payloads you send.
