# Documentation project instructions

## Project context

This repo is the source for **docs.mgpass.net**, the developer documentation for the mgPass platform: identity (OAuth + sessions + MFA), rewards (points, tiers, cashback, catalog redemption), and subscriptions (plans, billing, entitlements, embed widget). The site is built with [Mintlify](https://mintlify.com) and auto-deploys on every push to `main`.

The platform itself lives in a separate repo. This site documents the **public API contract** and integration patterns — not internal schema or admin-console internals.

Key hosts referenced throughout the docs:

- `auth.mgpass.net` — auth-worker (OAuth, management API, tenant v1 API, SDK bundles, hosted UI)
- `admin.mgpass.net` — admin console
- `mgpass.net` — user-facing account portal
- `pay.mgpass.net` — hosted checkout
- `docs.mgpass.net` — this site

## v3 data model — the one thing to internalize

In v3, every integration is an **Application** (one row in the `applications` table — the "spine") with zero or more **capabilities** attached:

- **OAuth capability** — issues access tokens, manages redirect URIs, scopes, custom domains
- **Rewards capability** — points ledger, tiers, benefits, catalog
- **Subscriptions capability** — plans, billing, entitlements, hosted/embed checkout

A capability is **present if its row exists**, not toggled by a boolean. Enabling a capability inserts the row; disabling deletes it. One Application can have all three, any combination, or none.

`application_id` is the universal foreign key end-to-end (DB, services, API JSON, admin URLs). Every management endpoint is mounted at `/api/applications/*`.

## Terminology

Use these terms exactly. Consistency matters more than variety.

| Use | Don't use |
| --- | --- |
| Application | client, tenant, partner |
| Application's OAuth capability | OAuth client (as an entity) |
| Application's Rewards capability | rewards partner, partner account |
| Application's Subscriptions capability | subscription tenant, billing tenant |
| spine row / Application row | parent row, root row |
| capability row | child row, sub-row |
| sponsored subscription | comped sub, free sub, gifted sub |
| embed widget | embedded checkout SDK, JS SDK (ambiguous) |
| hosted checkout | redirect checkout, Stripe-style checkout |
| seat | member slot, license slot |
| entitlement | feature flag (these are not the same thing) |
| minor units (pesewas, cents) | smallest unit, base unit |

A few subtleties:

- The OAuth term **`client_id`** as a field name is fine — it's the standard OAuth 2.0 parameter. The v3 change is that the value of `client_id` is now always an `application_id`. Don't rename the parameter; do explain the equivalence the first time it appears on a page.
- `client_secret_hash`, `secret_hash`, `api_key_hash` are all fine as field names.
- "The X capability" or "the Application's X capability" — never "the X tenant" or "the X partner".

## Forbidden legacy terms

These come from pre-v3 docs and code. If you encounter them in existing files, fix them as part of your change:

- `oauth_clients`, `rewards_partners`, `subscription_tenants` (tables no longer exist)
- `partner_id`, `tenant_id` (replaced by `application_id` everywhere)
- "create a client", "register a partner", "provision a tenant" → "create an Application" / "add the X capability"
- "the OAuth tab on a client" → "the OAuth capability on an Application"

## Style preferences

- Active voice, second person ("you can…", "the API returns…"). Never "we will" or "Claude will".
- Sentence case for headings: "Create a subscription", not "Create A Subscription".
- Code formatting (backticks) for paths, commands, file names, env vars, field names, IDs.
- Bold for UI elements when an admin walkthrough is unavoidable: **Settings → API keys**.
- One idea per sentence. Break long sentences.
- Lead with the goal: "To rotate a webhook secret, …" not "The webhook secret rotation endpoint …".

### Examples and IDs

Use realistic, namespaced IDs in code samples — never `foo`, `bar`, `123`:

- Application: `app_3news`, `app_streamly`, `app_acme`
- Plan: `plan_pro_monthly`, `plan_family_annual`
- Subscription: `sub_01HXR…`
- Checkout session: `cos_01HXR…`
- Invoice: `inv_01HXR…`
- Promo code: `promo_LAUNCH50`
- Webhook delivery: `whd_01HXR…`

### Money

- Always **minor units** in code, with a comment showing the human amount and currency:
  ```json
  {
    "amount": 5000,        // GHS 50.00 in pesewas
    "currency": "GHS"
  }
  ```
- Default to **GHS** in primary examples. Show **USD** as the alternative in a separate tab or follow-up snippet.
- Every money-bearing webhook event payload carries both `amount` and `currency`. Document them together.

### Cross-linking

Cross-link aggressively. The first mention of a concept on any page should link to its primary guide. Don't re-explain — link. Examples:

- First mention of "Application" → link to `/guides/applications`
- First mention of "entitlement" → link to the entitlements guide
- First mention of an event type → link to the webhooks event reference

## Content boundaries

- Document **shipped** behavior. The Subscriptions Engine ships Phases 1, 2, and 3 (plans, billing, entitlements, promo codes, trials, seats, notifications, webhooks, embed widget, sponsored subs, multi-currency, upgrade proration preview).
- Hubtel MoMo Repeat Payments are **deferred** — mention as a known limitation on the relevant guide ("Card-only for now; MoMo recurring billing is on the roadmap"), don't write integration docs for it.
- **Admin console internals** (screen-by-screen walkthroughs of admin.mgpass.net) belong in the admin runbook, not here. Document admin **API endpoints** freely.
- **Internal D1 schema** belongs in the platform repo's `CLAUDE.md`. This site describes the API contract, not the database.
- **Auth methods on the API** — three of them, document the right one per endpoint:
  - **OAuth Bearer access_token** — account self-service (`/api/account/*`), userinfo, OAuth grants
  - **Admin Bearer token** — management API (`/api/applications`, `/api/users`, `/api/subscriptions`, `/api/rewards/*`, etc.)
  - **X-API-Key** — tenant-facing v1 API (`/v1/checkout`, `/v1/plans`, `/v1/subscriptions`, `/v1/promo-codes`, `/v1/notifications`, `/api/partner/events`)

## Mintlify components — crib sheet

Use components freely. Pick the one that fits the shape of the content.

| Component | When to use |
| --- | --- |
| `<Steps>` / `<Step>` | Sequential setup, ordered procedures (3+ steps) |
| `<Tabs>` / `<Tab>` | Same content in multiple variants (curl / TypeScript / Python, GHS / USD) |
| `<CodeGroup>` | Multi-language code samples on API ref pages |
| `<CardGroup>` / `<Card>` | Top-of-funnel landing pages, "where to go next" |
| `<ParamField>` | Request fields on API ref pages — **API ref only**, not in guides |
| `<ResponseField>` | Response fields on API ref pages — **API ref only**, not in guides |
| `<Accordion>` / `<AccordionGroup>` | FAQ-style collapsibles, optional deep dives |
| `<Note>` | Context the reader should know but isn't critical |
| `<Tip>` | A shortcut or recommended approach |
| `<Warning>` | Footguns, irreversible actions, money-losing mistakes |
| `<Info>` | Neutral callouts ("This endpoint requires admin scope") |
| `<Check>` | Positive confirmations in checklists |
| `<Frame>` | Wrapping a screenshot (this refresh ships none) |

### API reference frontmatter

API ref pages need three frontmatter keys:

```mdx
---
title: 'Create application'
description: 'Create a new Application spine row in mgPass.'
api: 'POST /api/applications'
---
```

Body fields use `<ParamField path="…" type="string" required>…</ParamField>`. Responses use `<ResponseField name="…" type="object">…</ResponseField>`. Wrap code samples in `<CodeGroup>` with `curl` and `typescript` tabs at minimum.

## File ownership and PRs

- Each docs refresh agent owns a disjoint set of files. **Do not edit `docs.json`** unless your task explicitly says so — navigation changes are owned by a single agent.
- If you spot a gap outside your scope, leave it in the PR description and move on. Don't broaden the change.
- Commit format: `docs: <scope> — <short summary>`. One commit per workstream.
- All commits include `Co-Authored-By: Claude Opus 4.7 (1M context) <noreply@anthropic.com>`.

## Local validation

Before pushing:

```bash
mint dev              # local preview at http://localhost:3000
mint broken-links     # link validator — must pass
mint validate-openapi # only if you touched openapi.yaml
```

If `mint` is missing, install with `npm i -g mint`. Run `mint update` if the dev server misbehaves.
