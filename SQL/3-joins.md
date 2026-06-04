# SQL Reference Guide: 3. Joins

> **Optimize for**: Data Analytics, Risk Analytics, Finance, Banking

---

## INNER JOIN

### Definition
Returns only rows with matching values in BOTH tables. Eliminates unmatched rows from both sides.

### Syntax
```sql
SELECT t1.column1, t2.column2, ...
FROM table1 t1
INNER JOIN table2 t2 ON t1.key = t2.key
```

### Basic Example
```sql
-- Get customers and their transactions
SELECT 
    c.customer_id,
    c.customer_name,
    t.transaction_id,
    t.transaction_amount
FROM customers c
INNER JOIN transactions t ON c.customer_id = t.customer_id;
```

### Intermediate Example
```sql
-- Multi-table INNER JOIN for risk analysis
SELECT 
    c.customer_id,
    c.customer_name,
    a.account_balance,
    t.transaction_amount,
    r.risk_score
FROM customers c
INNER JOIN accounts a ON c.customer_id = a.customer_id
INNER JOIN transactions t ON c.customer_id = t.customer_id
INNER JOIN risk_scores r ON c.customer_id = r.customer_id
WHERE t.transaction_date >= DATEADD(month, -1, GETDATE())
  AND r.risk_score > 0.7;
```

### Advanced Example
```sql
-- INNER JOIN with complex business logic
SELECT 
    c.customer_id,
    c.customer_name,
    p.product_name,
    sh.sales_history_value,
    pc.customer_lifetime_value,
    CASE 
        WHEN sh.sales_history_value > pc.customer_lifetime_value * 0.2 THEN 'High Frequency'
        ELSE 'Low Frequency'
    END AS purchase_pattern
FROM customers c
INNER JOIN sales_history sh ON c.customer_id = sh.customer_id
INNER JOIN products p ON sh.product_id = p.product_id
INNER JOIN (SELECT customer_id, SUM(transaction_amount) AS customer_lifetime_value 
            FROM transactions GROUP BY customer_id) pc 
    ON c.customer_id = pc.customer_id
WHERE sh.sales_date >= DATEADD(year, -1, GETDATE())
  AND sh.sales_history_value > 10000;
```

### Common Mistakes

❌ **Selecting from unrelated tables with INNER JOIN**
```sql
-- This will create a Cartesian product if ON condition is wrong
SELECT * FROM customers c
INNER JOIN transactions t ON c.country = t.country;  -- Wrong join key!
```
✅ **Use correct primary/foreign key relationships**

❌ **Data loss without realizing**
```sql
INNER JOIN automatically removes non-matching rows
-- If not all customers have transactions, they disappear
```

### Best Practices

✅ **Use INNER JOIN for required relationships** - When both sides must have matches  
✅ **Be explicit about join columns** - Use aliases for clarity  
✅ **Index join columns** - Critical for performance  
✅ **Use table aliases** - Makes complex queries readable  
✅ **Document join logic** - Why this relationship exists  

### Performance Considerations

⚡ INNER JOIN is typically the fastest join type  
⚡ Index both sides of join condition - critical for performance  
⚡ Join on primary/foreign keys - usually have indexes  
⚡ Order joins by selectivity - smallest result set first  

### Business Use Case

📊 **Credit Risk Analysis with Account Data**:

```sql
SELECT 
    c.customer_id,
    c.customer_name,
    a.account_type,
    a.account_balance,
    cr.credit_score,
    cr.default_probability,
    SUM(t.transaction_amount) AS total_activity,
    COUNT(t.transaction_id) AS transaction_count
FROM customers c
INNER JOIN accounts a ON c.customer_id = a.customer_id
INNER JOIN credit_ratings cr ON c.customer_id = cr.customer_id
INNER JOIN transactions t ON c.customer_id = t.customer_id
WHERE t.transaction_date >= DATEADD(month, -3, GETDATE())
GROUP BY c.customer_id, c.customer_name, a.account_type, a.account_balance,
         cr.credit_score, cr.default_probability
HAVING cr.default_probability > 0.1;
```

---

## LEFT JOIN

### Definition
Returns ALL rows from LEFT table, with matching rows from RIGHT table. Non-matching RIGHT rows show NULL.

### Syntax
```sql
SELECT t1.column1, t2.column2, ...
FROM table1 t1
LEFT JOIN table2 t2 ON t1.key = t2.key
```

