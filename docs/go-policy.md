# Go dependency policy

Use the [Go preset](../go.json) alongside the [common preset](common-policy.md).

## Go modules and toolchains

- Run `go mod tidy` after module dependency updates.
- Limit standalone indirect dependency updates to security fixes. Indirect versions may still change as a consequence of direct upgrades and tidying.
- Require separate Dependency Dashboard approval for minimum Go version and toolchain changes, without forcing the minimum Go version to the latest release.
- Prioritize PRs for eligible `github.com/gnolang/gno` updates without bypassing approval or PR limits. This does not match Gno submodules such as `github.com/gnolang/gno/contribs/gnodev`.

The preset does not enable `gomodTidyAll`, import-path rewrites, or replacement massaging. See Renovate's [Go modules manager documentation](https://docs.renovatebot.com/modules/manager/gomod/).

## Non-major update groups

| Group | Included dependencies |
| --- | --- |
| GORM | `gorm.io/gorm`, `gorm.io/driver/mysql`, `gorm.io/driver/postgres`. Observability plugins, raw SQL clients, and database server images are excluded. |
| ClickHouse for Go | `github.com/ClickHouse/ch-go` and `github.com/ClickHouse/clickhouse-go/v2`. Ordinary indirect updates remain disabled, and security alerts do not pull the other module into the same PR. |
| go-redis | Go modules matching `github.com/redis/go-redis/**`, grouped by target major version (for example, `go-redis v9` and `go-redis v10`). Ordinary indirect updates remain disabled. |
| Sarama and SCRAM | `github.com/IBM/sarama` and `github.com/xdg-go/scram`. |
| Swagger for Go | `github.com/swaggo/swag`, `github.com/swaggo/gin-swagger`, and `github.com/swaggo/files`. The mise-managed `swag` command is excluded. |
| Protobuf for Go | The Go runtime and its mise-managed `protoc-gen-go` tool. The `protoc` compiler is excluded. |
| gRPC for Go | The Go runtime and its mise-managed `protoc-gen-go-grpc` tool. |

Groups combine eligible updates on the same base branch. They do not require every member to have an update, synchronize versions, regenerate checked-in code, or guarantee compatibility. Review generated code and dependent module artifacts where applicable.
