# Day 8: Window Functions — ROW_NUMBER, RANK, and OVER

**Reed says:** *"With GROUP BY we collapse rows. Sometimes we want to keep every row and still get a 'summary' per group—like a rank or a running total. Window functions do that with OVER ()."*

---

## What is a window function?

A **window function** computes a value from a "window" of rows (e.g. "all rows in this partition" or "all rows so far") but **does not collapse** rows. Every row stays, and you get one extra column (the result of the function).

Syntax: **function() OVER (PARTITION BY ... ORDER BY ...)**

- **PARTITION BY** — split rows into groups; the function is computed inside each group.
- **ORDER BY** — order of rows inside the partition (and meaning of "first," "last," "previous").
- **OVER ()** with nothing — the window is the whole result set.

---

## ROW_NUMBER() — unique 1, 2, 3, …

**ROW_NUMBER()** assigns a unique number (1, 2, 3, …) within each partition. No ties.

**Rank orders by total_amount within each customer**

```sql
SELECT
    id,
    customer_id,
    total_amount,
    ROW_NUMBER() OVER (PARTITION BY customer_id ORDER BY total_amount DESC) AS rn
FROM orders
ORDER BY customer_id, rn
LIMIT 25;
```

**Top 3 most expensive orders per customer**

```sql
SELECT *
FROM (
    SELECT
        id,
        customer_id,
        total_amount,
        ROW_NUMBER() OVER (PARTITION BY customer_id ORDER BY total_amount DESC) AS rn
    FROM orders
) t
WHERE rn <= 3
ORDER BY customer_id, rn
LIMIT 20;
```

---

## RANK() and DENSE_RANK()

- **RANK()** — ties get the same number; the next rank **skips** (e.g. 1, 2, 2, 4).
- **DENSE_RANK()** — ties get the same number; the next rank **does not skip** (e.g. 1, 2, 2, 3).

**Rank products by price within category**

```sql
SELECT
    category_id,
    name,
    price,
    ROW_NUMBER() OVER (PARTITION BY category_id ORDER BY price DESC) AS row_num,
    RANK()       OVER (PARTITION BY category_id ORDER BY price DESC) AS rk,
    DENSE_RANK() OVER (PARTITION BY category_id ORDER BY price DESC) AS dr
FROM products
ORDER BY category_id, price DESC
LIMIT 25;
```

---

## SUM/AVG as window functions

**Running total of order amount per customer (by order id)**

```sql
SELECT
    id,
    customer_id,
    total_amount,
    SUM(total_amount) OVER (PARTITION BY customer_id ORDER BY id) AS running_total
FROM orders
ORDER BY customer_id, id
LIMIT 25;
```

**Grand total next to each order (no PARTITION)**

```sql
SELECT
    id,
    customer_id,
    total_amount,
    SUM(total_amount) OVER () AS grand_total
FROM orders
LIMIT 15;
```

---

## NTILE(n) — split into n groups

**NTILE(4)** assigns 1, 2, 3, or 4 so rows are split into 4 buckets (e.g. quartiles).

**Quartiles of order total**

```sql
SELECT
    id,
    customer_id,
    total_amount,
    NTILE(4) OVER (ORDER BY total_amount) AS quartile
FROM orders
ORDER BY total_amount
LIMIT 20;
```

---

## You try

1. For each **product**, show `id`, `name`, `price`, and **rank by price** within its category (1 = most expensive). Use RANK or DENSE_RANK. LIMIT 20.
2. For each **customer**, list their **3 most recent orders** (by `id` or `created_at`). Use ROW_NUMBER() and a derived table. Show order id, customer_id, total_amount, and rank. LIMIT 15.
3. For each **order**, show `id`, `customer_id`, `total_amount`, and the **running total** of total_amount for that customer (order by id). Use SUM() OVER (PARTITION BY customer_id ORDER BY id). LIMIT 20.

---

Tomorrow we'll use **CTEs (WITH)** to name subqueries and write **recursive** queries for hierarchies.

— **Reed**