### Basic Example
```sql
-- All customers, showing their transactions (if any)
SELECT 
    c.customer_id,
    c.customer_name,
    t.transaction_id,
    t.transaction_amount
FROM customers c
LEFT JOIN transactions t ON c.customer_id = t.customer_id;
```

### Intermediate Example
```sql
-- Find customers with NO transactions
SELECT 
    c.customer_id,
    c.customer_name,
    c.signup_date,
    COUNT(t.transaction_id) AS transaction_count
FROM customers c
LEFT JOIN transactions t ON c.customer_id = t.customer_id
GROUP BY c.customer_id, c.customer_name, c.signup_date
HAVING COUNT(t.transaction_id) = 0  -- Never transacted
ORDER BY c.signup_date;
```

### Advanced Example
```sql
-- Compare customers with and without accounts
SELECT 
    c.customer_id,
    c.customer_name,
    COUNT(DISTINCT a.account_id) AS account_count,
    SUM(a.account_balance) AS total_balance,
    CASE WHEN a.account_id IS NULL THEN 'No Account' ELSE 'Has Account' END AS account_status,
    COUNT(t.transaction_id) AS transaction_count
FROM customers c
LEFT JOIN accounts a ON c.customer_id = a.customer_id
LEFT JOIN transactions t ON c.customer_id = t.customer_id 
    AND t.transaction_date >= DATEADD(month, -3, GETDATE())
GROUP BY c.customer_id, c.customer_name, 
    CASE WHEN a.account_id IS NULL THEN 'No Account' ELSE 'Has Account' END
ORDER BY account_count DESC, total_balance DESC;
```

### Common Mistakes

❌ **Using WHERE clause to filter RIGHT table**
```sql
SELECT c.*, t.*
FROM customers c
LEFT JOIN transactions t ON c.customer_id = t.customer_id
WHERE t.transaction_id IS NOT NULL;  -- This converts LEFT to INNER!
```
✅ **Put conditions on RIGHT table in ON clause**
```sql
SELECT c.*, t.*
FROM customers c
LEFT JOIN transactions t ON c.customer_id = t.customer_id
    AND t.transaction_date >= '2024-01-01'  -- In ON clause!
```

### Best Practices

✅ **LEFT JOIN when you need ALL rows from left table** - Keep the complete set  
✅ **Check for NULLs on joined columns** - Indicates no match  
✅ **Use COALESCE for NULL handling** - Makes results cleaner  
✅ **Filter RIGHT table conditions in ON clause** - Preserves LEFT rows  

### Performance Considerations

⚡ LEFT JOIN slightly slower than INNER JOIN  
⚡ Still requires index on join columns  
⚡ Large RIGHT tables can be slow - filter if possible  

### Business Use Case

📊 **Customer Segmentation - Active vs Inactive**:

```sql
SELECT 
    c.customer_id,
    c.customer_name,
    c.customer_tier,
    c.signup_date,
    MAX(t.transaction_date) AS last_transaction_date,
    DATEDIFF(day, MAX(t.transaction_date), GETDATE()) AS days_since_activity,
    COUNT(t.transaction_id) AS lifetime_transactions,
    CASE 
        WHEN MAX(t.transaction_date) >= DATEADD(month, -3, GETDATE()) THEN 'Active'
        WHEN MAX(t.transaction_date) >= DATEADD(month, -12, GETDATE()) THEN 'Inactive (6-12mo)'
        WHEN MAX(t.transaction_date) IS NOT NULL THEN 'Dormant (>1yr)'
        ELSE 'Never Transacted'
    END AS customer_status
FROM customers c
LEFT JOIN transactions t ON c.customer_id = t.customer_id
GROUP BY c.customer_id, c.customer_name, c.customer_tier, c.signup_date
ORDER BY days_since_activity DESC NULLS LAST;
```

---

## RIGHT JOIN

### Definition
Returns ALL rows from RIGHT table, with matching rows from LEFT table. Non-matching LEFT rows show NULL.

### Syntax
```sql
SELECT t1.column1, t2.column2, ...
FROM table1 t1
RIGHT JOIN table2 t2 ON t1.key = t2.key
```

### Example
```sql
-- All transactions, showing their customers (if they exist)
SELECT 
    t.transaction_id,
    t.transaction_amount,
    c.customer_id,
    c.customer_name
FROM customers c
RIGHT JOIN transactions t ON c.customer_id = t.customer_id;

-- Equivalent to:
SELECT 
    t.transaction_id,
    t.transaction_amount,
    c.customer_id,
    c.customer_name
FROM transactions t
LEFT JOIN customers c ON c.customer_id = t.customer_id;
```

