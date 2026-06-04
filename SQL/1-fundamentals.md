# SQL Reference Guide: 1. Fundamentals

> **Optimize for**: Data Analytics, Risk Analytics, Finance, Banking

---

## SELECT

### Definition
The SELECT statement retrieves data from one or more tables. It's the foundation of all SQL queries.

### Syntax
```sql
SELECT column1, column2, ...
FROM table_name
```

### Parameters
- **column1, column2**: Column names to retrieve (use `*` for all columns)
- **table_name**: Table to query from

### Basic Example
```sql
-- Select specific columns from a customer table
SELECT customer_id, customer_name, email
FROM customers;
```

### Intermediate Example
```sql
-- Select with aliases for better readability
SELECT 
    customer_id AS cust_id,
    customer_name AS name,
    account_balance AS balance
FROM customers;
```

### Advanced Example
```sql
-- Select with expressions and calculated columns
SELECT 
    customer_id,
    CONCAT(first_name, ' ', last_name) AS full_name,
    account_balance * 0.95 AS discounted_balance,  -- 5% discount
    CASE 
        WHEN account_balance > 100000 THEN 'VIP'
        WHEN account_balance > 50000 THEN 'Premium'
        ELSE 'Regular'
    END AS customer_tier
FROM customers
WHERE account_balance > 0;
```

### Common Mistakes

❌ **Selecting all columns with `*` in production** - Inefficient, includes unnecessary columns  
✅ **Select only needed columns** - Better performance and clarity

❌ **Forgetting to alias complex expressions** - Makes results hard to interpret  
✅ **Always alias calculated columns** - Improves readability

### Best Practices

✅ **Use meaningful aliases** - Help others understand your queries  
✅ **Select only necessary columns** - Reduces data transfer and memory usage  
✅ **Use DISTINCT when needed** - Avoid duplicate rows when appropriate  
✅ **Document complex expressions** - Add comments for calculated columns  

### Performance Considerations

⚡ Selecting specific columns is faster than `SELECT *`  
⚡ Avoid complex calculations in SELECT clause in very large datasets  
⚡ Consider pushing calculations to application layer for massive datasets  

### Business Use Case

📊 **Customer Portfolio Report**: Select customer ID, name, account balance, and customer tier to create a portfolio summary for relationship managers.

### Interview Question

❓ **Q: What's the difference between SELECT column_name and SELECT * ?**  
A: SELECT column_name is more efficient because it only retrieves specified columns, reducing network traffic and memory usage. SELECT * retrieves all columns, which is useful for exploration but inefficient in production.

---

## DISTINCT

### Definition
REMOVES duplicate rows from query results, keeping only unique rows.

### Syntax
```sql
SELECT DISTINCT column1, column2, ...
FROM table_name
```

### Parameters
- **column1, column2**: Columns for which to find distinct combinations

### Basic Example
```sql
-- Find all unique countries where we have customers
SELECT DISTINCT country
FROM customers
ORDER BY country;
```

### Intermediate Example
```sql
-- Find unique combinations of country and city
SELECT DISTINCT 
    country,
    city
FROM customers
ORDER BY country, city;
```

### Advanced Example
```sql
-- Find how many distinct products each customer has purchased
SELECT 
    customer_id,
    COUNT(DISTINCT product_id) AS unique_products_purchased
FROM orders
GROUP BY customer_id
HAVING COUNT(DISTINCT product_id) > 5  -- Customers who bought 5+ different products
ORDER BY unique_products_purchased DESC;
```

### Common Mistakes

❌ **Using DISTINCT with `*` when you only need one column** - Inefficient  
✅ **Specify only columns needed for distinctness** - Better performance

❌ **Assuming DISTINCT is free** - It requires sorting and deduplication  
✅ **Consider performance impact** - Especially with large datasets

### Best Practices

✅ **Only use DISTINCT when necessary** - It adds computational overhead  
✅ **Place DISTINCT early in query planning** - Better for optimization  
✅ **Combine with WHERE to reduce dataset first** - More efficient  
✅ **Document why DISTINCT is needed** - Help future readers understand intent  

### Performance Considerations

⚡ DISTINCT requires sorting/hashing - O(n log n) complexity  
⚡ Use GROUP BY instead when counting unique values  
⚡ Index columns used in DISTINCT for better performance  
⚡ For massive datasets, consider using GROUP BY for equivalent results

