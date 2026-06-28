# Gomoku Handoff Context

## Goal

This repository is the standalone Gomoku game package for `p2p-lockstep-kit`.
It must remain independent from the UI repository's directory structure and
must be installable, testable, buildable, and publishable on its own.

Repository path:

```text
/Users/ode1l/Documents/Jetbrain/WebStorm/Gomoku
```

Related sibling repositories:

```text
../p2p-lockstep-kit-ui
../p2p-lockstep-kit-session
../p2p-lockstep-kit-network
```

Do not move Gomoku back into `p2p-lockstep-kit-ui/packages`.

## Package Boundary

Package name:

```text
p2p-lockstep-kit-gomoku
```

The package owns:

- Gomoku rules and move validation.
- Board reconstruction from session history.
- Canvas board rendering and winner dialog.
- The narrow `mountGomokuDemo()` integration helper.
- Gomoku-specific CSS in `style.css`.

The package does not own:

- Signaling or WebRTC connections.
- Session creation or lifecycle.
- Ready, start, undo, restart, approve, or reject controls.
- The generic lobby/game shell.

Those responsibilities stay in `p2p-lockstep-kit-ui` and
`p2p-lockstep-kit-session`.

## Runtime Contract

Gomoku consumes a narrow structural runtime in `src/demo.ts`:

```ts
type GomokuRuntime = {
  setGamePlugin(plugin: IGamePlugin): void;
  actions: {
    move(data: unknown): void;
  };
  observer: {
    subscribe(observer: GomokuRuntimeObserver): () => void;
    getSnapshot(): GameStateSnapshot | null;
  };
};
```

Do not expose or duplicate network/session internals in this interface.

Canonical session types such as `GameState`, `GameStateSnapshot`,
`IGamePlugin`, `PlayerLabel`, and `TurnEntry` come from
`p2p-lockstep-kit-session`. Do not redefine them locally.

## Source Layout

```text
src/game.ts        Gomoku rules, move types, history reconstruction, plugin
src/game.test.ts   Rule validation tests
src/board-view.ts  Canvas board and winner dialog
src/demo.ts        Narrow UI runtime integration
src/index.ts       Public package exports
style.css          Package-owned board styles
```

`src/main.ts` was intentionally removed. It was a UI-demo bootstrap file that
was excluded from builds and created a hidden repository dependency.

## TypeScript and ESM

`tsconfig.json` is fully standalone. It must not extend a file outside this
repository.

The project uses:

```text
module: NodeNext
moduleResolution: NodeNext
target: ES2022
strict: true
```

Local TypeScript imports use explicit `.js` suffixes. This is intentional:
TypeScript resolves them to `.ts` during development and emits valid Node ESM
imports in `dist`.

Do not revert to extensionless relative imports. They build successfully but
fail at runtime with `ERR_MODULE_NOT_FOUND` when importing the published ESM.

## Styling

Gomoku no longer relies on Tailwind scanning from the UI repository.
`board-view.ts` uses semantic `gomoku-board__*` classes and the package exports:

```ts
import "p2p-lockstep-kit-gomoku/style.css";
```

Consumers normally load both UI and game styles:

```ts
import "p2p-lockstep-kit-ui/style.css";
import "p2p-lockstep-kit-gomoku/style.css";
```

CSS variables from the UI have fallback values in the Gomoku stylesheet.

## Commands

```bash
pnpm install
pnpm typecheck
pnpm test
pnpm build
npm publish --dry-run
node -e "import('./dist/index.js').then(() => console.log('package import passed'))"
```

Last verified results:

- TypeScript typecheck passed.
- All 5 Vitest tests passed.
- Declaration and JavaScript build passed.
- Direct Node ESM import passed.
- npm publish dry-run included `dist`, `README.md`, and `style.css`.

## Dependencies

`p2p-lockstep-kit-session@^0.1.11` and `p2p-lockstep-kit-ui@^0.1.0` are both
peer dependencies and development dependencies. Session version `0.1.11` is
required because Gomoku imports the tightened public session types introduced
in that release. The UI peer dependency makes the host integration requirement
explicit while keeping Gomoku independent from the UI implementation.

## Git State Note

The repository was initialized and files were staged before cleanup.
`git status` may show:

```text
AD src/main.ts
```

This means the file was staged as added and then intentionally deleted. Before
the first commit, run `git add -A` so the index reflects the final repository.
IDE files are ignored by `.gitignore` but may also have been staged before that
rule was added; remove them from the index without deleting local IDE settings.

## Engineering Rules

- Prefer first-principles, minimal interfaces over adapters and abstractions.
- Keep the game package independent from concrete UI and network instances.
- Treat session history as the canonical board source.
- Preserve mobile and desktop behavior when changing board layout.
- Add or update rule tests for game-state changes.
- Run typecheck, tests, build, ESM import, and npm dry-run before publishing.
