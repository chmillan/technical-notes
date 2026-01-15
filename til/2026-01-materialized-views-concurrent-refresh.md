# TIL — Designing Materialized Views for concurrent refresh in Postgres

## Context
I designed a Materialized View to serve a high-traffic application listing ~500,000 health establishments, consolidating installation data and recent measurement activity from multiple tables.

## What happened
The view needed to be refreshed daily without blocking reads, which required using `REFRESH MATERIALIZED VIEW CONCURRENTLY`.  
This introduced additional constraints on how the data and indexes had to be modeled.

## What I learned
- In Postgres, concurrent refresh of a Materialized View requires a UNIQUE index, which must be chosen based on domain guarantees, not convenience.
- Selecting the unique key (CNES) forced an explicit decision about data cardinality and ownership across source tables.
- Indexing a Materialized View should start with a minimal, usage-driven set, deferring composite indexes until real query patterns are observed.

## Takeaway
Efficient Materialized Views depend as much on data modeling decisions as on query performance.