### Business Use Case

📊 **Customer Risk Assessment**: Find distinct customers who have made large transactions (>$1M) across multiple countries to identify potential suspicious patterns or money laundering risks.

```sql
SELECT DISTINCT 
    c.customer_id,
    c.customer_name,
    COUNT(DISTINCT t.country) AS countries_transacted
FROM customers c
JOIN transactions t ON c.customer_id = t.customer_id
WHERE t.transaction_amount > 1000000
GROUP BY c.customer_id, c.customer_name
HAVING COUNT(DISTINCT t.country) > 1
ORDER BY countries_transacted DESC;
```

### Interview Question

❓ **Q: How would you count distinct customers without using COUNT(DISTINCT...)?**  
A: Use GROUP BY instead:
```sql
-- Using COUNT(DISTINCT)
SELECT COUNT(DISTINCT customer_id) AS num_customers FROM orders;

-- Using GROUP BY
SELECT COUNT(*) AS num_customers FROM (
    SELECT DISTINCT customer_id FROM orders
) subquery;
```

---

## WHERE

### Definition
FILTERS rows based on specified conditions. Only rows where the condition is TRUE are returned.

### Syntax
```sql
SELECT column1, column2, ...
FROM table_name
WHERE condition
```

### Parameters
- **condition**: Logical expression that evaluates to TRUE or FALSE

### Basic Example
```sql
-- Find all customers with balance greater than $50,000
SELECT customer_id, customer_name, account_balance
FROM customers
WHERE account_balance > 50000;
```

### Intermediate Example
```sql
-- Multiple conditions with AND/OR
SELECT customer_id, customer_name, country, account_balance
FROM customers
WHERE country = 'USA'
  AND account_balance > 25000
  AND account_status = 'active';

-- OR example: Find high-risk customers
SELECT customer_id, customer_name, risk_score
FROM customers
WHERE risk_score > 0.8
   OR account_balance > 5000000
   OR transaction_count > 1000;
```

### Advanced Example
```sql
-- Complex WHERE with IN, BETWEEN, LIKE
SELECT customer_id, customer_name, account_balance, last_transaction_date
FROM customers
WHERE country IN ('USA', 'Canada', 'Mexico')  -- Country list
  AND account_balance BETWEEN 10000 AND 1000000  -- Range
  AND customer_name LIKE '%Smith%'  -- Pattern matching
  AND last_transaction_date >= DATEADD(day, -30, GETDATE())  -- Last 30 days
  AND account_status NOT IN ('closed', 'suspended');
```

### Common Mistakes

❌ **Using `=` for NULL checks** - NULL comparisons always return NULL  
```sql
SELECT * FROM customers WHERE email = NULL;  -- WRONG! Returns 0 rows
```
✅ **Use `IS NULL` and `IS NOT NULL`**  
```sql
SELECT * FROM customers WHERE email IS NOT NULL;  -- CORRECT!
```

❌ **Not considering NULL in boolean logic**  
```sql
WHERE status = 'active' OR status = NULL;  -- NULL is never TRUE or FALSE
```
✅ **Explicitly check for NULL**  
```sql
WHERE status = 'active' OR status IS NULL;
```

### Best Practices

✅ **Filter early** - WHERE clause should come after FROM, before GROUP BY  
✅ **Use indexes on filtered columns** - Improves query performance  
✅ **Keep conditions simple and readable** - Break complex logic into subqueries if needed  
✅ **Test edge cases** - NULLs, empty strings, negative numbers  
✅ **Document business rules** - Why certain filters exist  

### Performance Considerations

⚡ WHERE conditions are applied BEFORE GROUP BY and aggregations - place them early  
⚡ Index columns used in WHERE clause (especially in large tables)  
⚡ Complex WHERE conditions can be slower - consider materialized views  
⚡ Use SARGABLE conditions (that can use indexes):
  - ✅ `WHERE salary > 50000` (SARGABLE)
  - ❌ `WHERE YEAR(hire_date) = 2020` (NOT SARGABLE - function applied to column)
  - ✅ `WHERE hire_date >= '2020-01-01' AND hire_date < '2021-01-01'` (SARGABLE)

### Business Use Case

📊 **Credit Risk Filtering**: Identify customers with elevated risk for credit decision:

