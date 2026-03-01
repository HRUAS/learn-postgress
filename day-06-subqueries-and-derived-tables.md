# Day 6: Subqueries and Derived Tables

**Reed says:** *"Sometimes one query isn't enough: you need a query inside another query. Today we use subqueries in WHERE and in FROM (derived tables)."*

---

## What is a subquery?

A **subquery** is a SELECT written inside another SQL statement. It can return:

- **One value** (one row, one column) — used like a number or string.
- **One column, many rows** — used with IN or NOT IN.
- **Many rows and columns** — used in FROM as a "derived table."

---

## Scalar subquery (one value)

A scalar subquery returns **exactly one row and one column**. Use it anywhere you'd use a single value.

**Products priced above the average price**

```sql
SELECT id, name, price
FROM products
WHERE price > (SELECT AVG(price) FROM products)
ORDER BY price
LIMIT 10;
```

`(SELECT AVG(price) FROM products)` gives one number; we compare each product’s price to that number.

**Customers who spent more than the average customer total**

```sql
SELECT customer_id, SUM(total_amount) AS total
FROM orders
GROUP BY customer_id
HAVING SUM(total_amount) > (SELECT AVG(tot) FROM (SELECT SUM(total_amount) AS tot FROM orders GROUP BY customer_id) t)
ORDER BY total DESC
LIMIT 10;
```

Here the subquery computes the average of "total per customer"; we keep only customers whose total is above that.

---

## IN and NOT IN with subqueries

The subquery returns **one column** (many rows). We check whether a value is in that list.

**Customers who have placed at least one order**

```sql
SELECT id, first_name, last_name
FROM customers
WHERE id IN (SELECT DISTINCT customer_id FROM orders)
LIMIT 15;
```

**Products that have never been ordered**

```sql
SELECT id, name, sku
FROM products
WHERE id NOT IN (SELECT product_id FROM order_items)
LIMIT 15;
```

---

## Derived table (subquery in FROM)

A **derived table** is a subquery in the **FROM** clause. It must have an **alias** (a name) and then you can treat it like a table.

**Average order total per customer, then show only those above 400**

```sql
SELECT customer_id, avg_total
FROM (
    SELECT customer_id, AVG(total_amount) AS avg_total
    FROM orders
    GROUP BY customer_id
) AS cust_avg
WHERE avg_total > 400
ORDER BY avg_total DESC
LIMIT 15;
```

The inner query produces one row per customer with their average order total. The outer query filters to only those above 400.

**Top 5 most expensive orders per customer**

```sql
SELECT *
FROM (
    SELECT
        id,
        customer_id,
        total_amount,
        ROW_NUMBER() OVER (PARTITION BY customer_id ORDER BY total_amount DESC) AS rn
    FROM orders
) AS ranked
WHERE rn <= 5
ORDER BY customer_id, rn
LIMIT 25;
```

*(ROW_NUMBER() is a window function we'll see again in Day 8. Here the derived table ranks orders per customer; the outer query keeps only the top 5 per customer.)*

---

## Correlated subquery

A **correlated** subquery uses a column from the outer query (e.g. `o.customer_id`). It runs once per row of the outer query.

**For each order, show total_amount and that customer’s average order amount**

```sql
SELECT
    o.id,
    o.customer_id,
    o.total_amount,
    (SELECT AVG(total_amount) FROM orders i WHERE i.customer_id = o.customer_id) AS customer_avg
FROM orders o
ORDER BY o.id
LIMIT 15;
```

The inner query depends on `o.customer_id`, so it’s correlated. For big tables, a JOIN or window function is often faster.

---

## You try

1. **Scalar:** List products whose price is **above** the average product price. Show `id`, `name`, `price`.
2. **IN:** List customers who have written at least one **review**. (Hint: use `reviews.customer_id` in a subquery.)
3. **Derived table:** From `order_items`, compute total quantity sold per `product_id`. Then from that result, show only products with total quantity sold greater than 50. Use a subquery in FROM with an alias.

---

Tomorrow we'll do **more JOINs**: self-joins, EXISTS, and when to prefer JOIN over subquery.

— **Reed**
