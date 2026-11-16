# Onbloc Renovate Config

Shared Renovate presets for Onbloc repositories. Use them to keep dependency update policies consistent while retaining repository-specific exceptions locally.

This repository provides configuration only. It does not install Renovate, enable GitHub security features, or change branch protection in consumer repositories.

## Quick start

1. Install Renovate for the consuming repository and grant it access to that repository.
2. Confirm that Renovate can read this preset repository. See [Preset access](#preset-access).
3. Add one of the following configurations as `renovate.json` in the consuming repository. For onboarding, edit the existing Renovate onboarding PR; for an already onboarded repository, open a configuration-change PR.
4. Review the resolved configuration and update preview before merging into the branch Renovate uses for that repository.

### Common policy only

```json
{
  "extends": ["github>onbloc/renovate-config"]
}
```

### JavaScript

```json
{
  "extends": [
    "github>onbloc/renovate-config",
    "github>onbloc/renovate-config:javascript"
  ]
}
```

### Go

```json
{
  "extends": [
    "github>onbloc/renovate-config",
    "github>onbloc/renovate-config:go"
  ]
}
```

Mixed repositories can extend both `:javascript` and `:go` after the common preset. The language presets do not implicitly extend the common preset.

### Preset access

With the Mend Renovate App, a public repository cannot extend a private preset repository, even within the same organization. A public preset can be shared across organizations.

For private presets, the Renovate installation must have access to both the preset and consumer repositories. Access from a developer's GitHub account does not prove that the Renovate App can read the same preset. Do not merge a consumer configuration until preset access is available to Renovate.

Never commit tokens or registry credentials to preset files or consumer configuration. Configure private dependency credentials in the Renovate execution environment.

## Update policies

The [common preset](default.json) extends `config:recommended`, keeping Renovate's recommended ecosystem groups and workarounds.

| Area | Behavior |
| --- | --- |
| Ordinary updates | No shared weekly schedule; up to three concurrent PRs and two new PRs per hour, per repository |
| Ordinary Major updates | Require approval in the Dependency Dashboard before PR creation |
| Merging | Automerge is disabled; use the repository's required reviews and CI checks |
| Labels | Add `renovate` without replacing other configured labels |
| GitHub Actions | Pin commit digests; group Action references, separating Major from non-major updates |
| Security fixes | Separate, immediate PR creation without ordinary approval, release-age, schedule, or PR-limit gates; still no automerge |

These settings control update processing, not how frequently the hosted bot executes. Dashboard approval controls PR creation, not permission to merge.

### GitHub Actions grouping

The common group matches the `github-actions` manager's `action` dependency type. Runtime inputs such as `go-version`, reusable workflows, container images, and service images are not included in this group.

### JavaScript policy

The [JavaScript preset](javascript.json):

- Preserves existing pinned and ranged dependency declarations through `rangeStrategy: "replace"`.
- Waits seven days after release for ordinary npm Major, Minor, and Patch updates.
- Groups non-major lint and format tooling, including ESLint, typescript-eslint, Prettier, and lint-staged. `eslint-config-next` and `eslint-plugin-storybook` are excluded from that generic group.
- Groups non-major Jest, ts-jest, and Testing Library updates.

Major tooling updates retain their separate approval path. Pinning, replacement, and lockfile operations retain the upstream release-age preset's exceptions. A release delay is not a guarantee that a package is safe.

### Go policy

The [Go preset](go.json):

- Runs `go mod tidy` after module dependency updates.
- Limits standalone indirect dependency updates to security fixes. Indirect versions may still change as a consequence of direct upgrades and tidying.
- Requires separate Dashboard approval for minimum Go version and toolchain changes, without forcing the minimum Go version to the latest release.
- Groups non-major updates to `gorm.io/gorm`, `gorm.io/driver/mysql`, and `gorm.io/driver/postgres` for manual review.

The GORM group does not include observability plugins, raw SQL clients, or database server images. Grouping does not synchronize versions across repositories or guarantee compatibility. The preset does not enable `gomodTidyAll`, import-path rewrites, or replacement massaging; verify dependent module artifacts in multi-module repositories.

### Scope and local exceptions

Test-directory manifests remain in scope. Installed dependencies, vendored code, generated Yarn caches, and editor SDKs are excluded. Renovate package-manager operations ignore installation scripts, and whole-lockfile maintenance is disabled; artifact updates for individual dependencies are still allowed. These settings do not sandbox a consuming repository's CI.

No common manager allowlist is set. Extending a language preset does not disable other managers that Renovate normally enables. Keep product-specific version constraints, fork replacements, manager exclusions, and deployment schedules in the consuming repository.

## Enable security-fix PRs in a consumer

The common preset already enables `vulnerabilityAlerts`. GitHub security settings and Renovate permissions must still be configured separately for each consumer.

1. In the consumer repository's **Settings → Advanced Security**, enable **Dependency graph** and **Dependabot alerts**. Organization policy may manage these settings centrally.
2. In the installed Renovate App's configuration, confirm access to the consumer repository and **read access to Dependabot alerts**. An organization owner may need to approve permissions.
3. Under the consumer's **Security → Dependabot** page, check that GitHub recognizes the relevant manifests and lockfiles and reports any known vulnerable dependencies.
4. When a supported, fixable alert exists, verify that Renovate creates a security-fix PR on a subsequent run.

GitHub supplies the vulnerability alerts; Renovate proposes dependency fixes. This is not a real-time detection guarantee, and an alert without a resolvable fix may not produce a PR. Coverage depends on the recognized dependencies and supported ecosystems.

**Dependabot alerts are distinct from Dependabot security updates and version updates.** No `dependabot.yml` is required just to enable alerts. After verifying Renovate's actual fix-PR path, disable Dependabot's PR-generating features if needed to avoid duplicate PRs. Keep Dependency graph and Dependabot alerts enabled.

Security PRs can exceed the ordinary three-PR limit because the shared security PR limit is unlimited. They remain ungrouped and require manual merging. The experimental `osvVulnerabilityAlerts` option is not enabled.

If a malicious dependency has already executed, updating it does not replace incident response for affected environments and credentials.

## Contributing and license

For changes to these presets, validation commands, and Conventional Commit/PR requirements, see [CONTRIBUTING.md](CONTRIBUTING.md).

Licensed under the [MIT License](LICENSE).

## References

- [Renovate shared presets](https://docs.renovatebot.com/config-presets/)
- [Renovate vulnerability alerts](https://docs.renovatebot.com/configuration-options/#vulnerabilityalerts)
- [Configure GitHub Dependabot alerts](https://docs.github.com/en/code-security/how-tos/secure-your-supply-chain/secure-your-dependencies/configure-dependabot-alerts)
- [Renovate npm release-age policy](https://docs.renovatebot.com/presets-security/#securityminimumreleaseagenpm)
- [Renovate Go modules](https://docs.renovatebot.com/modules/manager/gomod/)
