# Paystack deployment configuration

BOLD now uses the official Paystack REST API at `https://api.paystack.co/` with `Authorization: Bearer <PAYSTACK_SECRET_KEY>`.

## Required Netlify variables

Configure these in Netlify **Site configuration → Environment variables**. Do not commit live values or put them in the downloadable source archive.

- `PAYSTACK_SECRET_KEY` — server-only Paystack secret key.
- `PAYSTACK_PUBLIC_KEY` — Paystack public key for browser-side integrations.
- `PAYSTACK_BOLD_PRO_MONTHLY_PLAN_CODE` — Paystack recurring monthly plan code (`PLN_...`).
- `PAYSTACK_BOLD_PRO_ANNUAL_PLAN_CODE` — Paystack recurring annual plan code (`PLN_...`).
- `PAYSTACK_CURRENCY` — integration currency, default `NGN`.
- `PAYSTACK_EMAIL_TOKEN` — optional fallback token; the preferred value is persisted per subscription after Paystack creates it.

Optional amount fallbacks are `PAYSTACK_BOLD_PRO_MONTHLY_AMOUNT` and `PAYSTACK_BOLD_PRO_ANNUAL_AMOUNT`, expressed in the currency subunit. When a plan code is supplied, Paystack uses the plan amount.

## Provider behavior

- Checkout calls `POST /transaction/initialize` and redirects to Paystack’s hosted `authorization_url`.
- Subscription status verifies the transaction and then resolves the active Paystack subscription to persist its `subscription_code` and `email_token`.
- Cancellation calls `POST /subscription/disable` with the persisted subscription code and email token.
- Billing history calls `GET /transaction?customer=...` and stores Paystack references as receipts.
- The secret key is never returned to the browser or written into this archive.

The Paystack smoke test validates the configured live secret against the lightweight `GET /bank` endpoint before release.
