# Steam Market Intelligence Lakehouse

A production-grade lakehouse built on Databricks that integrates public data from the Steam ecosystem (catalog, pricing history, concurrent players, and reviews) — and later Twitch audience data — to answer **pricing analytics** and **product analytics** questions in the video game industry.

This repository is both a hands-on learning project (following a structured Data Engineer curriculum) and a professional portfolio piece. Each module of the curriculum adds a real component to the platform.

## Business Context

**Fictional client:** *Pixel Forge Studios*, an independent game publisher.

**Problem:** Pricing, discount, and launch decisions are currently made by intuition. The team wants data-driven insights from the Steam market.

**Key stakeholders:**
- Pricing team (discount strategy)
- Marketing (campaign timing, streamer impact)
- Product (game lifecycle and player sentiment)

### Business Questions

1. How much do concurrent players increase during a discount, depending on discount depth (10%, 25%, 50%+) and genre?
2. Does the effect of a discount persist after it ends, or do player counts return to previous levels?
3. How does review sentiment change before, during, and after a discount?
4. What is the typical lifecycle curve of a game after launch, by genre and price range?
5. Which launch price ranges are associated with better engagement and reviews by genre?
6. Can we detect review bombing (anomalous spikes of negative reviews)?
7. *(Phase 2)* Does Twitch audience anticipate increases in Steam concurrent players?

## Architecture Overview

```
Sources → Extractors → Landing (Volume) → Bronze → Silver → Gold → Consumption
```

- **Landing**: Raw JSON files from APIs, partitioned by source and extraction date, stored in a Unity Catalog Volume.
- **Bronze**: Incremental ingestion with Auto Loader + ingestion metadata.
- **Silver**: Typed, deduplicated, and validated data. Game attributes and prices as SCD Type 2. Invalid records go to quarantine.
- **Gold**: Dimensional model (star schema) oriented to the business questions above.

**Orchestration:** Lakeflow Jobs  
**Deployment:** Databricks Asset Bundles + GitHub Actions  
**Governance:** Unity Catalog (grants, lineage)  
**Observability:** System tables

### High-level diagram

```
Steam API / Store / Reviews / Twitch
          │
          ▼
   Python Extractors
          │
          ▼
     Landing Volume
          │
          ▼
  Bronze (Auto Loader)
          │
          ▼
 Silver (SCD2 + Quality)
          │
          ▼
   Gold (Dimensional)
          │
          ▼
 Databricks SQL Dashboards
```

## Tech Stack

- **Platform:** Databricks (Free Edition) + Unity Catalog
- **Storage & Tables:** Delta Lake
- **Ingestion:** Auto Loader, Python extractors (with rate limiting, backoff, and retries)
- **Processing:** PySpark + Spark SQL
- **Orchestration:** Lakeflow Jobs
- **CI/CD:** GitHub Actions + Databricks Asset Bundles
- **Quality & Observability:** Custom quality rules + system tables
- **Language:** Python, SQL

## Project Structure

```
steam-market-intelligence/
├── src/steam_mi/          # Extractors, transformations, quality rules
├── pipelines/             # Lakeflow Declarative Pipelines
├── notebooks/             # Exploration and analysis (not production logic)
├── resources/             # Job and pipeline definitions (Asset Bundles)
├── tests/
├── docs/
│   ├── architecture.md
│   ├── data_dictionary.md
│   ├── runbook.md
│   └── adr/
├── databricks.yml
└── README.md
```

## Current Status

| Area                    | Status          | Notes                                      |
|-------------------------|-----------------|--------------------------------------------|
| Project setup           | In progress     | Repo created, Git folders connected        |
| Free Edition validations| Completed       | Catalogs, Volumes, PATs, system tables OK  |
| Data sources            | Validated       | Steam APIs reachable from serverless       |
| Extractors              | Not started     | Planned for Module 4                       |
| Bronze / Silver / Gold  | Not started     | —                                          |
| CI/CD                   | Not started     | Planned for Modules 9–10                   |

> This project is under active development as part of a structured learning curriculum. See `docs/` and the ADR log for design decisions.

## Getting Started

*(To be completed as the project matures)*

1. Clone the repository
2. Configure Databricks authentication (PAT or service principal)
3. Deploy with Asset Bundles: `databricks bundle deploy --target dev`
4. ...

## Key Design Decisions

Important technical decisions are documented as Architecture Decision Records in `docs/adr/`.

Examples:
- **ADR-001:** Steam ecosystem as the data domain (covers master data + SCD2, time series, semi-structured data, and multi-source integration).
- **ADR-002:** Entire repository in English (code, commits, documentation).

## Roadmap

| Phase | Focus                              | Status      |
|-------|------------------------------------|-------------|
| 1     | Foundations & design               | In progress |
| 2     | Ingestion & Bronze/Silver          | Planned     |
| 3     | Declarative pipelines & Gold       | Planned     |
| 4     | Production (orchestration + CI/CD) | Planned     |
| 5     | Tuning, observability & closure    | Planned     |

## Contributing / Learning Notes

This repository is primarily a personal learning and portfolio project. Feedback and discussions about design decisions are welcome via issues or discussions.

## License

This project is licensed under the MIT License. See the [LICENSE](LICENSE) file for details.
