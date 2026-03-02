# Snowflake Transformation Patterns

Production SQL patterns from building data pipelines at Coles. Most of this comes from working with supply chain data - shipments, distribution centers, transport management - processing millions of records daily.

## Patterns

### SCD2 Implementation
Tracking historical changes while keeping queries fast. Built this to handle shipment status updates across our distribution network.

**The problem:** Original approach was storing every status change as a new row. Table hit 70+ million rows and queries were taking 45+ seconds.

**The solution:** SCD2 pattern that only stores actual changes. Compressed it down to 1.5 million rows, queries now run in about 3 seconds. Full history still there for audit.

[See the implementation](./scd2/)

---

### More patterns coming soon:
- Working with Snowflake VARIANT types for semi-structured data
- Dynamic tables vs views - when to use what
- Clustering and performance optimization

## About This Work

These patterns are from real production pipelines I built for supply chain analytics:
- High volume (70M+ records)
- Performance-critical (business dashboards need to be fast)
- Audit requirements (need full history)
- Complex transformations (multiple source systems, data quality issues)

All code samples use sanitized names and generic examples, but the logic and patterns are what I actually use in production.

## Tech

Built with Snowflake SQL. Patterns apply to dimensional modeling, ETL/ELT workflows, and data warehouse design generally.