### Note

✅ **Prefer LEFT JOIN over RIGHT JOIN** - More readable, easier to understand direction  
✅ **Rewrite RIGHT JOIN as LEFT JOIN** - Swap table order in FROM clause  

---

## FULL OUTER JOIN

### Definition
Returns ALL rows from BOTH tables, with NULLs where no match exists. (Not available in MySQL)

### Syntax
```sql
SELECT t1.column1, t2.column2, ...
FROM table1 t1
FULL OUTER JOIN table2 t2 ON t1.key = t2.key
```

### Basic Example
```sql
-- All customers and all transactions, showing unmatched rows
SELECT 
    c.customer_id,
    c.customer_name,
    t.transaction_id,
    t.transaction_amount
FROM customers c
FULL OUTER JOIN transactions t ON c.customer_id = t.customer_id;
```

### Advanced Example
```sql
-- Find data inconsistencies
SELECT 
    COALESCE(c.customer_id, t.customer_id) AS customer_id,
    c.customer_name,
    COUNT(t.transaction_id) AS transaction_count,
    CASE 
        WHEN c.customer_id IS NULL THEN 'Transaction orphan'
        WHEN t.transaction_id IS NULL THEN 'Customer no transactions'
        ELSE 'Matched'
    END AS data_quality
FROM customers c
FULL OUTER JOIN transactions t ON c.customer_id = t.customer_id
GROUP BY COALESCE(c.customer_id, t.customer_id), c.customer_name
HAVING c.customer_id IS NULL OR t.transaction_id IS NULL;
```

### Alternative for MySQL (doesn't support FULL OUTER JOIN)
```sql
-- Simulate FULL OUTER JOIN with UNION
SELECT 
    c.customer_id,
    c.customer_name,
    t.transaction_id,
    t.transaction_amount
FROM customers c
LEFT JOIN transactions t ON c.customer_id = t.customer_id
UNION
SELECT 
    c.customer_id,
    c.customer_name,
    t.transaction_id,
    t.transaction_amount
FROM customers c
RIGHT JOIN transactions t ON c.customer_id = t.customer_id
WHERE c.customer_id IS NULL;
```

### Business Use Case

📊 **Data Reconciliation - Regulatory Reporting**:

```sql
SELECT 
    COALESCE(gl.account_id, tx.account_id) AS account_id,
    gl.account_balance AS gl_balance,
    SUM(tx.transaction_amount) AS tx_sum,
    ABS(COALESCE(gl.account_balance, 0) - COALESCE(SUM(tx.transaction_amount), 0)) AS difference,
    CASE 
        WHEN gl.account_id IS NULL THEN 'In transactions but not GL'
        WHEN tx.account_id IS NULL THEN 'In GL but not transactions'
        WHEN ABS(COALESCE(gl.account_balance, 0) - COALESCE(SUM(tx.transaction_amount), 0)) > 0.01 THEN 'Mismatch'
        ELSE 'Reconciled'
    END AS reconciliation_status
FROM general_ledger gl
FULL OUTER JOIN transactions tx ON gl.account_id = tx.account_id
GROUP BY COALESCE(gl.account_id, tx.account_id), gl.account_balance
HAVING ABS(COALESCE(gl.account_balance, 0) - COALESCE(SUM(tx.transaction_amount), 0)) > 0.01
ORDER BY difference DESC;
```

---

## CROSS JOIN

### Definition
Produces Cartesian product - every row from table1 combined with every row from table2.

### Syntax
```sql
SELECT t1.column1, t2.column2, ...
FROM table1 t1
CROSS JOIN table2 t2
-- OR: FROM table1 t1, table2 t2
```

### Caution ⚠️
```sql
-- 1,000,000 customers × 100 products = 100,000,000 rows!
SELECT * FROM customers CROSS JOIN products;  -- DANGEROUS!
```

### Valid Example
```sql
-- Generate all combinations for missing values analysis
SELECT 
    c.customer_id,
    p.product_id,
    CASE WHEN s.sale_id IS NULL THEN 'No Purchase' ELSE 'Purchased' END AS purchase_status
FROM customers c
CROSS JOIN products p
LEFT JOIN sales s ON c.customer_id = s.customer_id AND p.product_id = s.product_id;
```

### Business Use Case

📊 **Customer-Product Matrix for Cross-sell Analysis**:

