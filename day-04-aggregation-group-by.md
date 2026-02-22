# Day 4: Aggregation — COUNT, SUM, AVG, GROUP BY, HAVING

**Reed says:** *"Today we summarize data: how many rows? What's the total? The average? And how to group by a column (e.g. 'per customer' or 'per status')."*

---

## What is aggregation?

**Aggregation** means turning many rows into fewer rows (or one row) by computing a single value:

- **COUNT(*)** — how many rows.
- **SUM(column)** — sum of the values in that column.
- **AVG(column)** — average of the values.
- **MIN(column)** — smallest value.
- **MAX(column)** — largest value.

These are called **aggregate functions**. They ignore NULLs (except COUNT(*) which counts every row).

---

## Simple aggregation (one number for the whole table)

**How many customers?**

```sql
SELECT COUNT(*) FROM customers;
```

**How many orders?**

```sql
SELECT COUNT(*) FROM orders;
```

**Total revenue (sum of all order totals)**

```sql
SELECT SUM(total_amount) FROM orders;
```

**Average order total**

```sql
SELECT AVG(total_amount) FROM orders;
```

**Cheapest and most expensive product**

```sql
SELECT MIN(price) AS min_price, MAX(price) AS max_price FROM products;
```

**Aliases** (e.g. `AS min_price`) give a name to the column in the result.

---

## GROUP BY: aggregate per group

**GROUP BY** splits rows into groups (e.g. by status, by customer_id). Then we compute one aggregate per group.

**Order count per status**

```sql
SELECT status, COUNT(*) AS order_count
FROM orders
GROUP BY status
ORDER BY order_count DESC;
```

**Total revenue per customer**

```sql
SELECT customer_id, SUM(total_amount) AS total_spent
FROM orders
GROUP BY customer_id
ORDER BY total_spent DESC
LIMIT 15;
```

**Average rating per product (from reviews)**

```sql
SELECT product_id, AVG(rating) AS avg_rating, COUNT(*) AS review_count
FROM reviews
GROUP BY product_id
ORDER BY avg_rating DESC
LIMIT 15;
```

Rule: every column in the SELECT list must either be inside an aggregate (COUNT, SUM, …) or appear in GROUP BY.

---

## HAVING: filter after grouping

**WHERE** filters rows **before** grouping. **HAVING** filters **after** grouping (on the aggregated result).

**Customers who have placed more than 5 orders**

```sql
SELECT customer_id, COUNT(*) AS order_count
FROM orders
GROUP BY customer_id
HAVING COUNT(*) > 5
ORDER BY order_count DESC
LIMIT 15;
```

**Statuses that have at least 500 orders**

```sql
SELECT status, COUNT(*) AS cnt
FROM orders
GROUP BY status
HAVING COUNT(*) >= 500;
```

**Products with average rating above 4**

```sql
SELECT product_id, AVG(rating) AS avg_rating
FROM reviews
GROUP BY product_id
HAVING AVG(rating) > 4
ORDER BY avg_rating DESC
LIMIT 10;
```

---

## Order of clauses (with GROUP BY and HAVING)

```sql
SELECT   customer_id, SUM(total_amount) AS total
FROM     orders
WHERE    status = 'delivered'   -- optional: filter rows first
GROUP BY customer_id
HAVING   SUM(total_amount) > 200   -- optional: filter groups
ORDER BY total DESC
LIMIT    10;
```

Order: **SELECT → FROM → WHERE → GROUP BY → HAVING → ORDER BY → LIMIT**.

---

## You try

1. How many products are there? Use COUNT(*).
2. What is the **total** of `line_total` in `order_items`? Use SUM.
3. **Per customer**, how many orders? Show `customer_id` and the count. Order by count descending. LIMIT 10.
4. **Per order status**, how many orders? Show `status` and count. Use GROUP BY.
5. Which **customers** have a total spend (SUM of total_amount) greater than 500? Show customer_id and total. Use GROUP BY and HAVING.

---

Tomorrow we'll **join** two tables so we can show data from both in one query (e.g. order + customer name).

— **Reed**
