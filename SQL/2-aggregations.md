# SQL Reference Guide: 2. Aggregations

> **Optimize for**: Data Analytics, Risk Analytics, Finance, Banking

---

## COUNT

### Definition
Counts the number of rows or non-NULL values in a column.

### Syntax
```sql
SELECT COUNT(*) AS total_count,           -- Count all rows
       COUNT(column_name) AS non_null_count  -- Count non-NULL values
FROM table_name
```

### Basic Example
```sql
-- Count total customers
SELECT COUNT(*) AS total_customers
FROM customers;

-- Count customers with email (non-NULL)
SELECT COUNT(email) AS customers_with_email
FROM customers;
```

### Intermediate Example
```sql
-- Count different types of customers
SELECT 
    COUNT(*) AS total_customers,
    COUNT(email) AS with_email,
    COUNT(*) - COUNT(email) AS without_email,
    COUNT(DISTINCT country) AS unique_countries
FROM customers;
```

### Advanced Example
```sql
-- Count by category with conditional logic
SELECT 
    COUNT(*) AS total_transactions,
    COUNT(CASE WHEN transaction_amount > 10000 THEN 1 END) AS large_transactions,
    COUNT(CASE WHEN transaction_status = 'failed' THEN 1 END) AS failed_transactions,
    COUNT(DISTINCT customer_id) AS unique_customers,
    COUNT(DISTINCT YEAR(transaction_date)) AS years_active
FROM transactions
WHERE transaction_date >= DATEADD(year, -5, GETDATE());
```

### Common Mistakes

❌ **COUNT(*) vs COUNT(column_name)**
```sql
COUNT(*) = 5  -- Counts all rows, including rows where all columns are NULL
COUNT(email) = 3  -- Counts only rows where email is NOT NULL
```

❌ **Forgetting to use COUNT(DISTINCT) when needed**
```sql
COUNT(customer_id) = 100  -- May count same customer 100 times
COUNT(DISTINCT customer_id) = 10  -- Actual unique customers
```

### Best Practices

✅ **Use COUNT(*) for row counts** - Simpler and clearer intent  
✅ **Use COUNT(column_name) for non-NULL counts** - When NULL handling matters  
✅ **Use COUNT(DISTINCT) carefully** - It's slower than alternatives  
✅ **Comment the difference** - Help others understand which COUNT you used  

### Performance Considerations

⚡ COUNT(*) is highly optimized in most databases  
⚡ COUNT(DISTINCT) requires sorting/hashing - expensive on large datasets  
⚡ For very large tables, consider approximate counts (HyperLogLog)  
⚡ GROUP BY with COUNT is efficient with proper indexing  

### Business Use Case

📊 **Customer and Transaction Metrics**:

```sql
SELECT 
    customer_id,
    COUNT(*) AS total_transactions,
    COUNT(DISTINCT DATE(transaction_date)) AS days_with_transactions,
    COUNT(CASE WHEN transaction_status = 'successful' THEN 1 END) AS successful_txn,
    COUNT(CASE WHEN transaction_status = 'failed' THEN 1 END) AS failed_txn
FROM transactions
WHERE transaction_date >= DATEADD(month, -12, GETDATE())
GROUP BY customer_id
HAVING COUNT(*) >= 10  -- Active customers only
```

---

## SUM

### Definition
Calculates the total of numeric values in a column.

### Syntax
```sql
SELECT SUM(numeric_column) AS total
FROM table_name
```

### Basic Example
```sql
-- Total revenue across all transactions
SELECT SUM(transaction_amount) AS total_revenue
FROM transactions;
```

### Intermediate Example
```sql
-- Revenue by customer
SELECT 
    customer_id,
    SUM(transaction_amount) AS total_revenue,
    SUM(CASE WHEN transaction_status = 'successful' THEN transaction_amount ELSE 0 END) AS successful_revenue
FROM transactions
GROUP BY customer_id
ORDER BY total_revenue DESC;
```

