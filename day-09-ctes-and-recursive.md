# Day 9: CTEs (WITH) and Recursive Queries

**Reed says:** *"CTEs let you name a subquery and reuse it, so complex queries become readable. Recursive CTEs let you walk trees and generate sequences."*

---

## What is a CTE?

A **CTE** (Common Table Expression) is a **named** subquery defined with **WITH**. You can then use that name in the main SELECT (and in later CTEs). It makes multi-step logic clear.

**Syntax**

```sql
WITH name AS (
    SELECT ... FROM ...
)
SELECT ... FROM name ...;
```

---

## Simple CTE

**High-value orders and the customers who placed them**

```sql
WITH high_value_orders AS (
    SELECT id, customer_id, total_amount
    FROM orders
    WHERE total_amount > 500
)
SELECT o.id, o.total_amount, c.first_name, c.last_name
FROM high_value_orders o
JOIN customers c ON c.id = o.customer_id
ORDER BY o.total_amount DESC
LIMIT 15;
```

The CTE `high_value_orders` is like a temporary named result set; the main query uses it like a table.

---

## Multiple CTEs

You can define several CTEs in one WITH. Separate them with commas; later ones can use earlier ones.

**Customer totals, then only top spenders**

```sql
WITH customer_totals AS (
    SELECT customer_id, SUM(total_amount) AS total_spent
    FROM orders
    GROUP BY customer_id
),
top_spenders AS (
    SELECT customer_id, total_spent
    FROM customer_totals
    WHERE total_spent > 1000
)
SELECT c.id, c.first_name, c.last_name, t.total_spent
FROM top_spenders t
JOIN customers c ON c.id = t.customer_id
ORDER BY t.total_spent DESC
LIMIT 15;
```

---

## Recursive CTE — two parts

A **recursive CTE** has:

1. **Anchor** — a non-recursive SELECT that gives the starting rows.
2. **Recursive part** — a SELECT that references the CTE name and unions with the anchor. It runs repeatedly until no new rows are added.

**Generate numbers 1 to 100**

```sql
WITH RECURSIVE seq(n) AS (
    SELECT 1
    UNION ALL
    SELECT n + 1 FROM seq WHERE n < 100
)
SELECT n FROM seq;
```

**Category hierarchy: each category and its depth (1 = root, 2 = child, …)**

```sql
WITH RECURSIVE cat_tree AS (
    SELECT id, name, parent_id, 1 AS depth
    FROM categories
    WHERE parent_id IS NULL

    UNION ALL

    SELECT c.id, c.name, c.parent_id, t.depth + 1
    FROM categories c
    JOIN cat_tree t ON t.id = c.parent_id
)
SELECT id, name, parent_id, depth FROM cat_tree ORDER BY depth, id LIMIT 40;
```

*(If your DB limits recursion depth, add something like `AND t.depth < 10` in the recursive part.)*

---

## When to use a CTE

- When the same subquery is used more than once.
- When you want to break a complex query into clear steps (read top to bottom).
- When you need a **recursive** query (hierarchies, number sequences).

---

## You try

1. **CTE:** Write a CTE that selects orders where `status = 'delivered'`. Then join that CTE to `customers` and show customer first name, last name, and count of delivered orders. Use GROUP BY.
2. **Recursive:** Use a recursive CTE to generate the numbers 1 to 50. From that result, select only the **even** numbers (n % 2 = 0 or similar).
3. **Hierarchy:** For `categories`, write a recursive CTE that for a given category id (e.g. 50) returns that category and **all its descendants** (children, grandchildren, …). Hint: anchor = the row where id = 50; recursive = rows whose parent_id is in the current level.

---

Tomorrow we'll touch **transactions**, **indexes**, and **real-world patterns** so you can write correct, fast, and maintainable SQL.

— **Reed**
