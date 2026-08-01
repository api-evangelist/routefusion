---
name: Subscribe to and verify webhooks
description: Create a Routefusion webhook subscription, receive events, and verify the Ed25519 signature on every payload.
api: https://external.routefusion.com/graphql
operations:
  - createWebhookSubscription
  - subscriptions
  - disableWebhookSubscription
---

# Subscribe to and verify Routefusion webhooks

Routefusion pushes events to a URL you configure and signs every payload with
Ed25519 so you can confirm it came from Routefusion and was not altered.

## Steps

1. **Subscribe** — call the `createWebhookSubscription` mutation with your
   endpoint URL and the subscription type. Available types:
   `transfer`, `entity`, `incoming_transfer`, `wallet`, `beneficiary`, `rfi`.
2. **List subscriptions** — call the `subscriptions` query to see active
   subscriptions.
3. **Disable** — call `disableWebhookSubscription` to turn off a subscription.

## Verify the signature

Every delivery is signed with Ed25519. Verify the signature over the raw request
body against Routefusion's public key before trusting the event. See
https://docs.routefusion.com/reference/webhook-integrity-verification.

## Testing in sandbox

Use `changeTransferState` to drive a transfer through its states and fire the
`transfer` webhook, and `sandboxMockIncomingTransfer` to simulate an
`incoming_transfer` event.

## Payload shape

Each event carries a `subscription_type` discriminator plus the resource fields
(for `transfer`: id, state, source/destination amount+currency, rate, fee,
reference, expected_delivery_date, ...). See asyncapi/routefusion-webhooks.yml.
