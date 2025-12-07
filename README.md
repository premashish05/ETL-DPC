# Query Result to Grid Component - Complete Demo Project

## 📚 Table of Contents

- [Overview](#overview)
- [Component Documentation](#component-documentation)
- [Demo Project Structure](#demo-project-structure)
- [Prerequisites](#prerequisites)
- [Quick Start Guide](#quick-start-guide)
- [Pipeline Descriptions](#pipeline-descriptions)
- [Use Cases](#use-cases)
- [Component Properties Reference](#component-properties-reference)
- [Best Practices](#best-practices)
- [Troubleshooting](#troubleshooting)
- [Additional Resources](#additional-resources)

---

## 🎯 Overview

This project provides a **complete, working demonstration** of the [Query Result to Grid](https://docs.matillion.com/data-productivity-cloud/designer/docs/query-to-grid) component in Matillion Data Productivity Cloud. The project is fully portable and will work across any DPC environment without modification.

### What is the Query Result to Grid Component?

The **Query Result to Grid** component is an orchestration component that allows you to:
- Query a table and return rows of data
- Load query results into a predefined [grid variable](https://docs.matillion.com/data-productivity-cloud/designer/docs/grid-variables)
- Use that data elsewhere in your pipeline (iteration, conditional logic, etc.)
- Build dynamic, data-driven pipelines

### Key Features

✅ **Basic Mode**: Build queries visually using component properties  
✅ **Advanced Mode**: Write custom SQL for complex queries and aggregations  
✅ **Grid Variables**: Store multi-row, multi-column datasets for pipeline use  
✅ **Row Limit**: Up to 5,000 records (with ~1 MB payload limit)  
✅ **Integration**: Works seamlessly with Grid Iterator and other components  

---

## 📖 Component Documentation

### Component Type
**Orchestration Component** - `query-to-grid`

### Official Documentation
[Query Result to Grid - Matillion Documentation](https://docs.matillion.com/data-productivity-cloud/designer/docs/query-to-grid)

### What Problems Does It Solve?

1. **Dynamic Pipeline Execution**: Query configuration data and use it to drive pipeline behavior
2. **Batch Processing**: Load a list of items (files, tables, customers) and process them iteratively
3. **Data-Driven Workflows**: Make pipeline decisions based on query results
4. **Metadata Operations**: Query table metadata and use it for dynamic transformations
5. **Reporting & Analytics**: Aggregate data and pass results between pipelines

### Comparison with Similar Components

| Component | Purpose | Output |
|-----------|---------|--------|
| **Query Result to Grid** | Query data, store multiple rows/columns | Grid Variable (multi-row) |
| [Query Result to Scalar](https://docs.matillion.com/data-productivity-cloud/designer/docs/query-result-to-scalar) | Query data, store single value | Scalar Variable (single value) |
| [Table Metadata to Grid](https://docs.matillion.com/data-productivity-cloud/designer/docs/table-metadata-to-grid) | Get table column metadata | Grid Variable (column info) |

---

## 📁 Demo Project Structure

This project contains three pipelines that demonstrate the component:

```
Query_Result_To_Grid_Demo/
├── README.md                          # This file - comprehensive documentation
├── COMPONENT_DEEP_DIVE.md            # Detailed component explanation
├── 01_Setup_Demo_Data.orch.yaml     # Creates sample employee table
├── 02_Basic_Mode_Demo.orch.yaml     # Demonstrates Basic Mode with Grid Iterator
└── 03_Advanced_Mode_Demo.orch.yaml  # Demonstrates Advanced Mode with custom SQL
```

---

## ✅ Prerequisites

### Required
- Matillion Data Productivity Cloud account
- Snowflake data warehouse connection configured
- Default environment with database and schema set

### No Additional Setup Required!
This project uses:
- ✅ `[Environment Default]` for database/schema (adapts to your environment)
- ✅ Dynamic table creation (no pre-existing tables needed)
- ✅ Embedded grid variables (no external dependencies)

---

## 🚀 Quick Start Guide

### Step 1: Import the Project

1. **Export** this entire `Query_Result_To_Grid_Demo` folder from your current DPC account
2. **Import** the folder into any DPC account (yours or another user's)
3. The project will automatically adapt to the target environment's configuration

### Step 2: Run the Setup Pipeline

**Pipeline**: `01_Setup_Demo_Data.orch.yaml`

**Purpose**: Creates a sample `DEMO_EMPLOYEES` table with 20 employee records

**Run this first!** This sets up the data needed for the other demos.

```yaml
What it does:
1. Creates DEMO_EMPLOYEES table in your default schema
2. Inserts 20 sample employee records
3. Data includes: ID, Name, Department, Salary, Hire Date, Status
```

**Expected Output**:
- ✅ Table `DEMO_EMPLOYEES` created
- ✅ 20 rows inserted

### Step 3: Run Basic Mode Demo

**Pipeline**: `02_Basic_Mode_Demo.orch.yaml`

**Purpose**: Demonstrates Basic Mode query configuration

```yaml
What it does:
1. Queries high-earning active employees (salary >= $90,000)
2. Loads top 10 results into a grid variable
3. Uses Grid Iterator to process each employee
4. Creates EMPLOYEE_REPORTS table with results
```

**Expected Output**:
- ✅ 7 employees loaded into grid variable
- ✅ Grid Iterator runs 7 times
- ✅ Table `EMPLOYEE_REPORTS` created with 7 records

### Step 4: Run Advanced Mode Demo

**Pipeline**: `03_Advanced_Mode_Demo.orch.yaml`

**Purpose**: Demonstrates Advanced Mode with custom SQL

```yaml
What it does:
1. Runs custom SQL to aggregate department statistics
2. Calculates avg salary, employee count, max salary per department
3. Loads results into grid variable
4. Creates GRID_LOAD_LOG table to confirm success
```

**Expected Output**:
- ✅ 5 departments loaded into grid variable
- ✅ Table `GRID_LOAD_LOG` created with execution log

---

## 📋 Pipeline Descriptions

### Pipeline 1: Setup Demo Data

**File**: `01_Setup_Demo_Data.orch.yaml`

**Components**:
1. **Start** → Initiates pipeline
2. **Create Employees Table** → [Create Table](https://docs.matillion.com/data-productivity-cloud/designer/docs/create-table-v2) component
3. **Insert Sample Employee Data** → [SQL Executor](https://docs.matillion.com/data-productivity-cloud/designer/docs/sql-executor) component

**Table Schema**: `DEMO_EMPLOYEES`
```sql
EMPLOYEE_ID     NUMBER(10,0)    PRIMARY KEY
FIRST_NAME      VARCHAR(100)    NOT NULL
LAST_NAME       VARCHAR(100)    NOT NULL
DEPARTMENT      VARCHAR(50)     NOT NULL
SALARY          NUMBER(12,2)    NOT NULL
HIRE_DATE       DATE            NOT NULL
STATUS          VARCHAR(20)     NOT NULL
```

**Sample Data**: 20 employees across Engineering, Marketing, Sales, HR, and Finance

---

### Pipeline 2: Basic Mode Demo

**File**: `02_Basic_Mode_Demo.orch.yaml`

**Demonstrates**:
- ✅ Basic Mode query configuration
- ✅ Table/column selection via UI
- ✅ Filtering conditions
- ✅ Sorting and limiting results
- ✅ Grid variable mapping
- ✅ Grid Iterator integration

**Components**:

1. **Start** → Initiates pipeline

2. **Query High Earners - Basic Mode** → Query Result to Grid component
   - **Mode**: Basic
   - **Source**: DEMO_EMPLOYEES table
   - **Columns**: EMPLOYEE_ID, FIRST_NAME, LAST_NAME, DEPARTMENT, SALARY
   - **Filters**: 
     - SALARY >= 90000
     - STATUS = 'Active'
     - Combined with AND
   - **Order By**: SALARY (Descending)
   - **Limit**: 10 records
   - **Output**: `employee_data_grid` variable

3. **Process Each Employee** → [Grid Iterator](https://docs.matillion.com/data-productivity-cloud/designer/docs/grid-iterator) component
   - Iterates over each row in the grid variable
   - Maps grid columns to scalar variables:
     - `employee_id` → `current_employee_id`
     - `first_name` → `current_employee_name`
     - `department` → `current_department`
     - `salary` → `current_salary`

4. **Create Employee Report Table** → SQL Executor component
   - Creates `EMPLOYEE_REPORTS` table
   - Inserts one row per iteration
   - Uses scalar variables populated by Grid Iterator

**Grid Variable**: `employee_data_grid`
```yaml
Columns:
  - employee_id: NUMBER
  - first_name: TEXT
  - last_name: TEXT
  - department: TEXT
  - salary: NUMBER
```

---

### Pipeline 3: Advanced Mode Demo

**File**: `03_Advanced_Mode_Demo.orch.yaml`

**Demonstrates**:
- ✅ Advanced Mode with custom SQL
- ✅ Aggregation queries
- ✅ GROUP BY and ORDER BY
- ✅ Calculated fields
- ✅ Complex SQL logic

**Components**:

1. **Start** → Initiates pipeline

2. **Query Department Stats - Advanced Mode** → Query Result to Grid component
   - **Mode**: Advanced
   - **Custom SQL**:
   ```sql
   SELECT 
     DEPARTMENT as dept_name,
     ROUND(AVG(SALARY), 2) as avg_salary,
     COUNT(*) as employee_count,
     MAX(SALARY) as max_salary
   FROM DEMO_EMPLOYEES
   WHERE STATUS = 'Active'
   GROUP BY DEPARTMENT
   ORDER BY avg_salary DESC
   ```
   - **Output**: `dept_stats_grid` variable

3. **Display Statistics** → SQL Executor component
   - Creates `GRID_LOAD_LOG` table
   - Logs successful grid variable load
   - Demonstrates completion confirmation

**Grid Variable**: `dept_stats_grid`
```yaml
Columns:
  - dept_name: TEXT
  - avg_salary: NUMBER
  - employee_count: NUMBER
  - max_salary: NUMBER
```

---

## 💡 Use Cases

### 1. Dynamic File Processing
**Scenario**: Query a list of files from a staging table, iterate through them, and load each file.

```yaml
Step 1: Query Result to Grid → Get list of files to process
Step 2: Grid Iterator → Loop through each file
Step 3: S3 Load Component → Load each file into Snowflake
```

### 2. Metadata-Driven Transformations
**Scenario**: Query table metadata and dynamically generate transformations.

```yaml
Step 1: Query Result to Grid → Get column list and data types
Step 2: Grid Iterator → Process each column
Step 3: Run Transformation → Apply column-specific logic
```

### 3. Multi-Tenant Data Processing
**Scenario**: Query list of tenants/customers and process data for each.

```yaml
Step 1: Query Result to Grid → Get active tenant list
Step 2: Grid Iterator → Loop through each tenant
Step 3: Run Orchestration → Execute tenant-specific pipeline
```

### 4. Configuration-Driven Pipelines
**Scenario**: Store pipeline configurations in a table and execute dynamically.

```yaml
Step 1: Query Result to Grid → Get pipeline configurations
Step 2: Grid Iterator → Loop through each configuration
Step 3: SQL Executor → Execute based on config parameters
```

### 5. Data Quality Checks
**Scenario**: Query data quality rules and validate data.

```yaml
Step 1: Query Result to Grid → Get list of validation rules
Step 2: Grid Iterator → Execute each rule
Step 3: SQL Executor → Run validation query and log results
```

---

## 🔧 Component Properties Reference

### Basic Mode Properties

| Property | Type | Required | Description |
|----------|------|----------|-------------|
| **Mode** | Dropdown | Yes | Select "Basic" for UI-driven query |
| **Database** | Dropdown | Yes | Target database (use `[Environment Default]`) |
| **Schema** | Dropdown | Yes | Target schema (use `[Environment Default]`) |
| **Table** | Dropdown | Yes | Table to query |
| **Table Columns** | Multi-select | Yes | Columns to include in results |
| **Order By** | Multi-select | No | Columns to sort by |
| **Sort** | Dropdown | Yes | Ascending or Descending |
| **Limit** | Integer | Yes | Maximum rows to return (max 5,000) |
| **Grid Variable** | Dropdown | Yes | Target grid variable to populate |
| **Grid Variable Mapping** | Grid | Yes | Map query columns to grid columns |
| **Filter Conditions** | Grid | No | WHERE clause filters |
| **Combine Filters** | Dropdown | Yes | AND or OR logic for filters |

### Advanced Mode Properties

| Property | Type | Required | Description |
|----------|------|----------|-------------|
| **Mode** | Dropdown | Yes | Select "Advanced" for SQL query |
| **Query** | SQL Editor | Yes | Custom SQL SELECT statement |
| **Grid Variable** | Dropdown | Yes | Target grid variable to populate |
| **Grid Variable Mapping** | Grid | Yes | Map query columns to grid columns |

### Important Limits

⚠️ **Row Limit**: Maximum 5,000 records  
⚠️ **Payload Limit**: Approximately 1 MB total payload size  
⚠️ **SQL Syntax**: Do NOT end SQL statements with a semicolon in Advanced Mode  

---

## 🌟 Best Practices

### 1. Grid Variable Design

✅ **DO**:
- Define grid variables with clear, descriptive column names
- Use appropriate column types (TEXT, NUMBER)
- Keep column count reasonable (10-15 columns max)
- Document variable purpose in the description field

❌ **DON'T**:
- Create overly complex grid structures
- Mix data types inappropriately
- Leave variables undocumented

### 2. Query Optimization

✅ **DO**:
- Use WHERE clauses to limit rows
- Set appropriate LIMIT values
- Order results for consistent iteration
- Index columns used in WHERE clauses
- Test queries in SQL Executor first

❌ **DON'T**:
- Query entire tables without filters
- Return unnecessary columns
- Use SELECT * in production
- Exceed the 5,000 row limit

### 3. Basic vs Advanced Mode

**Use Basic Mode when**:
- Simple queries with standard filters
- Single table queries
- UI-driven configuration preferred
- No aggregations needed

**Use Advanced Mode when**:
- Complex SQL with JOINs
- Aggregations (GROUP BY, SUM, AVG)
- Calculated fields
- Subqueries or CTEs
- Advanced SQL features needed

### 4. Integration Patterns

**Grid Iterator Pattern** (Most Common):
```
Query to Grid → Grid Iterator → Action Component
```
Use for: Row-by-row processing

**Conditional Execution Pattern**:
```
Query to Grid → Calculator (check row count) → Conditional component
```
Use for: Data-driven branching logic

**Child Pipeline Pattern**:
```
Query to Grid → Grid Iterator → Run Orchestration
```
Use for: Complex multi-step processing per row

### 5. Error Handling

✅ **DO**:
- Add failure transitions to handle query errors
- Validate grid variable has data before iterating
- Use Break On Failure appropriately in iterators
- Log grid variable population success/failure

❌ **DON'T**:
- Assume queries always return data
- Ignore empty result sets
- Skip validation steps

### 6. Performance Considerations

✅ **DO**:
- Limit result sets to necessary data only
- Use indexes on filtered columns
- Run heavy aggregations in Advanced Mode
- Consider caching frequently queried data

❌ **DON'T**:
- Query large datasets unnecessarily
- Run complex queries in tight loops
- Ignore query performance metrics

---

## 🔍 Troubleshooting

### Common Issues and Solutions

#### Issue 1: "Column has no data" Error

**Cause**: Column name mismatch between query results and grid variable mapping

**Solution**:
1. Use dynamic lookup to verify actual column names from query
2. Ensure column names in mapping match exactly (case-sensitive)
3. In Advanced Mode, use column aliases that match grid columns

```sql
-- ✅ CORRECT
SELECT 
  EMPLOYEE_ID as employee_id,  -- Matches grid column
  FIRST_NAME as first_name      -- Matches grid column
FROM DEMO_EMPLOYEES

-- ❌ INCORRECT
SELECT EMPLOYEE_ID, FIRST_NAME  -- Column names won't match
FROM DEMO_EMPLOYEES
```

#### Issue 2: Grid Variable Mapping Validation Errors

**Cause**: Table doesn't exist or dependencies not set correctly

**Solution**:
1. Run setup pipeline first to create required tables
2. Ensure database/schema parameters are set before table selection
3. Use dynamic lookups to populate column selections

#### Issue 3: Empty Grid Variable

**Cause**: Query returns no results

**Solution**:
1. Test query independently in SQL Executor
2. Check filter conditions
3. Verify table has data
4. Add validation after Query to Grid to check row count

#### Issue 4: Payload Size Exceeded

**Cause**: Query results exceed ~1 MB limit

**Solution**:
1. Reduce LIMIT value
2. Select fewer columns
3. Filter more aggressively
4. Consider pagination approach with multiple queries

#### Issue 5: Grid Iterator Not Running

**Cause**: Grid variable is empty or iterator not properly attached

**Solution**:
1. Verify Query to Grid component succeeded
2. Check grid variable has data (use Calculator to check)
3. Ensure iterator is properly "stacked" on target component
4. Verify iterationTarget property is set correctly

---

## 🎓 Learning Path

### Beginner
1. ✅ Run `01_Setup_Demo_Data.orch.yaml`
2. ✅ Run `02_Basic_Mode_Demo.orch.yaml`
3. ✅ Examine the `employee_data_grid` variable
4. ✅ Review `EMPLOYEE_REPORTS` table results

### Intermediate
1. ✅ Run `03_Advanced_Mode_Demo.orch.yaml`
2. ✅ Modify the SQL query to add more aggregations
3. ✅ Create a new grid variable with different columns
4. ✅ Build your own Basic Mode query

### Advanced
1. ✅ Create a pipeline that combines both modes
2. ✅ Build a dynamic ETL pipeline using Query to Grid
3. ✅ Implement error handling and logging
4. ✅ Create reusable child pipelines with grid variables

---

## 📚 Additional Resources

### Official Matillion Documentation
- [Query Result to Grid Component](https://docs.matillion.com/data-productivity-cloud/designer/docs/query-to-grid)
- [Grid Variables](https://docs.matillion.com/data-productivity-cloud/designer/docs/grid-variables)
- [Grid Iterator Component](https://docs.matillion.com/data-productivity-cloud/designer/docs/grid-iterator)
- [Variables Overview](https://docs.matillion.com/data-productivity-cloud/designer/docs/variables)

### Related Components
- [Query Result to Scalar](https://docs.matillion.com/data-productivity-cloud/designer/docs/query-result-to-scalar)
- [Table Metadata to Grid](https://docs.matillion.com/data-productivity-cloud/designer/docs/table-metadata-to-grid)
- [Create Table](https://docs.matillion.com/data-productivity-cloud/designer/docs/create-table-v2)
- [SQL Executor](https://docs.matillion.com/data-productivity-cloud/designer/docs/sql-executor)

### Snowflake Resources
- [Snowflake SQL Reference](https://docs.snowflake.com/en/sql-reference)
- [Snowflake Data Types](https://docs.snowflake.com/en/sql-reference/data-types)

---

## 🤝 Contributing

This demo project is designed to be educational and portable. Feel free to:
- Modify queries and add your own examples
- Extend pipelines with additional components
- Adapt for your specific use cases
- Share with other DPC users

---

## 📝 Project Metadata

**Created**: 2025-11-27  
**Component**: Query Result to Grid (`query-to-grid`)  
**Component Type**: Orchestration  
**Matillion Version**: Data Productivity Cloud  
**Warehouse**: Snowflake  
**Portability**: ✅ Fully portable across all DPC environments  

---

## ✅ Checklist for New Users

- [ ] Import project folder into DPC
- [ ] Verify environment default database and schema are configured
- [ ] Run `01_Setup_Demo_Data.orch.yaml` successfully
- [ ] Verify `DEMO_EMPLOYEES` table created with 20 rows
- [ ] Run `02_Basic_Mode_Demo.orch.yaml` successfully
- [ ] Verify `EMPLOYEE_REPORTS` table created
- [ ] Run `03_Advanced_Mode_Demo.orch.yaml` successfully
- [ ] Verify `GRID_LOAD_LOG` table created
- [ ] Review grid variables in both pipelines
- [ ] Read `COMPONENT_DEEP_DIVE.md` for detailed explanations

---

## 🎉 Success!

You now have a complete understanding of the Query Result to Grid component and can:
- ✅ Query data and load it into grid variables
- ✅ Use both Basic and Advanced modes effectively
- ✅ Integrate with Grid Iterators for row-by-row processing
- ✅ Build dynamic, data-driven pipelines
- ✅ Apply best practices for performance and reliability

Happy pipeline building! 🚀