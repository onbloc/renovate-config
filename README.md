# Onbloc Renovate Config

Shared Renovate presets for Onbloc repositories. Use them to keep dependency update policies consistent while retaining repository-specific exceptions locally.

This repository provides configuration only. It does not install Renovate, enable GitHub security features, or change branch protection in consumer repositories.

## Quick start

1. Install Renovate for the consumer repository and [verify preset access](docs/common-policy.md#preset-access).
2. Add `renovate.json` to the branch Renovate uses. For a new consumer, edit its Renovate onboarding PR; for an existing consumer, use a configuration-change PR.
3. Review the resolved configuration and update preview before merging.

```json
{
  "$schema": "https://docs.renovatebot.com/renovate-schema.json",
  "extends": ["github>onbloc/renovate-config"]
}
```

Add `github>onbloc/renovate-config:go` or `github>onbloc/renovate-config:javascript` after the common preset as needed. Mixed repositories can add both. The language presets do not implicitly extend the common preset.

## Update policies

| Policy | Covers |
| --- | --- |
| [Common](docs/common-policy.md) | Update approvals, PR limits, GitHub Actions, and [security-fix setup](docs/common-policy.md#enable-security-fix-prs-in-a-consumer) |
| [Go](docs/go-policy.md) | Module maintenance, toolchain approvals, and dependency groups |
| [JavaScript](docs/javascript-policy.md) | npm release delay, version ranges, and tooling groups |

## Contributing and license

For changes to these presets, validation commands, and Conventional Commit/PR requirements, see [CONTRIBUTING.md](CONTRIBUTING.md).

Licensed under the [MIT License](LICENSE).
