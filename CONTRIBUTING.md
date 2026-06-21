# Contributing to mgPass documentation

Thanks for helping improve [docs.mgpass.net](https://docs.mgpass.net). This guide gets you set up; [`AGENTS.md`](./AGENTS.md) covers the substantive rules — read it before you write.

## How to contribute

### Option 1: Edit on GitHub

1. Open the page on docs.mgpass.net and click **Edit this page**.
2. Make your change in the GitHub web editor.
3. Open a pull request against `main`.

Good for typos, link fixes, small clarifications.

### Option 2: Local development

1. Fork and clone this repo.
2. Install the Mintlify CLI: `npm i -g mint`.
3. Create a branch: `git checkout -b docs/<short-name>`.
4. Make your changes.
5. From the repo root, run `mint dev` and preview at `http://localhost:3000`.
6. Run `mint broken-links` and fix anything it reports.
7. Commit and open a pull request.

## v3 terminology — the bit you must get right

mgPass v3 unified every integration under a single concept: the **Application**. One Application row (the spine) has zero or more **capabilities** attached (OAuth, Rewards, Subscriptions). The legacy `oauth_clients`, `rewards_partners`, and `subscription_tenants` entities are gone.

Quick rules:

- Use **Application**, never **client**, **tenant**, or **partner** as an entity name.
- Use **the X capability** (e.g. "the Subscriptions capability"), never **the X tenant** or **the X partner**.
- The OAuth parameter `client_id` is still spelled `client_id` — that's the standard OAuth 2.0 field. Its value is now an `application_id`.
- `application_id` is the universal foreign key across the API. If you see `partner_id` or `tenant_id` in an existing page, fix it.

The full term-by-term table, forbidden legacy terms, and rationale live in [`AGENTS.md`](./AGENTS.md#terminology).

## Writing guidelines

- **Active voice, second person**: "Run the command" / "You can…". Not "The command should be run" or "We will…".
- **Sentence case headings**: "Create a subscription", not "Create A Subscription".
- **One idea per sentence**. Break long sentences.
- **Lead with the goal**: "To rotate a webhook secret, …" not "The rotation endpoint accepts …".
- **Realistic IDs in examples**: `app_3news`, `plan_pro_monthly`, `sub_01HXR…` — never `foo`/`bar`/`123`.
- **Money in minor units** with a unit comment: `"amount": 5000, // GHS 50.00 in pesewas`. Default to GHS; show USD as the alternative.
- **Cross-link** on first mention of any concept. Don't re-explain — link.

Full style guidance, Mintlify component crib sheet, and content boundaries are in [`AGENTS.md`](./AGENTS.md).

## Pull request checklist

Before requesting review:

- [ ] `mint broken-links` passes
- [ ] `mint validate-openapi` passes (if you touched `openapi.yaml`)
- [ ] No legacy terms (`tenant`, `partner_id`, `oauth_clients`, etc.) introduced
- [ ] Code samples use realistic IDs and minor-unit money
- [ ] New concepts link to their primary guide on first mention
- [ ] You did **not** touch `docs.json` unless the PR is specifically a navigation change

## Code of conduct

Be kind, be specific, assume good faith. Issues and PRs that violate community norms will be closed without discussion.