```sql
SELECT 
    customer_id,
    customer_name,
    credit_score,
    debt_to_income_ratio,
    default_probability
FROM customers
WHERE credit_score < 600
   OR debt_to_income_ratio > 0.50
   OR default_probability > 0.10
   OR payment_history LIKE '%late%'
ORDER BY default_probability DESC;
```

### Interview Question

❓ **Q: What's the order of operations in SQL? When does WHERE execute?**  
A: 1. FROM, 2. WHERE, 3. GROUP BY, 4. HAVING, 5. SELECT, 6. ORDER BY. WHERE filters individual rows BEFORE aggregation.

---

## ORDER BY

### Definition
SORTS query results based on specified columns in ascending (ASC) or descending (DESC) order.

### Syntax
```sql
SELECT column1, column2, ...
FROM table_name
ORDER BY column1 [ASC|DESC], column2 [ASC|DESC]
```

### Parameters
- **column1, column2**: Columns to sort by (order matters)
- **ASC**: Ascending order (default, smallest to largest)
- **DESC**: Descending order (largest to smallest)

### Basic Example
```sql
-- Sort customers by account balance, highest first
SELECT customer_id, customer_name, account_balance
FROM customers
ORDER BY account_balance DESC;
```

### Intermediate Example
```sql
-- Sort by multiple columns - primary and secondary sort
SELECT customer_id, customer_name, country, account_balance, last_transaction_date
FROM customers
ORDER BY country ASC, account_balance DESC;  -- Country A-Z, then balance highest first
```

### Advanced Example
```sql
-- Sort by expression and by column position
SELECT 
    customer_id,
    CONCAT(first_name, ' ', last_name) AS full_name,
    account_balance,
    account_balance * 0.05 AS estimated_annual_interest
FROM customers
ORDER BY account_balance * 0.05 DESC  -- Sort by expression
LIMIT 10;  -- Top 10 earners

-- Alternative: sort by column position (1-based index)
SELECT customer_id, full_name, account_balance
FROM customers
ORDER BY 3 DESC;  -- Sort by 3rd column (account_balance)
```

### Common Mistakes

❌ **Using ORDER BY on computed column name that's not in SELECT**  
✅ **Order by column in SELECT or use position number**

❌ **Not considering NULL sorting behavior** - NULLs sort first or last (database-dependent)  
✅ **Use NULLS FIRST/NULLS LAST explicitly** (in Oracle, PostgreSQL)

### Best Practices

✅ **ORDER BY comes LAST** - After GROUP BY and HAVING  
✅ **Use meaningful sort columns** - Helps with business logic  
✅ **Limit results** - Use LIMIT or TOP when sorting large datasets  
✅ **Index on ORDER BY columns** - For frequently sorted queries  
✅ **Be explicit about ASC/DESC** - Improves readability  

### Performance Considerations

⚡ ORDER BY requires sorting - expensive operation on large datasets  
⚡ Index columns in ORDER BY for better performance  
⚡ Multiple ORDER BY columns can be slow - consider limiting to primary sort  
⚡ Avoid ORDER BY on computed columns - calculate first, sort second  
⚡ LIMIT with ORDER BY is efficient - database can optimize this pattern

### Business Use Case

📊 **Customer Profitability Analysis**: Rank customers by profitability metrics:

```sql
SELECT 
    customer_id,
    customer_name,
    SUM(transaction_amount) AS total_revenue,
    COUNT(*) AS transaction_count,
    AVG(transaction_amount) AS avg_transaction,
    SUM(transaction_amount) / COUNT(*) / 365.0 AS daily_avg_revenue
FROM transactions
GROUP BY customer_id, customer_name
ORDER BY total_revenue DESC, transaction_count DESC
LIMIT 100;  -- Top 100 customers by revenue
```

### Interview Question

❓ **Q: What's the difference between ORDER BY column_name and ORDER BY column_position?**  
A: Both work, but ORDER BY column_name is preferred for clarity and maintainability. Position numbers can break if columns are added/removed.

---

## LIMIT & TOP

### Definition
**LIMIT** (PostgreSQL, MySQL, SQLite) or **TOP** (SQL Server) restricts the number of rows returned.

### Syntax
```sql
-- PostgreSQL, MySQL, SQLite
SELECT column1, column2, ...
FROM table_name
LIMIT number [OFFSET offset_value]

-- SQL Server
SELECT TOP number [PERCENT] column1, column2, ...
FROM table_name

-- Modern SQL Server (OFFSET-FETCH)
SELECT column1, column2, ...
FROM table_name
ORDER BY column1
OFFSET offset_value ROWS
FETCH NEXT number ROWS ONLY
```

