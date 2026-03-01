# Day 10: Transactions, Indexes, and Real-World Patterns

**Reed says:** *"Last day we cover: making multi-step changes safe with transactions, speeding up queries with indexes, and a few patterns you'll see often in real projects."*

---

## 1. Transactions — all or nothing

A **transaction** groups several statements. Either **all** of them are saved (COMMIT) or **none** are (ROLLBACK). That keeps your data consistent when one logical operation has multiple steps.

**PostgreSQL**

```sql
BEGIN;
UPDATE orders SET status = 'processing' WHERE id = 1;
INSERT INTO payments (order_id, method, amount, status) VALUES (1, 'card', 99.99, 'completed');
-- If everything is OK:
COMMIT;
-- If something went wrong:
-- ROLLBACK;
```

**MySQL / TiDB**

```sql
START TRANSACTION;
UPDATE orders SET status = 'processing' WHERE id = 1;
INSERT INTO payments (order_id, method, amount, status) VALUES (1, 'card', 99.99, 'completed');
COMMIT;
-- or ROLLBACK;
```

Use transactions when several INSERT/UPDATE/DELETE must succeed or fail together (e.g. "create order + reserve inventory + record payment").

---

## 2. Indexes — speed up lookups

An **index** helps the database find rows quickly by a column (or set of columns) instead of scanning the whole table.

- **Primary key** and **UNIQUE** columns usually have an index already.
- Add an index on columns you often use in **WHERE**, **JOIN**, or **ORDER BY**.

**Create an index**

```sql
CREATE INDEX idx_orders_customer_id ON orders(customer_id);
CREATE INDEX idx_orders_status ON orders(status);
```

**See how the query runs (execution plan)**

**PostgreSQL:** `EXPLAIN (ANALYZE) SELECT * FROM orders WHERE customer_id = 42;`  
**MySQL / TiDB:** `EXPLAIN SELECT * FROM orders WHERE customer_id = 42;`

Look for "Index Scan" or "Index Seek" (good) vs "Full Table Scan" (may be slow on big tables).

---

## 3. Real-world patterns

### Pivot: rows → columns

**Order count per customer by status (one row per customer, one column per status)**

```sql
SELECT
    customer_id,
    SUM(CASE WHEN status = 'pending'   THEN 1 ELSE 0 END) AS pending,
    SUM(CASE WHEN status = 'delivered' THEN 1 ELSE 0 END) AS delivered,
    SUM(CASE WHEN status = 'cancelled' THEN 1 ELSE 0 END) AS cancelled,
    COUNT(*) AS total
FROM orders
GROUP BY customer_id
ORDER BY total DESC
LIMIT 15;
```

*(In PostgreSQL you can use COUNT(*) FILTER (WHERE status = 'pending') etc.)*

### Top N per group (again)

**Top 3 orders by amount per customer**

```sql
WITH ranked AS (
    SELECT id, customer_id, total_amount,
           ROW_NUMBER() OVER (PARTITION BY customer_id ORDER BY total_amount DESC) AS rn
    FROM orders
)
SELECT customer_id, id AS order_id, total_amount, rn
FROM ranked
WHERE rn <= 3
ORDER BY customer_id, rn
LIMIT 20;
```

### Find duplicates

**Customers with duplicate email**

```sql
SELECT email, COUNT(*) AS cnt
FROM customers
GROUP BY email
HAVING COUNT(*) > 1;
```

---

## 4. Good habits

- **Filter early** — use WHERE to reduce rows before JOINs and GROUP BY.
- **Select only what you need** — avoid `SELECT *` in production when you only need a few columns.
- **Use meaningful aliases** — e.g. `o` for orders, `c` for customers.
- **Keep transactions short** — don’t hold a transaction open while doing slow or external work.
- **Use EXPLAIN** when a query is slow — see if indexes are used.

---

## You try

1. **Transaction:** In a transaction, update one order’s status to 'cancelled' and insert a row into a log table (if you have one) or just do the UPDATE. Then ROLLBACK and check that the order status did not change.
2. **Index:** Create an index on `reviews(product_id)` if it doesn’t exist. Run a query that counts reviews per product and use EXPLAIN to see if the index is used.
3. **Pivot:** Show one row per **order status** with columns: status, count of orders, and total revenue (SUM of total_amount). Use GROUP BY status.

---

## Course complete

You’ve gone from "what is a table?" to transactions, indexes, and real-world patterns. Keep practicing with your e-commerce data: try rewriting a subquery as a JOIN, add a CTE to clarify a complex query, and use EXPLAIN to see how the database runs it. When you’re stuck, ask Reed—or open the right day’s file and find the pattern you need.

— **Reed**