### Advanced Example
```sql
-- Financial performance metrics
SELECT 
    DATE_TRUNC('month', transaction_date) AS month,
    SUM(CASE WHEN transaction_type = 'deposit' THEN amount ELSE 0 END) AS total_deposits,
    SUM(CASE WHEN transaction_type = 'withdrawal' THEN amount ELSE 0 END) AS total_withdrawals,
    SUM(CASE WHEN transaction_type = 'deposit' THEN amount ELSE -amount END) AS net_cash_flow,
    COUNT(DISTINCT customer_id) AS active_customers
FROM transactions
WHERE transaction_date >= DATEADD(year, -2, GETDATE())
GROUP BY DATE_TRUNC('month', transaction_date)
ORDER BY month DESC;
```

### Common Mistakes

❌ **SUM on NULL values**
```sql
SUM(NULL) = NULL  -- Not 0!
SUM(10, NULL, 20) = 30  -- NULL is ignored
```

❌ **SUM without GROUP BY when you need aggregates per group**

### Best Practices

✅ **Handle NULLs explicitly** - Use COALESCE or conditional SUM
✅ **Use conditional SUM for complex logic** - CASE WHEN within SUM
✅ **Include COUNT with SUM** - Helps validate calculations

### Business Use Case

📊 **Portfolio Risk Analysis**:

```sql
SELECT 
    portfolio_id,
    SUM(position_value) AS total_portfolio_value,
    SUM(CASE WHEN risk_level = 'high' THEN position_value ELSE 0 END) AS high_risk_value,
    SUM(CASE WHEN risk_level = 'high' THEN position_value ELSE 0 END) / SUM(position_value) AS pct_high_risk,
    SUM(daily_loss) AS total_loss_ytd,
    SUM(CASE WHEN daily_loss > 0 THEN daily_loss ELSE 0 END) AS cumulative_losses
FROM portfolio_positions
GROUP BY portfolio_id;
```

---

## AVG

### Definition
Calculates the average (mean) of numeric values.

### Syntax
```sql
SELECT AVG(numeric_column) AS average
FROM table_name
```

### Basic Example
```sql
-- Average transaction amount
SELECT AVG(transaction_amount) AS avg_transaction
FROM transactions;
```

### Intermediate Example
```sql
-- Average by customer tier
SELECT 
    customer_tier,
    AVG(account_balance) AS avg_balance,
    AVG(transaction_amount) AS avg_transaction,
    COUNT(*) AS transaction_count
FROM customers c
JOIN transactions t ON c.customer_id = t.customer_id
GROUP BY customer_tier;
```

### Advanced Example
```sql
-- Time-series average (moving average)
SELECT 
    DATE(transaction_date) AS transaction_day,
    AVG(transaction_amount) AS daily_avg,
    AVG(AVG(transaction_amount)) OVER (
        ORDER BY DATE(transaction_date) 
        ROWS BETWEEN 6 PRECEDING AND CURRENT ROW
    ) AS rolling_7day_avg
FROM transactions
GROUP BY DATE(transaction_date)
ORDER BY transaction_day DESC;
```

### Common Mistakes

❌ **AVG returning INTEGER instead of DECIMAL**
```sql
SELECT AVG(amount) FROM table;  -- May truncate decimals
```
✅ **Cast to DECIMAL for accuracy**
```sql
SELECT AVG(CAST(amount AS DECIMAL(10,2))) FROM table;
```

### Best Practices

✅ **Cast to appropriate decimal precision** - Avoid truncation  
✅ **Include COUNT and STDDEV with AVG** - Provides context  
✅ **Be aware of outliers** - AVG can be skewed by extreme values  

### Business Use Case

📊 **Risk Metrics Calculation**:

