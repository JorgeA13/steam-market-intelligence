# Databricks Free Edition — Validated Capabilities

This document records the capabilities of **Databricks Free Edition** that were validated during the early stages of the Steam Market Intelligence Lakehouse project (October 2026).

The goal of these validations was to confirm which platform features are available and usable under Free Edition constraints, so that the architecture and implementation decisions remain realistic and portable.

All tests were performed in a serverless environment unless otherwise noted.

## Summary of Validations

| Capability | Result | Implication for the Project |
|---|---|---|
| **Internet egress from serverless notebooks** | ✅ Available (2026-10-15) | HTTP 200 responses from `api.steampowered.com`, `store.steampowered.com`, `steamspy.com`, `api.github.com`, and `pypi.org`. | Ingestion can run directly from Databricks. Official documentation states that egress is restricted to trusted domains and the allow-list may change. Extractors must fail explicitly when a required domain is blocked. Final decision on ingestion location will be documented in the ingestion ADR. |
| **Custom catalogs** | ✅ Available | Catalog `steam_dev` was successfully created. `SHOW CATALOGS` returned: `samples`, `steam_dev`, `system`, `workspace`. | The planned multi-environment design (`steam_dev` / `steam_prod`) is viable. |
| **Volumes** | ✅ Available | Volume `steam_dev.landing.raw` was created. Write operations succeeded (`dbutils.fs.put`). | Landing zone in a Unity Catalog Volume is viable. |
| **Personal Access Tokens (PATs)** | ✅ Available (2026-10-16) | The option to create PATs is enabled in the workspace. No token has been generated yet. | Databricks CLI and Asset Bundles can be used from a local machine. Personal tokens will **not** be used in CI/CD pipelines. |
| **Service principals** | ✅ Available (2026-10-16) | Service principals can be created from the workspace UI. | Mitigates a previously identified risk: CI/CD (planned for later modules) can authenticate using a service principal. OAuth secret (M2M) generation without the account console still needs verification. |
| **System tables** | ✅ Available | `system.billing.usage` is readable (records from 2026-10-15 observed). Available schemas under `system`: `access`, `ai`, `ai_gateway`, `billing`, `compute`, `information_schema`, `lakeflow`, `mlflow`, `query`, `serving`, `sharing`, `storage`. | Cost monitoring is viable. Usage records appear with a delay of several hours. |
| **Observed compute limits** | ⏳ Not yet observed in practice | — | Runtime observed: Spark 4.2.0 on serverless. `cache()` / `persist()` and the classic Spark UI are not available on serverless (per official documentation). |

## Detailed Findings

### Internet Egress

Outbound HTTP requests from serverless notebooks succeeded against the domains required by the project (Steam APIs, SteamSpy, GitHub, and PyPI).  

Because Databricks Free Edition restricts egress to a list of trusted domains, and that list can change, all extractors must treat connectivity failures as explicit errors rather than silent retries or partial successes.

### Unity Catalog: Catalogs and Volumes

- Custom catalogs can be created.
- Volumes can be created and written to.
- When reading files from a Volume, Spark ignores files whose names start with `_` or `.` (the same convention used for Delta metadata such as `_delta_log`, `_SUCCESS`, and `.crc`). This caused an `UNABLE_TO_INFER_SCHEMA` error during early tests.  

**Convention adopted:** no data files written to the landing Volume will start with `_` or `.`.

### Authentication Options

Both Personal Access Tokens and service principals are available.  
This enables:

- Local development with the Databricks CLI and Asset Bundles.
- Future CI/CD pipelines that authenticate with a service principal (preferred over personal tokens).

### System Tables

System tables are accessible and contain usable data (billing/usage records were successfully queried). This confirms that cost and operational monitoring will be possible later in the project.

### Serverless Runtime Notes

- Spark version observed: **4.2.0**
- Classic Spark UI is not available
- `cache()` and `persist()` are not supported on serverless

These limitations are accepted for the Free Edition environment and will be revisited if the project is later moved to classic compute.

## Open Items

| Item | Status | Next Step |
|---|---|---|
| Actual daily/weekly compute quota behavior | Not yet observed | Monitor during ingestion development |
| OAuth secret generation for service principals (M2M) without account console | Pending | Validate when implementing CI/CD |
| Long-term stability of the egress allow-list | Unknown | Keep extractors defensive and document any failures |

## References

- Databricks Free Edition documentation (egress restrictions, serverless limitations)
- Project Architecture Decision Records (`docs/adr/`)

---

*Last updated: 2026-10-17*  
*Validated in workspace: Steam Market Intelligence (Free Edition)*
