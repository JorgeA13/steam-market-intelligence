# ADR-0003: Domain change: vehicles and energy in Mexico

- **Date:** 2026-10-18
- **Status:** Accepted
- **Supersedes:** [ADR-0001](0001-project-domain-steam.md)

## Context

The Steam Web API key is not available to "limited" Steam accounts (accounts that have not spent at least USD 5 on the store). Buying something only to unlock the key was not acceptable. Public store endpoints still worked without a key, so Steam remained technically feasible, but this was a good moment to check whether another domain offered more real business value and portfolio impact.

Constraints: real and free data; enterprise data patterns (SCD2, time series, semi-structured data, multi-source integration, restated figures); volume manageable on Databricks Free Edition.

## Options considered

1. **Steam without an API key** (public store endpoints): feasible and fast-moving data; depends on unofficial endpoints; lower business relevance.
2. **Vehicles only** (INEGI, EPA, NHTSA): clear and relevant question in Mexico; low volume (main source is monthly).
3. **Energy in Mexico** (CNE, CENACE): high volume, official data, restated figures; legacy web services.
4. **Maritime (NOAA AIS):** very large, geospatial volume; little relevance to Mexico; risky for compute quotas.
5. **Aviation (OpenSky, BTS):** Mexican data mostly in PDF or spreadsheets; API limits.
6. **AI ecosystem (Hugging Face, arXiv):** interesting, but noisy metrics and further from an enterprise use case.
7. **Vehicles + energy in Mexico:** combines the business question of option 2 with the volume and patterns of option 3.

## Decision

Option 7: a lakehouse that analyzes the energy transition of road transport in Mexico. Central question: which option is cheaper to drive in each state (gasoline, non-plug-in hybrid, plug-in hybrid or electric), and does real adoption follow that cost?

## Rationale

- It answers a real question for drivers and for the industry (auto lenders, insurers, carmakers, charging operators).
- Official, free data with high relevance for Mexico.
- It covers every pattern the project needs: SCD2 (gas stations, tariffs), high-volume time series (hourly prices per grid node), semi-structured data (XML, JSON), integration of heterogeneous sources with entity resolution, and preliminary → revised → final figures.
- The central analysis does not appear to be publicly available at state and month level with these sources.

## Consequences

- The repository was renamed from `steam-market-intelligence` to `vehicle-energy-mx`, and the README was rewritten.
- `docs/validated_sources.md` will be rewritten once the new sources are validated (reachability from serverless, formats, publication frequency, terms of use).
- New risks: CENACE's legacy web service, CFE tariffs without an open format, and EPA coverage of models sold in Mexico (the Mexican efficiency portal is the preferred source, pending validation).
- Platform validations already done (Free Edition egress, catalogs, Volumes, tokens, service principals, system tables) still apply.
- Lesson learned: validate access to the main data source (keys, requirements, terms) before committing to a domain.