```sql
SELECT 
    customer_id,
    AVG(transaction_amount) AS avg_transaction,
    STDEV(transaction_amount) AS transaction_volatility,
    AVG(days_to_settle) AS avg_settlement_days,
    COUNT(*) AS transaction_count
FROM transactions
GROUP BY customer_id
HAVING COUNT(*) >= 20  -- Minimum sample size
```

---

## MIN & MAX

### Definition
Return the minimum (smallest) and maximum (largest) values from a column.

### Syntax
```sql
SELECT 
    MIN(column_name) AS minimum,
    MAX(column_name) AS maximum
FROM table_name
```

### Basic Example
```sql
-- Account balance range
SELECT 
    MIN(account_balance) AS lowest_balance,
    MAX(account_balance) AS highest_balance,
    MAX(account_balance) - MIN(account_balance) AS balance_range
FROM customers;
```

### Intermediate Example
```sql
-- First and last transaction dates by customer
SELECT 
    customer_id,
    MIN(transaction_date) AS first_transaction,
    MAX(transaction_date) AS last_transaction,
    DATEDIFF(day, MIN(transaction_date), MAX(transaction_date)) AS customer_tenure_days
FROM transactions
GROUP BY customer_id;
```

### Advanced Example
```sql
-- Find outliers using MIN/MAX with CASE
SELECT 
    customer_id,
    MIN(transaction_amount) AS min_transaction,
    MAX(transaction_amount) AS max_transaction,
    MAX(transaction_amount) / NULLIF(MIN(transaction_amount), 0) AS max_to_min_ratio
FROM transactions
WHERE transaction_date >= DATEADD(month, -12, GETDATE())
GROUP BY customer_id
HAVING MAX(transaction_amount) / NULLIF(MIN(transaction_amount), 0) > 100  -- Extreme variation
ORDER BY max_to_min_ratio DESC;
```

### Common Mistakes

❌ **Assuming MIN/MAX returns a specific row** - It only returns the value, not the row  
✅ **Use subqueries or window functions** - To get full row details

### Best Practices

✅ **Use with context** - Always include COUNT or other aggregates  
✅ **Check for outliers** - MAX/MIN can reveal data quality issues  
✅ **Use in validation** - Compare actual ranges with expected ranges  

### Business Use Case

📊 **Transaction Anomaly Detection**:

```sql
SELECT 
    customer_id,
    MIN(transaction_amount) AS min_amount,
    MAX(transaction_amount) AS max_amount,
    AVG(transaction_amount) AS avg_amount,
    MAX(transaction_amount) / AVG(transaction_amount) AS max_to_avg_ratio,
    CASE 
        WHEN MAX(transaction_amount) > AVG(transaction_amount) * 3 THEN 'ANOMALY'
        ELSE 'NORMAL'
    END AS anomaly_flag
FROM transactions
GROUP BY customer_id
HAVING MAX(transaction_amount) > AVG(transaction_amount) * 3;
```

---

## GROUP BY & HAVING

### Definition
**GROUP BY** organizes rows into groups; **HAVING** filters groups (like WHERE for aggregates).

### Syntax
```sql
SELECT column1, AGGREGATE(column2)
FROM table_name
WHERE condition  -- Filter individual rows
GROUP BY column1
HAVING condition  -- Filter groups
```

### Basic Example
```sql
-- Count transactions per customer
SELECT 
    customer_id,
    COUNT(*) AS transaction_count
FROM transactions
GROUP BY customer_id;
```

### Intermediate Example
```sql
-- Revenue per country, only showing countries with > $1M revenue
SELECT 
    country,
    SUM(transaction_amount) AS total_revenue,
    COUNT(DISTINCT customer_id) AS unique_customers,
    AVG(transaction_amount) AS avg_transaction
FROM transactions t
JOIN customers c ON t.customer_id = c.customer_id
GROUP BY country
HAVING SUM(transaction_amount) > 1000000  -- Filter groups
ORDER BY total_revenue DESC;
```

