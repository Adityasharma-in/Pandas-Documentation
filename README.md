# Pandas Masterclass — Complete Reference

A comprehensive documentation covering **Pandas** from foundations through advanced edge cases. Built from a full lecture transcript, organized into 13 modules with runnable code examples using real-world data.

## Content Structure

Every topic follows a 4-part blueprint:

1. **Concept Anchor** — core intuition and what problem this tool solves
2. **Engine Rules** — how it works under the hood, edge cases, and behavioral rules
3. **Code** — realistic example with e-commerce, sensor, or analytics data
4. **Visual Memory** — mnemonic or mental model for retention

## Modules

| # | Module | Topics |
|---|---|---|
| 01 | **Foundations** | Core concepts, Series vs DataFrame, data ingestion, inspection, schema, type system, Copy-on-Write (Pandas 3.0+) |
| 02 | **Selection** | Column navigation, `.loc`, `.iloc`, `.at`/`.iat`, boolean masking, subset mutation, index operations, MultiIndex |
| 03 | **Cleaning** | Missingness audit, `dropna`, `fillna`, value mapping, deduplication, renaming, type standardization, string preprocessing, outlier clipping |
| 04 | **Transformation** | Feature generation, vectorized arithmetic, `apply`/`map`, sorting, schema alignment, `assign`/`pipe`, `value_counts`, `cut`/`qcut` |
| 05 | **Aggregation** | Split-apply-combine, GroupBy formations, reduction functions, `.agg()`, transform vs reduce, group filters, ranking, `crosstab` |
| 06 | **Combining Data** | `concat`, `merge`, join typologies (one-to-one, many-to-one, many-to-many), key targets, collision mitigation, `.join()` |
| 07 | **Reshaping** | Wide vs long, `.pivot()`, `pivot_table`, `melt`, `stack`/`unstack`, MultiIndex restructuring |
| 08 | **Text Data** | String dtypes, formatting, substring auditing, vectorized replace, token extraction, regex patterns |
| 09 | **Time Series** | `to_datetime`, DatetimeIndex, `.dt` accessor, chronological slicing, `resample`, `shift`/lag, rolling windows, time zones |
| 10 | **I/O** | CSV ingestion, Excel, JSON & Parquet, exporting, ingestion debugging, large-file strategies |
| 11 | **Visualization** | Embedded `.plot()`, chart typologies, framework selection (matplotlib/seaborn/plotly), visual auditing |
| 12 | **Performance** | Loop avoidance, optimization rules, `memory_usage`, storage minimization, scaling architecture, CoW paradigm |
| 13 | **Edge Cases** | Assignment gotchas, index alignment surprises, nullable types, categorical nuances, sparse representations, workflow diagnostics |

## Data Philosophy

All examples use realistic data — e-commerce transactions, sensor telemetry, inventory logs, customer analytics. No `foo`, `bar`, or placeholder datasets.

## Copy-on-Write (Pandas 3.0+)

CoW behavior is documented explicitly throughout — where chained assignment breaks, how `copy()` behaves, and what changes in 3.0+ defaults.

## Files

| File | Description |
|---|---|
| `pandas-masterclass.html` | Full HTML reference (all 13 modules) |
| `PANDAS_MASTERCLASS.md` | Same content in Markdown |

## License

MIT
