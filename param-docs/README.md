# StarRocks Parameter Dictionary

Comprehensive reference for all session variables and configuration parameters in the StarRocks codebase.

## Source Files

| Source | File | Count |
|--------|------|-------|
| FE Session Variables | `fe/fe-core/src/main/java/com/starrocks/qe/SessionVariable.java` | ~278 |
| FE Global Config | `fe/fe-core/src/main/java/com/starrocks/common/Config.java` | ~779 |
| BE Config | `be/src/common/config.h` | ~718 |
| Thrift (FE→BE) | `gensrc/thrift/InternalService.thrift` (TQueryOptions) | ~107 |

## Category Files

| # | File | Description |
|---|------|-------------|
| 01 | `01_query_optimization.yml` | CBO rules, rewrite toggles, predicate pushdown |
| 02 | `02_join_optimization.yml` | Hash join, broadcast, shuffle, runtime filter |
| 03 | `03_scan_and_io.yml` | Scan limits, split sizes, data cache |
| 04 | `04_parquet_orc_reader.yml` | Page index, bloom filter, lazy materialization |
| 05 | `05_memory_management.yml` | Memory limits, spill settings |
| 06 | `06_parallelism_scheduling.yml` | Pipeline, DOP, concurrency |
| 07 | `07_materialized_views.yml` | MV refresh, rewrite, planning |
| 08 | `08_external_catalogs.yml` | Hive, Iceberg, JDBC connectors |
| 09 | `09_network_timeout.yml` | RPC timeouts, connection settings |
| 10 | `10_statistics_cost_estimation.yml` | CBO statistics, histogram, sampling |
| 11 | `11_security_authentication.yml` | Auth, encryption, access control |
| 12 | `12_logging_debugging.yml` | Log levels, profiling, debug flags |
| 13 | `13_general_system.yml` | Catch-all for uncategorized params |

## YAML Entry Format

```yaml
- name: "variable_sql_name"
  type: Boolean | Integer | Long | Double | String
  default: <value>
  scope: "FE session variable" | "FE global config" | "BE config" | combination
  mutable: true | false
  description: >
    1-3 sentences explaining what it controls.
  code_references:
    definition:
      - file: "path/to/file"
        line: NNN
        context: "brief context"
    usage:
      - file: "path/to/file"
        line: NNN
        context: "how the variable changes behavior"
    thrift_propagation:
      - file: "gensrc/thrift/InternalService.thrift"
        line: NNN
        context: "Field N in TQueryOptions"
  behavior:
    "true": "what happens when enabled"
    "false": "what happens when disabled"
  related_variables:
    - "other_variable_name"
```

## Legend

- **scope**: Where the parameter is defined and takes effect
  - `FE session variable` — set per-session via `SET variable = value`
  - `FE global config` — set in `fe.conf` or via `ADMIN SET FRONTEND CONFIG`
  - `BE config` — set in `be.conf` or via HTTP API
  - Combinations indicate the variable exists in multiple scopes
- **mutable**: Whether it can be changed at runtime without restart
  - For BE configs, the `m` prefix on macros (e.g., `CONF_mBool`) indicates mutable
  - For FE configs, `@ConfField(mutable = true)` indicates mutable
- **thrift_propagation**: Variables propagated from FE to BE per-query via `TQueryOptions`
- **behavior**: What changes when the value is toggled (for booleans) or varied (for numerics)