```sql
SELECT 
    c.customer_id,
    c.customer_name,
    p.product_id,
    p.product_category,
    CASE WHEN s.sale_id IS NOT NULL THEN 1 ELSE 0 END AS purchased_flag,
    COALESCE(s.sale_amount, 0) AS sale_amount,
    CASE 
        WHEN s.sale_id IS NULL AND p.product_category NOT IN (
            SELECT DISTINCT product_category FROM sales 
            WHERE customer_id = c.customer_id
        ) THEN 'Cross-sell Opportunity'
        ELSE 'Existing'
    END AS opportunity_type
FROM customers c
CROSS JOIN products p
LEFT JOIN sales s ON c.customer_id = s.customer_id AND p.product_id = s.product_id
WHERE c.customer_tier = 'Premium'
ORDER BY c.customer_id, p.product_id;
```

---

## SELF JOIN

### Definition
Join a table to itself. Useful for hierarchical or comparative analysis.

### Syntax
```sql
SELECT t1.column1, t2.column2, ...
FROM table t1
JOIN table t2 ON t1.key = t2.related_key
```

### Example 1: Organizational Hierarchy
```sql
-- Find each employee and their manager
SELECT 
    e.employee_id,
    e.employee_name,
    m.employee_id AS manager_id,
    m.employee_name AS manager_name,
    e.salary,
    m.salary AS manager_salary,
    e.salary - m.salary AS salary_difference
FROM employees e
LEFT JOIN employees m ON e.manager_id = m.employee_id
ORDER BY m.employee_name, e.employee_name;
```

### Example 2: Customer Comparison
```sql
-- Find customers with similar transaction amounts
SELECT 
    c1.customer_id AS customer_1,
    c1.customer_name AS name_1,
    c2.customer_id AS customer_2,
    c2.customer_name AS name_2,
    c1.total_transactions,
    c2.total_transactions,
    ABS(c1.total_transactions - c2.total_transactions) AS difference
FROM (
    SELECT customer_id, customer_name, COUNT(*) AS total_transactions
    FROM transactions
    GROUP BY customer_id, customer_name
) c1
JOIN (
    SELECT customer_id, customer_name, COUNT(*) AS total_transactions
    FROM transactions
    GROUP BY customer_id, customer_name
) c2 ON c1.customer_id < c2.customer_id
WHERE ABS(c1.total_transactions - c2.total_transactions) < 5
ORDER BY difference;
```

### Business Use Case

📊 **Fraud Detection - Duplicate/Similar Accounts**:

```sql
SELECT 
    c1.customer_id AS account_1,
    c1.customer_name AS name_1,
    c1.email AS email_1,
    c1.phone AS phone_1,
    c2.customer_id AS account_2,
    c2.customer_name AS name_2,
    c2.email AS email_2,
    c2.phone AS phone_2,
    CASE 
        WHEN c1.email = c2.email THEN 'Same Email'
        WHEN c1.phone = c2.phone THEN 'Same Phone'
        WHEN c1.address = c2.address AND c1.dob = c2.dob THEN 'Same Address & DOB'
        ELSE 'Other Similarity'
    END AS match_type
FROM customers c1
JOIN customers c2 ON c1.customer_id < c2.customer_id
WHERE (c1.email = c2.email OR c1.phone = c2.phone OR (c1.address = c2.address AND c1.dob = c2.dob))
ORDER BY c1.customer_id, c2.customer_id;
```

---

## Join Performance Optimization

### Index Strategy
```sql
-- For JOIN: index the columns used in ON condition
CREATE INDEX idx_transactions_customer_id ON transactions(customer_id);
CREATE INDEX idx_accounts_customer_id ON accounts(customer_id);
```

### Join Order Matters
```sql
-- Better: Join smaller tables first, filter early
SELECT c.*, a.*, t.*
FROM customers c  -- Usually filtered significantly
INNER JOIN accounts a ON c.customer_id = a.customer_id
INNER JOIN transactions t ON c.customer_id = t.customer_id
WHERE c.country = 'USA'  -- Filter customers first
  AND a.account_type = 'checking';
```

### Interview Question

❓ **Q: What's the difference between INNER, LEFT, RIGHT, and FULL OUTER joins?**  
A:
- INNER: Only matching rows from both tables
- LEFT: All from left, matching from right (NULLs if no match)
- RIGHT: All from right, matching from left (opposite of LEFT)
- FULL OUTER: All from both, NULLs where no match
