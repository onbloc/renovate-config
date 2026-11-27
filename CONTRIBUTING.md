# Contributing

This guide covers changes to the shared presets. For setup and links to the dependency policies, see [README.md](README.md).

Write commits, PR titles, and PR descriptions in English. Keep contributions focused on one policy change and explain the effect on consuming repositories.

## Choose where the rule belongs

| Location | Use it for |
| --- | --- |
| `default.json` | Cross-language policy: approval, PR limits, security exceptions, labels, and Action grouping |
| `javascript.json` | npm/Yarn/pnpm declaration handling, npm release age, and JavaScript tooling groups |
| `go.json` | Go module maintenance, Go version approvals, and reusable Go dependency families |
| Consumer `renovate.json` | Product-specific version constraints, forks, exclusions, schedules, and deployment requirements |

A new shared group needs evidence of a coherent dependency family and repeat use. Match only the intended managers, packages, dependency types, and update types. Do not group unrelated runtime libraries merely to reduce PR counts.

Preserve existing safeguards unless the PR explicitly proposes changing them:

- Automerge stays disabled. Grouping or a non-major version number does not establish that an update is safe.
- Ordinary Major updates outside the Action group keep Dashboard approval. Action updates, including Major updates, do not require Dashboard approval; Major and non-major Action groups remain separate.
- Security fixes bypass ordinary schedules, release-age delays, approval, and PR limits without enabling automerge.
- A group must not accidentally enable ordinary indirect Go updates or capture unrelated drivers, plugins, or runtime inputs.
- Language presets remain additive and do not implicitly extend the common preset or restrict unrelated managers.

## Make the change

1. Start a short, descriptive branch from the current `main` branch, such as `docs/preset-usage` or `fix/security-approval`.
2. Reuse the existing preset structure. Prefer narrow package rules over a new preset unless multiple consumers need the distinction.
3. Update the relevant [dependency policy](README.md#update-policies) when behavior changes. Keep setup and policy links in README, policy details in `docs/`, and contribution procedures here.
4. Do not include credentials, internal service URLs, private ticket links, or sensitive package metadata in public commits or PRs.

## Validate

For preset or workflow changes, run the same strict validator as [CI](.github/workflows/validate.yml). From the repository root, with Docker available:

```sh
docker run --rm \
  --volume "$PWD:/workspace:ro" \
  --workdir /workspace \
  --entrypoint renovate-config-validator \
  ghcr.io/renovatebot/renovate:44.188.0@sha256:96ded8bde6a33719cb57b66878637dec03c4ec8c06e0d725e29883655dda6744 \
  --no-global --strict default.json javascript.json go.json
```

The workflow runs on PRs, pushes to `main`, and manual dispatch using a GitHub-hosted Ubuntu runner. It uses a digest-pinned image, a read-only checkout mount, read-only repository permissions, and no package registry token. Keep the command above aligned with the workflow if the validator version changes.

Strict validation does not prove consumer behavior. For a policy change, also inspect a representative consumer using onboarding or `renovate/reconfigure`:

- Resolve common+JavaScript, common+Go, and mixed compositions when affected.
- Check which dependencies match and which PRs are grouped. Confirm that Action Major updates bypass Dashboard approval while other ordinary Major updates still require it.
- Check security-fix exceptions using a supported alert when available. Authenticated developer access does not prove hosted Renovate access to a private preset.
- Review changed manifests, lockfiles, and `go.sum` files, including dependent modules connected by local `replace` directives.
- Confirm relevant consumer CI passes. Do not merge into production consumers solely to test a preset.

Report exactly what was checked and any remaining limits. For documentation-only changes, check relative links, JSON examples, command accuracy, and consistency with the current presets. Do not claim a consumer check was performed when only schema validation ran.

## Conventional Commits and PR titles

**Every commit subject and every PR title must follow [Conventional Commits 1.0.0](https://www.conventionalcommits.org/en/v1.0.0/).** PR descriptions use normal Markdown; the Conventional Commit format applies to their titles.

The subject format is:

```text
<type>[optional scope][!]: <description>
```

Put a scope in parentheses when present. Use a concise, imperative description. Common types are `feat`, `fix`, `docs`, `chore`, `ci`, `test`, `refactor`, `build`, `perf`, `style`, and `revert`. Useful scopes include `default`, `javascript`, and `go`.

Examples for both commit subjects and PR titles:

```text
feat(go): group non-major GORM updates
fix(default): preserve security update exemptions
docs: clarify preset access requirements
ci: validate presets on GitHub-hosted runners
```

For a backward-incompatible policy change, use `!` and/or a `BREAKING CHANGE:` footer. Explain the consumer impact and migration in the commit body and PR description:

```text
feat(default)!: change ordinary update approval policy

BREAKING CHANGE: Consumers must review the new approval requirements before adopting this release.
```

Keep independently reviewable changes in separate commits. Do not prefix a subject or PR title with a ticket number instead of its conventional type. Maintainers must also preserve a Conventional Commit subject for the final squash or merge commit.

## Open the PR

Target `main`. Use a Conventional Commit title and include:

- **Summary:** the problem and intended policy change.
- **Changes:** affected presets and the rationale for each rule.
- **Verification:** commands and consumer scenarios actually exercised, plus any unverified behavior.
- **Impact:** affected consumers, approval/security/grouping changes, and migration or rollback considerations.

State whether default-branch consumers pick up the change on their next Renovate run. Link only information reviewers can access; keep private operational context out of public descriptions. Do not create unrelated consumer changes as part of a preset-only PR.

## Releasing policy changes

Maintainers publish tags after review, successful CI, and representative consumer verification. Use SemVer-style tags such as `v1.0.0`:

- Patch: a correction that preserves the documented policy contract.
- Minor: a backward-compatible capability or optional preset.
- Major: a backward-incompatible policy change requiring consumer migration or coordination.

Document the effect in the release notes. Default-branch consumers receive merged changes without waiting for a tag; a tag does not gate their rollout. Tag-pinned consumers adopt a release by updating all shared preset references to the same version.

Never move or overwrite a published version tag. Publish a new version for corrections. Roll back a pinned consumer by selecting an earlier existing tag; handle a bad change on `main` with a reviewed revert or corrective PR rather than rewriting published history.

## License

Contributions are provided under the repository's [MIT License](LICENSE). Retain required notices for any third-party material you add.
