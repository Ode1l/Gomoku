# P2P Gomoku

A browser-based peer-to-peer Gomoku application built with Vite and
`p2p-lockstep-kit-ui`.

## Development

```bash
pnpm install
pnpm dev
```

## Validation

```bash
pnpm typecheck
pnpm test
pnpm build
```

## Cloudflare Workers

The production site is emitted to `dist/` and configured as a Workers Static
Assets application in `wrangler.jsonc`.

```jsonc
{
  "$schema": "./node_modules/wrangler/config-schema.json",
  "name": "p2p-lockstep-gomoku",
  "compatibility_date": "2026-06-28",
  "assets": {
    "directory": "./dist",
    "not_found_handling": "single-page-application"
  }
}
```

Authenticate once, then deploy the application:

```bash
pnpm exec wrangler login
pnpm deploy
```

Cloudflare serves `dist/index.html` and the hashed Vite assets directly. No
Worker entry file, npm package publish step, or Pages-specific output directory
is required.
