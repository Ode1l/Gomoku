# P2P Lockstep Gomoku

Gomoku package for `p2p-lockstep-kit-ui`.

It provides the Gomoku rules plugin, board view, and a small mount helper for
`<p2p-lockstep-app>`.

## Usage

```ts
import "p2p-lockstep-kit-ui";
import "p2p-lockstep-kit-ui/style.css";
import "p2p-lockstep-kit-gomoku/style.css";
import { mountGomokuDemo } from "p2p-lockstep-kit-gomoku";

await customElements.whenDefined("p2p-lockstep-app");

const app = document.querySelector("p2p-lockstep-app");
const mount = app?.getBoardHost();
const runtime = app?.getRuntime();

if (mount && runtime) {
  mountGomokuDemo({ mount, runtime });
}
```

## Development

```bash
pnpm install
pnpm typecheck
pnpm test
pnpm build
```