### Parameters
- **number**: Number of rows to return
- **offset_value**: Number of rows to skip
- **PERCENT**: Return percentage of rows (SQL Server only)

### Basic Example
```sql
-- Get top 10 customers by balance
SELECT TOP 10 customer_id, customer_name, account_balance
FROM customers
ORDER BY account_balance DESC;

-- PostgreSQL/MySQL equivalent
SELECT customer_id, customer_name, account_balance
FROM customers
ORDER BY account_balance DESC
LIMIT 10;
```

### Intermediate Example
```sql
-- Pagination: Get rows 11-20
SELECT customer_id, customer_name, account_balance
FROM customers
ORDER BY account_balance DESC
LIMIT 10 OFFSET 10;  -- Skip first 10, take next 10

-- SQL Server equivalent
SELECT customer_id, customer_name, account_balance
FROM customers
ORDER BY account_balance DESC
OFFSET 10 ROWS
FETCH NEXT 10 ROWS ONLY;
```

### Advanced Example
```sql
-- Top 1% of customers by risk score
SELECT TOP 1 PERCENT customer_id, customer_name, risk_score
FROM customers
ORDER BY risk_score DESC;

-- Get top 5 transactions per customer
SELECT *
FROM (
    SELECT 
        customer_id,
        transaction_id,
        transaction_amount,
        ROW_NUMBER() OVER (PARTITION BY customer_id ORDER BY transaction_amount DESC) AS rn
    FROM transactions
) ranked
WHERE rn <= 5;
```

### Common Mistakes

❌ **Using LIMIT without ORDER BY** - Results are non-deterministic  
✅ **Always ORDER BY first, then LIMIT**

❌ **LIMIT in subqueries without ORDER BY in outer query** - Order not guaranteed  
✅ **Put ORDER BY at final level**

### Best Practices

✅ **Always use ORDER BY with LIMIT** - Ensures consistent results  
✅ **Use LIMIT for data exploration** - Don't fetch millions of rows  
✅ **Use OFFSET-FETCH for pagination** - Clearer than LIMIT OFFSET  
✅ **Prefer TOP for quick previews** - Signals to reader it's a limited look  

### Performance Considerations

⚡ LIMIT is very efficient - database can short-circuit sorting  
⚡ LIMIT with ORDER BY on indexed column is highly optimized  
⚡ Large OFFSET is slow - it still scans skipped rows  
⚡ For pagination, consider cursor/keyset pagination instead of OFFSET

### Business Use Case

📊 **Daily Risk Report**: Get top 50 at-risk customers for daily monitoring:

```sql
SELECT TOP 50
    customer_id,
    customer_name,
    risk_score,
    default_probability,
    last_transaction_date,
    account_balance
FROM customers
WHERE account_status = 'active'
ORDER BY risk_score DESC, default_probability DESC;
```

### Interview Question

❓ **Q: What's the difference between LIMIT and OFFSET-FETCH?**  
A: LIMIT 10 OFFSET 20 and OFFSET 20 ROWS FETCH NEXT 10 ROWS ONLY are equivalent. OFFSET-FETCH is more explicit and readable, especially for pagination.

---

## Summary Table: Fundamentals

| Concept | Purpose | Key Use | Performance |
|---------|---------|---------|-------------|
| SELECT | Retrieve columns | Data extraction | Fast - specific columns faster than * |
| DISTINCT | Remove duplicates | Find unique values | Slow - requires sorting |
| WHERE | Filter rows | Reduce dataset | Fast - use indexed columns |
| ORDER BY | Sort results | Ranking, top-N queries | Moderate - fast with indexes |
| LIMIT/TOP | Limit rows | Pagination, sampling | Very fast - short-circuits |

---

## Practice Exercises

1. **Basic SELECT**: Write a query to get customer ID, name, and balance from the customers table
2. **DISTINCT**: Find how many distinct countries have customers
3. **WHERE**: Find all customers with balance > $100,000 AND account status = 'active'
4. **ORDER BY**: List top 20 customers by account balance
5. **Combination**: Get top 10 active customers from USA, ordered by account balance, returning only name and balance
