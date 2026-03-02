# SCD2 Implementation

## The Problem

I had to track shipment status changes across our distribution network. Every time a shipment status updated, we were creating a new row. After a few months, the table hit 71 million rows and queries were taking 45+ seconds. Most of that data was just duplicates - if a shipment didn't change for weeks, we still had the same row over and over.

## What I Built

Set up a Type 2 Slowly Changing Dimension table that only stores actual changes:

- Each shipment gets one row per status change (not one per day)
- Tracks when each version was valid (effective_from/effective_to dates)
- Has an is_current flag for quick "what's happening now" queries
- Kept full history for audit

This got the table down to 1.5M rows. Queries that were taking 45 seconds now run in 3.

## Code

The SQL is pretty straightforward - use window functions to get the next update time for each shipment, then create validity periods between updates. See the .sql file for the full implementation.

## When to Use This

Good for situations where:
- You need to track changes over time
- Most queries just need current state
- You have audit requirements

Not great if:
- Your data doesn't change much
- You don't need history
- You're doing real-time streaming
