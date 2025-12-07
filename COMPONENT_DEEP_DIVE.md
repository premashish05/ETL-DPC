# Query Result to Grid Component - Deep Dive

This document provides an in-depth technical explanation of the **Query Result to Grid** component.

## Component Architecture

**Component ID**: `query-to-grid`  
**Type**: Orchestration  
**Status**: Public Preview

### Ports

**Input**: INPUT (one connection)  
**Outputs**: SUCCESS, FAILURE, UNCONDITIONAL (many connections each)

## How It Works

### Basic Mode

1. Collects parameters (database, schema, table, columns, filters)
2. Generates SQL automatically
3. Executes query against Snowflake
4. Transforms results into grid variable format
5. Populates grid variable
6. Follows success/failure transition

### Advanced Mode

1. Validates custom SQL syntax
2. Wraps query with `LIMIT 5000` for safety
3. Executes query against Snowflake
4. Transforms results into grid variable format
5. Populates grid variable
6. Follows success/failure transition

## Grid Variables

Grid variables store tabular data (rows and columns).

**Structure**:
```yaml
variable_name:
  metadata:
    type: "GRID"
    scope: "SHARED"      # or COPIED
    visibility: "PUBLIC"  # or PRIVATE
    columns:
      col1: {columnType: "TEXT"}
      col2: {columnType: "NUMBER"}
  defaultValue: []
```

### Scopes

- **SHARED**: All branches share same variable
- **COPIED**: Each branch gets independent copy

### Visibility

- **PRIVATE**: Only visible in current pipeline
- **PUBLIC**: Accessible to child pipelines

## Mode Comparison

### Basic Mode

**Use For**:
- Single table queries
- Simple filters
- No calculations needed
- UI-driven configuration

**Limitations**:
- No JOINs
- No aggregations
- No calculated fields
- Single table only

### Advanced Mode

**Use For**:
- Complex queries with JOINs
- Aggregations (SUM, AVG, COUNT)
- Calculated fields
- Subqueries/CTEs

**Advantages**:
- Full SQL flexibility
- Multiple tables
- Advanced SQL features

## Integration Patterns

### Pattern 1: Grid Iterator

**Most Common Pattern**

```
Query to Grid → Grid Iterator → Action Component
```

Process each row sequentially or concurrently.

### Pattern 2: Conditional Execution

```
Query to Grid → Calculator → If Component → Branches
```

Make decisions based on query results.

### Pattern 3: Child Pipeline

```
Query to Grid → Run Orchestration → Child Pipeline
```

Pass grid data to child for complex processing.

## Limits

**Hard Limits**:
- 5,000 rows maximum
- ~1 MB payload size
- Cannot be overridden

**Best Practices**:
- Keep row count reasonable (100-1,000)
- Limit columns to what's needed (5-15)
- Use filters to reduce dataset size
- Test queries independently first

## Real-World Examples

### Example 1: Multi-File Processing

```yaml
Step 1: Query file list → files_grid
Step 2: Grid Iterator over files
Step 3: Load each file
Step 4: Update status
```

### Example 2: Multi-Tenant ETL

```yaml
Step 1: Query active tenants → tenants_grid
Step 2: Grid Iterator (concurrent)
Step 3: Run tenant-specific ETL
```

### Example 3: Incremental Loads

```yaml
Step 1: Query tables with watermarks → tables_grid
Step 2: Grid Iterator over tables
Step 3: Run incremental load
Step 4: Update watermark
```

## Debugging Tips

1. **Test SQL First**: Use SQL Executor to test queries
2. **Check Row Count**: Use Calculator to check `ARRAY_SIZE(grid_var)`
3. **Log Progress**: Add logging inside iterators
4. **Use Failure Transitions**: Always handle failures

## Advanced Techniques

### Pagination

For datasets > 5,000 rows:
- Query in batches with OFFSET
- Process each batch
- Continue until no more rows

### Dynamic SQL

Use variables in Advanced Mode:
```sql
SELECT * FROM sales
WHERE sale_date >= '${target_date}'
```

### Nested Iteration

Use child pipelines:
- Parent iterates customers
- Child iterates products per customer
- Processes customer + product combinations

## Security

- Runs with environment service account permissions
- Respects row-level security and masking
- Use PRIVATE visibility for sensitive data
- Mask sensitive columns in queries

## Performance Tips

- Index columns used in WHERE clauses
- Avoid full table scans
- Use appropriate LIMIT values
- Keep grid variables reasonably sized
- Use concurrent iteration when appropriate

---

**For practical examples and setup instructions, see [README.md](./README.md)**