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
