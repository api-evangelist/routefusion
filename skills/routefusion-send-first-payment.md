---
name: Send your first cross-border payment
description: Onboard a business entity, open a wallet, register a beneficiary, quote an FX rate, and execute a cross-border transfer with the Routefusion GraphQL API.
api: https://external.routefusion.com/graphql
operations:
  - createBusinessEntity
  - finalizeEntity
  - createWallet
  - resourceCorridorsV2
  - beneficiaryRequiredFieldsV2
  - createBusinessBeneficiary
  - getTransferQuote
  - createTransfer
  - finalizeTransfer
---

# Send your first cross-border payment (Routefusion)

Routefusion is a GraphQL API. All calls go to a single endpoint:

- Sandbox: `https://sandbox.external.routefusion.com/graphql`
- Production: `https://external.routefusion.com/graphql`

## Auth

Send every request with `Authorization: Bearer ${token}`. Tokens are issued by
Routefusion (email customersuccess@routefusion.com). Use a sandbox token while
testing. Some admin operations require the calling user to have the `admin` role.

## Steps

1. **Create the paying entity** — call the `createBusinessEntity` mutation (or
   `createPersonalEntity`) with the business details. Use `entityRequiredFields`
   first to learn what a given jurisdiction requires.
2. **Finalize the entity** — call `finalizeEntity`. This submits the entity to
   Routefusion's compliance review; it immediately moves to a pending state.
3. **Open a wallet** — call `createWallet` on the entity, then
   `walletFundingInstructions` to learn how to fund it. In sandbox, use
   `addBalanceToWallet` to add test funds.
4. **Discover the corridor** — call `resourceCorridorsV2` to see which
   countries/currencies/payment methods are available, then
   `beneficiaryRequiredFieldsV2` for the fields the destination corridor needs.
5. **Create the beneficiary** — call `createBusinessBeneficiary` (or
   `createPersonalBeneficiary`) with those required fields.
6. **Quote the rate** — call `getTransferQuote` to lock an FX rate for a limited
   time.
7. **Create then execute the transfer** — call `createTransfer` (this prepares
   but does NOT send), then `finalizeTransfer` to actually initiate it. This
   two-step create/finalize split is Routefusion's safety mechanism in lieu of an
   idempotency key — never assume a transfer is sent until `finalizeTransfer`
   succeeds.

## Track the outcome

Poll the `transfer` query or subscribe to the `transfer` webhook. States progress
Accepted → Finalized → Initiated → Processing → Sent → Completed; watch for
Returned, Failed, Canceled, or In Review. In sandbox, drive states with
`changeTransferState`.

## Errors

GraphQL errors come back in an `errors` array with `message`, `path`, and
`extensions.code` (e.g. `BAD_USER_INPUT`, `GRAPHQL_VALIDATION_FAILED`).
Validation errors return HTTP 200 with the failure inside `errors`; auth failures
return HTTP 401 `{"message":"unauthorized"}`.