### Advanced Example
```sql
-- Multi-level grouping with HAVING
SELECT 
    DATE_TRUNC('month', transaction_date) AS month,
    customer_tier,
    SUM(transaction_amount) AS total_revenue,
    COUNT(*) AS transaction_count,
    AVG(transaction_amount) AS avg_transaction,
    COUNT(DISTINCT customer_id) AS active_customers
FROM transactions t
JOIN customers c ON t.customer_id = c.customer_id
GROUP BY DATE_TRUNC('month', transaction_date), customer_tier
HAVING COUNT(*) >= 50  -- Minimum 50 transactions
   AND SUM(transaction_amount) >= 100000  -- Minimum $100K revenue
ORDER BY month DESC, total_revenue DESC;
```

### Common Mistakes

❌ **GROUP BY with non-aggregated column not in GROUP BY**
```sql
SELECT customer_id, first_name, COUNT(*) FROM customers GROUP BY customer_id;  -- WRONG!
-- first_name not in GROUP BY
```
✅ **Include all non-aggregated columns in GROUP BY**
```sql
SELECT customer_id, first_name, COUNT(*) FROM customers GROUP BY customer_id, first_name;
```

❌ **Using WHERE instead of HAVING to filter aggregates**
```sql
SELECT customer_id, COUNT(*) FROM transactions
WHERE COUNT(*) > 10  -- WRONG! WHERE can't use aggregates
GROUP BY customer_id;
```
✅ **Use HAVING for aggregate conditions**
```sql
SELECT customer_id, COUNT(*) FROM transactions
GROUP BY customer_id
HAVING COUNT(*) > 10;  -- CORRECT!
```

### Best Practices

✅ **WHERE filters before grouping; HAVING filters after**
✅ **Use WHERE to reduce dataset first** - More efficient  
✅ **Group by primary key when possible** - Cleaner results  
✅ **Document GROUP BY logic** - Especially complex groupings  
✅ **Include COUNT with aggregates** - Provides context  

### Performance Considerations

⚡ GROUP BY requires sorting/hashing - consider indexing group columns  
⚡ Use WHERE to reduce data before GROUP BY - significant performance gain  
⚡ Multiple GROUP BY columns can be slow - consider materialized views  
⚡ GROUP BY integer position works but avoid for maintainability  

### Business Use Case

📊 **Risk Segmentation by Customer Tier**:

```sql
SELECT 
    customer_tier,
    COUNT(DISTINCT customer_id) AS num_customers,
    AVG(risk_score) AS avg_risk,
    MAX(risk_score) AS max_risk,
    SUM(CASE WHEN risk_score > 0.8 THEN 1 ELSE 0 END) AS high_risk_count,
    SUM(account_balance) AS total_aum,
    AVG(account_balance) AS avg_balance
FROM customers
GROUP BY customer_tier
HAVING COUNT(DISTINCT customer_id) >= 10
   AND AVG(risk_score) > 0.3
ORDER BY avg_risk DESC;
```

### Interview Question

❓ **Q: What's the order of execution: WHERE, GROUP BY, HAVING, or does it matter?**  
A: FROM → WHERE → GROUP BY → HAVING → SELECT → ORDER BY. WHERE filters individual rows before grouping. HAVING filters aggregated groups after grouping. This is crucial for performance.

---

## Summary: Aggregations

| Function | Purpose | Returns | Handles NULL |
|----------|---------|---------|---------------|
| COUNT(*) | Count all rows | Integer | Counts NULLs |
| COUNT(col) | Count non-NULL | Integer | Ignores NULLs |
| SUM | Total of values | Numeric | Ignores NULLs, returns NULL if all NULL |
| AVG | Average value | Numeric | Ignores NULLs |
| MIN/MAX | Smallest/largest | Same as column | Ignores NULLs |
| GROUP BY | Organize rows | - | Can group by NULL |
| HAVING | Filter groups | - | Works with aggregates |
