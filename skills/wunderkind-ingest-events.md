---
name: Ingest server-side behavioral events
description: >-
  Use the Wunderkind Event Ingestion API to send server-side behavioral
  events (visits, page views, carts, orders, identification) that feed
  Wunderkind's identity graph and triggered campaigns.
api: openapi/wunderkind-event-ingestion-openapi.yml
operations:
  - Service_Visit
  - Service_Pageview
  - Service_AddToCart
  - Service_EmptyCart
  - Service_OrderTransaction
  - Service_UserIdentified
  - Service_TrackLink
  - Service_Custom
---

# Ingest server-side behavioral events

## Auth

Server-side events are sent to `https://api.wunderkind.co/events/*`; see
https://developer.wunderkind.co/docs/authenticate-api-requests and the
server-side tracking guide
(https://developer.wunderkind.co/docs/server-side-tracking-implementation)
for credential provisioning.

## Steps

1. **Session start** — `Service_Visit` (`POST /events/visit`) when a
   user session begins.
2. **Browse** — `Service_Pageview` (`POST /events/page-view`) on every
   page view; include `item`, `category`, or `search` details blocks when
   the page has them.
3. **Cart** — `Service_AddToCart` (`POST /events/add-to-cart`) when items
   are added; `Service_EmptyCart` (`POST /events/empty-cart`) only when a
   user with items empties their cart (not on every empty-cart open).
4. **Purchase** — `Service_OrderTransaction`
   (`POST /events/order-transaction`) on completed orders, with `total`,
   `shipping`, `tax`, `discounts` (Money) and `items[]` line items. Send it
   **in addition to** the page-view event.
5. **Identify** — `Service_UserIdentified`
   (`POST /events/user-identified`) whenever a user is identified; include
   as much profile data as possible.
6. **Deep links / custom** — `Service_TrackLink` (`POST /events/track-link`)
   for deep links opened into a mobile app (resolve shortened URLs to their
   final form first); `Service_Custom` (`POST /events/custom`) for named
   custom events.

## Rules

- Every event embeds the `identification` block (device, session, user,
  source, profile, location) — populate it consistently; it is how events
  join Wunderkind's identity graph.
- Failures return a grpc-gateway `rpcStatus` envelope
  `{code, message, details[]}` as the `default` response.
- Events are fire-and-forget facts with no idempotency contract; avoid
  replaying old event batches, since duplicates skew triggering.
