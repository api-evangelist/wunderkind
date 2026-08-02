---
name: Subscribe and unsubscribe users
description: >-
  Use the Wunderkind UCRM API to subscribe or unsubscribe lists of email
  addresses for a website ID.
api: openapi/wunderkind-ucrm-openapi.yml
operations:
  - external_subscribe
  - external_unsubscribe
---

# Subscribe and unsubscribe users

## Auth

Private API key created in Wunderkind Platform for the **UCRM** API
product, sent as `Authorization: apikey {apikey}`.

## Steps

1. **Subscribe** — `external_subscribe`
   (`PUT https://api.wunderkind.co/ucrm/v1/subscribe`). Body:
   `{"website_id": "1234", "id_type": "email", "ids": ["a@example.com"]}`.
   `ids` is required; `id_type` currently supports only `email`.
2. **Unsubscribe** — `external_unsubscribe`
   (`PUT https://api.wunderkind.co/ucrm/v1/unsubscribe`), same body shape.

## Rules

- Success is **201**, and any ids that failed are returned in the response
  body — always check it; a 201 is not proof every id was processed.
- 400 = bad request, 401 = bad/missing key, 500 = internal error.
- Both operations are state-setting (subscribe/unsubscribe to a target
  state), so re-running the same request is safe in practice, but there is
  no documented idempotency-key contract.
- These operations change consent state for real users — only call them
  with explicit user intent, and prefer unsubscribe requests to be
  processed promptly and verifiably.
