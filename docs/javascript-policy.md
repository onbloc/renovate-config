# JavaScript dependency policy

Use the [JavaScript preset](../javascript.json) alongside the [common preset](common-policy.md).

## npm updates

- Preserve existing pinned and ranged dependency declarations through `rangeStrategy: "replace"`.
- Wait seven days after release for ordinary npm Major, Minor, and Patch updates. Pinning, replacement, and lockfile operations retain the [upstream release-age preset's exceptions](https://docs.renovatebot.com/presets-security/#securityminimumreleaseagenpm). A release delay is not a guarantee that a package is safe.

## Non-major update groups

- **Lint and format tooling:** ESLint, `@eslint-react/eslint-plugin`, `@stylistic/eslint-plugin`, typescript-eslint, Prettier, and lint-staged. `eslint-config-next` and `eslint-plugin-storybook` are excluded from this group.
- **Test tooling:** Jest, ts-jest, and Testing Library.
- **Gno JavaScript clients:** `@gnolang/gno-js-client`, `@gnolang/tm2-js-client`, and `@gnolang/tm2-rpc` updates within 2.x, the RPC major line used by `tm2-js-client` 3.3.0. Other RPC major lines remain separate.
- **React Hook Form:** `react-hook-form` and `@hookform/resolvers`.

Major tooling updates retain their separate Dependency Dashboard approval path.
