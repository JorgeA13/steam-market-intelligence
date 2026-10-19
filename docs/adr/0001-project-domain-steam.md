# ADR-0001: Project data domain: Steam ecosystem

- **Date:** 2026-10-15
- **Status:** Superseded by [ADR-0003](0003-domain-change-vehicle-energy-mx.md)

## Context

The project needs a domain with real data that forces the typical enterprise data patterns: master data that changes over time, events and time series, semi-structured data and multi-source integration. It must be feasible on Databricks Free Edition.

## Options considered

1. **GitHub Archive:** strong on events and nested JSON; weak on historical master data; volume hard to handle within Free Edition quotas.
2. **Financial / macroeconomic data:** strong on time series and restatements; little complex JSON; good APIs are often paid or limited.
3. **Steam ecosystem (video games):** covers all four patterns; public APIs; controllable volume.

## Decision

Steam ecosystem, framed as a market intelligence platform (pricing and product analytics) for a fictional game publisher.

## Rationale

It covered the most enterprise data patterns (SCD2 price history, player time series, review JSON, optional Twitch integration), with a business framing comparable to retail and e-commerce cases.

## Consequences

- Steam endpoints, rate limits and terms of use had to be validated before building.
- Superseded on 2026-10-18: the Steam Web API key requires a minimum purchase on the account. See ADR-0003.
