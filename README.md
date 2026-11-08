# Onbloc Renovate Config

Shared Renovate presets for Onbloc repositories. This repository defines common update policies and JavaScript and Go rules. Product-specific version constraints, dependency exceptions, and deployment policies belong in the consuming repository.

## Usage

Install Renovate for the consuming repository and allow it to read this preset repository. Add a `renovate.json` file to the consuming repository's default branch.

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

Mixed repositories can extend both ecosystem presets after the common preset. The ecosystem presets do not implicitly extend the common preset.

These examples track this repository's default branch. Changes take effect on the next Renovate run. After a version tag has been published, consumers can pin an existing tag, for example `github>onbloc/renovate-config:javascript#v1.0.0`.

### Access

While this repository is private, Renovate must have permission to read it. Grant the Onbloc Renovate installation access to both this repository and the consuming repository, and verify preset resolution in the onboarding PR.

Do not commit tokens, registry credentials, or other secrets to presets or repository configuration. Configure any required credentials in the Renovate execution environment. This repository contains policy only and does not activate Renovate in product repositories.

## Presets

| Preset | Policy |
| --- | --- |
| [`default`](default.json) | `config:recommended`, no automerge, Dependency Dashboard, approval before ordinary major-update PRs, and commit SHA pinning for GitHub Actions |
| [`javascript`](javascript.json) | Preserve existing pinned and ranged npm/Yarn/pnpm declarations; wait seven days for ordinary npm major, minor, and patch releases |
| [`go`](go.json) | Run `go mod tidy` after module updates, limit indirect dependency updates to security fixes, and require approval for Go minimum-version and toolchain changes |

The common schedule permits ordinary updates on Mondays from 09:00 to 18:00 in `Asia/Seoul`. Ordinary PR limits are three concurrent PRs and two PRs per hour. This controls update creation, not how often the bot executes.

- Dashboard approval controls PR creation. Required reviews and merge permissions must be enforced separately through branch protection or rulesets.
- Test-directory manifests remain in scope. Installed dependencies, vendored code, and generated Yarn caches and editor SDKs are excluded.
- Package installation scripts are ignored, and whole-lockfile maintenance is disabled. Artifact updates needed for individual dependency changes are still allowed.
- No common manager allowlist is set, so composing ecosystem presets does not disable unrelated managers.
- The Go preset does not force the minimum Go version to the latest release. It does not enable `gomodTidyAll`, import-path rewrites, or replacement massaging.
- Additional Docker, Helm, Terraform, dependency exclusion, `resolutions`, fork, and branch rules are repository-specific. Omitting a dedicated preset does not disable a manager that Renovate normally enables.

## Security updates

The default remediation path uses GitHub vulnerability alerts:

1. Enable Dependency graph and Dependabot alerts in the consuming repository.
2. Grant Renovate read access to Dependabot alerts.
3. Verify manifest and lockfile recognition and actual security-fix PR creation.
4. Disable Dependabot version-update and security-update PR creation only after the replacement has been verified. Keep Dependency graph and Dependabot alerts enabled.

Recognized vulnerability fixes bypass the ordinary schedule, release-age delay, and Dashboard approval. They also bypass ordinary PR limits by default. Automerge remains disabled. Processing occurs on a subsequent Renovate run; this is not a real-time detection or deployment guarantee.

The experimental `osvVulnerabilityAlerts` option is not enabled. A seven-day release delay does not establish that a package is safe or cover every transitive dependency. Pinning, replacement, and lockfile-related operations retain the upstream preset's age-check exceptions. If a malicious version has already executed, dependency updates do not replace incident response for affected environments and credentials.

## Validation

Use Node.js 24.x, version 24.11 or later, to run the pinned validator:

```sh
npm exec --yes --ignore-scripts --package=renovate@44.188.0 -- \
  renovate-config-validator --no-global --strict \
  default.json javascript.json go.json
```

`--ignore-scripts` prevents lifecycle scripts during validator installation. In environments without the native RE2 module, validation may fall back to JavaScript regular expressions.

Schema validation does not prove runtime behavior. Use onboarding or `renovate/reconfigure` to inspect preset resolution, extracted dependencies, lockfile changes, and CI results in a consuming repository. Confirm that security fixes are not blocked by ordinary update policies.

## References

- [Shareable config presets](https://docs.renovatebot.com/config-presets/)
- [Config validation](https://docs.renovatebot.com/config-validation/)
- [GitHub vulnerability alerts](https://docs.renovatebot.com/configuration-options/#vulnerabilityalerts)
- [npm release-age checks and exceptions](https://docs.renovatebot.com/presets-security/#securityminimumreleaseagenpm)
- [Go modules](https://docs.renovatebot.com/modules/manager/gomod/)
