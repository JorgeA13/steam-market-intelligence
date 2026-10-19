# ADR-0002: Repository language

- **Date:** 2026-10-16
- **Status:** Accepted

## Context

The repository is public and serves as a professional portfolio. A single language is needed for code, commits, pull requests and documentation; the first pull request mixed English and Spanish.

## Options considered

1. **Spanish:** natural for the author; limits the audience to Spanish-speaking companies and recruiters.
2. **English:** industry standard; widens the audience to international companies.
3. **Mixed (code in English, docs in Spanish):** common in local teams, but looks inconsistent in a portfolio.

## Decision

English across the whole repository: code, names, commits, pull request titles and descriptions, README and `docs/`.

## Rationale

It maximizes the portfolio's reach and matches industry practice. Language consistency is a quality signal for technical reviewers.

## Consequences

- All files and folders are named in English (`docs/validated_sources.md`, `docs/architecture.md`).
- ADRs in this folder are written in English.
