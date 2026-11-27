# Common dependency policy

The [common preset](../default.json) extends [`config:recommended`](https://docs.renovatebot.com/presets-config/#configrecommended), keeping Renovate's recommended ecosystem groups and workarounds.

| Area | Behavior |
| --- | --- |
| Ordinary updates | No shared weekly schedule; up to three concurrent PRs and two new PRs per hour, per repository |
| Ordinary Major updates | Require Dependency Dashboard approval before PR creation, except for grouped GitHub Action references |
| Merging | Automerge is disabled; use the repository's required reviews and CI checks |
| Rebasing | Automatically rebase only conflicting branches, including initial version-pinning updates; do not rebase solely because the base branch advances |
| Labels | Add `renovate` to all update PRs and `renovate-security` additionally to security-fix PRs, without replacing other configured labels |
| Security fixes | Separate, immediate PR creation without ordinary approval, release-age, schedule, or PR-limit gates; still no automerge |

These settings control update processing, not how frequently the hosted bot executes. Dashboard approval controls PR creation, not permission to merge.

If branch protection requires PRs to be up to date, a non-conflicting PR may still need a manual update before merging. Request one through the PR's rebase checkbox or Dependency Dashboard.

## Preset access

With the Mend Renovate App, a public repository cannot extend a private preset repository, even within the same organization. A public preset can be shared across organizations.

For private presets, the Renovate installation must have access to both the preset and consumer repositories. Access from a developer's GitHub account does not prove that the Renovate App can read the same preset. Do not merge a consumer configuration until preset access is available to Renovate.

Never commit tokens or registry credentials to preset files or consumer configuration. Configure private dependency credentials in the Renovate execution environment.

## GitHub Actions

The common group matches the `github-actions` manager's `action` dependency type. Action updates, including Major updates, do not require Dashboard approval but remain subject to ordinary PR limits and manual merging. Major and non-major groups stay separate. Runtime inputs such as `go-version`, reusable workflows, container images, and service images are not included.

The common preset keeps tag references such as `@v7` instead of forcing commit SHAs and does not impose a release-age delay on Action updates. Existing SHA-pinned references are not converted back to tags and can still receive digest updates. The [JavaScript preset's seven-day delay](javascript-policy.md) applies to npm updates, not Actions.

## Scope and local exceptions

Test-directory manifests remain in scope. Installed dependencies, vendored code, generated Yarn caches, and editor SDKs are excluded. Renovate package-manager operations ignore installation scripts, and whole-lockfile maintenance is disabled; artifact updates for individual dependencies are still allowed. These settings do not sandbox a consuming repository's CI.

No common manager allowlist is set. Extending a language preset does not disable other managers that Renovate normally enables. Keep product-specific version constraints, fork replacements, manager exclusions, and deployment schedules in the consuming repository.

## Enable security-fix PRs in a consumer

The common preset already enables [`vulnerabilityAlerts`](https://docs.renovatebot.com/configuration-options/#vulnerabilityalerts). GitHub security settings and Renovate permissions must still be configured separately for each consumer.

1. In the consumer repository's **Settings → Advanced Security**, enable **Dependency graph** and [**Dependabot alerts**](https://docs.github.com/en/code-security/how-tos/secure-your-supply-chain/secure-your-dependencies/configure-dependabot-alerts). Organization policy may manage these settings centrally.
2. In the installed Renovate App's configuration, confirm access to the consumer repository and **read access to Dependabot alerts**. An organization owner may need to approve permissions.
3. Under the consumer's **Security → Dependabot** page, check that GitHub recognizes the relevant manifests and lockfiles and reports any known vulnerable dependencies.
4. When a supported, fixable alert exists, verify that Renovate creates a security-fix PR on a subsequent run.

GitHub supplies the vulnerability alerts; Renovate proposes dependency fixes. This is not a real-time detection guarantee, and an alert without a resolvable fix may not produce a PR. Coverage depends on the recognized dependencies and supported ecosystems.

**Dependabot alerts are distinct from Dependabot security updates and version updates.** No `dependabot.yml` is required just to enable alerts. After verifying Renovate's actual fix-PR path, disable Dependabot's PR-generating features if needed to avoid duplicate PRs. Keep Dependency graph and Dependabot alerts enabled.

Security PRs can exceed the ordinary three-PR limit because the shared security PR limit is unlimited. They remain ungrouped and require manual merging. The experimental `osvVulnerabilityAlerts` option is not enabled.

If a malicious dependency has already executed, updating it does not replace incident response for affected environments and credentials.
