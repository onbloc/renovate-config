# JavaScript dependency policy

Use the [JavaScript preset](../javascript.json) alongside the [common preset](common-policy.md).

## npm updates

- Preserve existing pinned and ranged dependency declarations through `rangeStrategy: "replace"`.
- Wait seven days after release for ordinary npm Major, Minor, and Patch updates. Pinning, replacement, and lockfile operations retain the [upstream release-age preset's exceptions](https://docs.renovatebot.com/presets-security/#securityminimumreleaseagenpm). A release delay is not a guarantee that a package is safe.

## Non-major update groups

- **Lint and format tooling:** ESLint, typescript-eslint, Prettier, and lint-staged. `eslint-config-next` and `eslint-plugin-storybook` are excluded from this group.
- **Test tooling:** Jest, ts-jest, and Testing Library.

Major tooling updates retain their separate Dependency Dashboard approval path.
