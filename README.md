# mgPass documentation

Source for [docs.mgpass.net](https://docs.mgpass.net) — the developer documentation for the **mgPass** platform.

mgPass is a unified identity, rewards, and subscriptions stack built on Cloudflare. This site covers:

- **Identity** — OAuth 2.0 + OIDC, sessions, MFA, SMS OTP, social login, SSO, RBAC
- **Rewards** — points ledger, tiers, benefits, cashback, catalog redemption
- **Subscriptions** — plans, hosted and embed checkout, billing, entitlements, promo codes, trials, seats, notifications, multi-currency (GHS/USD), webhooks

The docs are built with [Mintlify](https://mintlify.com) and deploy automatically on every push to `main`.

## What's in this repo

| Path | What lives here |
| --- | --- |
| `docs.json` | Navigation, theme, top-level Mintlify config |
| `guides/` | Conceptual + how-to pages |
| `api-reference/` | Endpoint reference, grouped by capability |
| `openapi.yaml` | OpenAPI spec used to power some reference pages |
| `images/`, `logo/` | Site assets |
| `AGENTS.md` | Project rules — terminology, style, content boundaries. **Read this before editing.** |
| `CONTRIBUTING.md` | Contributor quickstart |

## Local development

Install the Mintlify CLI once:

```bash
npm i -g mint
```

From the repo root (where `docs.json` lives):

```bash
mint dev              # preview at http://localhost:3000
mint broken-links     # link checker — must pass before merging
mint validate-openapi # only if you touched openapi.yaml
```

If the dev server gets weird, `mint update` to pull the latest CLI.

## AI-assisted writing

If you use Claude Code, Cursor, Windsurf, or another agentic IDE, install the Mintlify skill so the tool understands components and writing standards:

```bash
npx skills add https://mintlify.com/docs
```

Then read [`AGENTS.md`](./AGENTS.md) — it captures the mgPass-specific rules an AI tool won't know from generic Mintlify training (v3 Application terminology, capability model, currency conventions, content boundaries).

## Contributing

See [`CONTRIBUTING.md`](./CONTRIBUTING.md) for the contributor flow, then [`AGENTS.md`](./AGENTS.md) for the substantive style and terminology rules.

## Need help?

- Mintlify CLI issues — `mint update`, then re-run
- 404 on a page locally — confirm you're running from the directory that contains `docs.json`
- Platform / API questions — file an issue on the platform repo, not this one
