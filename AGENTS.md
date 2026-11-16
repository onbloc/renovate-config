# Agent guide

This repository contains shared Renovate configuration, not application code. `AGENTS.md` is the canonical agent guide; keep `CLAUDE.md` as a relative symlink to this file rather than a separate copy.

## Read first

- [README.md](README.md): consumer setup, preset behavior, access requirements, and security alerts.
- [CONTRIBUTING.md](CONTRIBUTING.md): rule placement, validation commands, Conventional Commits, PR requirements, and releases.
- [LICENSE](LICENSE): the repository's MIT license.

Use English for repository documentation, commit messages, PR titles, and PR descriptions. Both commit subjects and PR titles must follow Conventional Commits as specified in CONTRIBUTING.

## Repository layout

- `default.json`: cross-language update policy and Action grouping.
- `javascript.json`: JavaScript dependency ranges, release delay, and tooling groups.
- `go.json`: Go module maintenance, version approvals, and GORM grouping.
- `.github/workflows/validate.yml`: the pinned validator and GitHub-hosted CI configuration.

Keep consumer-specific version constraints, forks, exclusions, and deployment rules in the consuming repository. Add shared rules only when there is a reusable policy or dependency-family requirement.

## Policy changes

Unless the requested change explicitly revises these safeguards:

- Preserve manual merging and separate Major/non-major Action groups. GitHub Action references bypass Dashboard approval even for Major updates; other ordinary Major updates retain approval.
- Preserve security-fix exceptions to ordinary approval, release-age, schedule, grouping, and PR-limit gates without enabling automerge.
- Do not enable ordinary indirect Go updates as a side effect of grouping packages.
- Keep language presets additive; do not implicitly include the common preset or disable unrelated managers.
- Use narrow matchers. Action references are distinct from runtime inputs, workflows, and images; ORM packages are distinct from database servers and observability plugins.

Grouping and SemVer classifications do not prove compatibility or safety. Keep README aligned with behavior changes, and keep contributor procedures in CONTRIBUTING instead of duplicating them here.

## Verification and publication

Use the validation command in CONTRIBUTING; the workflow is the source of truth for its pinned runtime. For behavior changes, verify the affected preset compositions and representative consumer behavior as described there. For documentation-only changes, check examples, relative links, and any symlink changes.

Report checks actually performed and any environment or access limitations. Schema validation is not a substitute for consumer verification, and developer credentials do not prove hosted Renovate access to a private preset.

Never commit credentials, private operational details, or internal ticket links to public artifacts. Do not change repository visibility, App permissions, consumer repositories, or publish commits, PRs, or tags without authorization covering that action. Do not move published version tags or rewrite published history; follow the release and rollback guidance in CONTRIBUTING.
