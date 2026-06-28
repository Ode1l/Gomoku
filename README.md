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

## Static Deployment

Build the application with Vite:

```bash
pnpm build
```

The deployable static site is written to `dist/`. It can be served by any static
host without a Worker runtime.

For Cloudflare Pages, use:

```text
Build command: pnpm build
Build output directory: dist
```
