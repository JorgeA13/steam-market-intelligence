# Vehicle Energy MX

**A Databricks lakehouse that answers a real question for Mexican drivers: is it cheaper to drive a gasoline car, a hybrid (HEV), a plug-in hybrid (PHEV) or an electric vehicle (BEV) — and does that depend on where you live?**

> 🚧 **Status:** in progress — validating data sources (Module 0). This README describes the target design; sections are updated as each component ships.

## The problem

Car buyers in Mexico usually decide between gasoline, hybrid and electric based on intuition or marketing. Energy prices vary a lot by state, by municipality and even by hour, but there is no public, comparable **energy cost per km** by state and month built from official data.

This project integrates official Mexican and public datasets on **vehicle sales**, **fuel prices per gas station** and **wholesale electricity prices** to answer:

1. What is the energy cost per km of ICE, HEV, PHEV and BEV vehicles, by state and month?
2. In which states, and since when, is a hybrid or an EV cheaper to drive than a gasoline car?
3. Is the non-plug-in hybrid the best balance between savings and convenience? *(hypothesis to be tested)*
4. Does real adoption (monthly sales by powertrain and state) follow the cost difference?
5. How much do fuel prices vary between gas stations in the same municipality?
6. How much does electricity cost change by hour and region (e.g., charging at night)?

## Data sources

| Source | Data | Frequency |
|---|---|---|
| INEGI — RAIAVL | Light-vehicle sales by brand/model; EV and hybrid sales by state | Monthly |
| CNE (formerly CRE) | Gasoline and diesel prices per gas station | Daily |
| CENACE | Locational marginal electricity prices by node and hour (day-ahead and real-time) | Daily (hourly grain) |
| CFE | Residential electricity tariffs | Monthly |
| CONUEE / EPA | Vehicle fuel economy and energy consumption | Yearly |
| NHTSA | Vehicle catalog, recalls and complaints | Continuous |

All sources are public. Access, formats and terms of use are documented in [`docs/validated_sources.md`](docs/validated_sources.md).

## Architecture

```
Sources ──▶ Python extractors ──▶ Landing (UC Volume) ──▶ Bronze ──▶ Silver ──▶ Gold ──▶ Databricks SQL dashboards
                                                   Auto Loader   SCD2, quality,   star schema,
                                                                 entity resolution cost per km
Orchestration: Lakeflow Jobs · Deployment: Asset Bundles + GitHub Actions · Governance: Unity Catalog
```

Key engineering challenges:
- **Entity resolution** across sources that name brands, models and locations differently.
- **Restated data**: INEGI figures move from preliminary to revised to final; real-time electricity prices correct day-ahead prices.
- **Heterogeneous formats**: XML, legacy web services, spreadsheets and JSON APIs.
- **Incremental and idempotent** pipelines: re-running never duplicates data.

## Tech stack

Databricks (Free Edition, serverless) · Apache Spark / PySpark · Delta Lake · Unity Catalog · Lakeflow Jobs & Declarative Pipelines · Databricks SQL · Databricks Asset Bundles · GitHub Actions · pytest · ruff

## Repository structure (target)

```
src/vemx/        extract/, transform/, matching/, quality/
pipelines/       Lakeflow Declarative Pipelines
notebooks/       exploration and analysis
resources/       jobs and pipelines for Asset Bundles
tests/           unit and integration tests
docs/            architecture, data dictionary, runbook, ADRs
```

## Roadmap

- [x] Repository setup, branch protection, Databricks Git folder
- [ ] Source validation and sample extraction
- [ ] Bronze ingestion with Auto Loader
- [ ] Silver: SCD2, data quality, entity resolution
- [ ] Gold: dimensional model and cost-per-km metrics
- [ ] Orchestration, CI/CD and observability
- [ ] Findings and dashboards

## Project history

This repository started as a Steam market analytics project. The domain changed after validating data access; the reasoning is documented in the ADRs (`docs/adr/`).

## License

MIT