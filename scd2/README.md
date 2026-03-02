# SCD2 Implementation Pattern

## 🎯 The Problem

Tracking historical changes in shipment status across distribution centers. Original approach stored every status change as a separate row.

**Issues:**
- 70+ million rows (mostly duplicates)
- Query times: 45+ seconds
- Difficult to get "current state" view
- Complex queries to find status at specific dates

## ✅ The Solution

Implemented Slowly Changing Dimension Type 2 (SCD2) pattern:
- Compress historical changes
- Flag current records with `is_current`
- Track validity periods with `effective_from` and `effective_to`
- Maintain complete audit trail

## 📊 Results

- **98% reduction:** 70M+ → 1.5M rows
- **Query time:** 45s → 3s for current state
- **Audit compliance:** Full history preserved
- **Storage costs:** Significant reduction

## 💻 Implementation
```sql
-- See scd2-implementation.sql for full code
```

## 🔑 Key Design Decisions

**Why `effective_from` / `effective_to` over version numbers?**
- More intuitive for business users
- Easier date-range queries for audit
- Natural handling of "current" (effective_to = '9999-12-31')

**Why `is_current` flag?**
- Most queries only need current state
- Simple `WHERE is_current = TRUE` filter
- Indexed for performance

**Why hash key on natural key?**
- Faster updates and lookups
- Consistent identifier across systems

## 📖 Usage Examples
```sql
-- Get current state only (fast)
SELECT * FROM shipments_scd2 
WHERE is_current = TRUE;

-- Get state at specific date (audit)
SELECT * FROM shipments_scd2 
WHERE '2024-01-15' BETWEEN effective_from AND effective_to;

-- Track all changes for specific shipment
SELECT * FROM shipments_scd2 
WHERE shipment_id = 12345 
ORDER BY effective_from;
```

## 🏗️ When to Use This Pattern

**Good fit:**
- Need historical tracking
- Frequent updates to records
- Audit requirements
- "Current state" is most common query

**Not ideal:**
- Data never changes (use simple table)
- Don't need history (use SCD1 or updates)
- Real-time requirements (consider streaming)
