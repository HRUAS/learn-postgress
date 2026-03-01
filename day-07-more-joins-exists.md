# Day 7: More JOINs, EXISTS, and Self-Joins

**Reed says:** *"Today we go further: self-joins (a table joined to itself), EXISTS for 'has at least one,' and when to choose JOIN vs subquery."*

---

## Self-join: table joined to itself

A **self-join** is when you use the same table twice in one query, with different aliases. Use it when rows relate to other rows in the **same** table (e.g. categories with parent_id, or employees with manager_id).

**Category and its parent category name**

```sql
SELECT
    child.id,
    child.name AS category_name,
    parent.name AS parent_name
FROM categories child
LEFT JOIN categories parent ON parent.id = child.parent_id
ORDER BY child.id
LIMIT 20;
```

Here `child` and `parent` are both the `categories` table. We match `child.parent_id` to `parent.id` so we see the parent’s name. LEFT JOIN keeps categories that have no parent (root categories).

**Customers who share the same last name (pairs)**

```sql
SELECT a.id AS id1, a.first_name, a.last_name, b.id AS id2, b.first_name AS other_first
FROM customers a
INNER JOIN customers b ON b.last_name = a.last_name AND b.id < a.id
ORDER BY a.last_name
LIMIT 15;
```

We join customers to customers on same last name, and `b.id < a.id` so each pair appears only once.

---

## EXISTS and NOT EXISTS

**EXISTS (subquery)** is true if the subquery returns **at least one row**. We often use `SELECT 1` because we only care about "any row exists," not the data.

**Customers who have at least one order**

```sql
SELECT id, first_name, last_name, email
FROM customers c
WHERE EXISTS (SELECT 1 FROM orders o WHERE o.customer_id = c.id)
LIMIT 15;
```

**Products that have never been ordered**

```sql
SELECT id, name, sku
FROM products p
WHERE NOT EXISTS (SELECT 1 FROM order_items oi WHERE oi.product_id = p.id)
LIMIT 15;
```

**Why EXISTS?** It can be faster than IN when the right table is large, and it’s NULL-safe (unlike NOT IN when the subquery can return NULLs).

---

## Multiple JOINs in one query

You can chain several JOINs to bring in many tables.

**Order id, customer name, product name, quantity, line total**

```sql
SELECT o.id AS order_id, c.first_name, c.last_name, p.name AS product_name, oi.quantity, oi.line_total
FROM orders o
JOIN customers c ON c.id = o.customer_id
JOIN order_items oi ON oi.order_id = o.id
JOIN products p ON p.id = oi.product_id
ORDER BY o.id, oi.id
LIMIT 20;
```

**Products with category name and review count**

```sql
SELECT p.id, p.name, cat.name AS category_name, COUNT(r.id) AS review_count
FROM products p
JOIN categories cat ON cat.id = p.category_id
LEFT JOIN reviews r ON r.product_id = p.id
GROUP BY p.id, p.name, cat.name
ORDER BY review_count DESC
LIMIT 15;
```

---

## When to use what

| Goal | Prefer |
|------|--------|
| "Rows from A with matching data from B" | INNER JOIN or LEFT JOIN |
| "Rows from A that have at least one match in B" | EXISTS or IN (subquery) |
| "Rows from A that have no match in B" | NOT EXISTS or LEFT JOIN ... WHERE right.id IS NULL |
| "Relationship within the same table" | Self-join with two aliases |

---

## You try

1. **Self-join:** List categories with their **parent category name**. Use LEFT JOIN so categories with no parent still appear. Show category id, category name, parent name. LIMIT 20.
2. **EXISTS:** Find customers who have **never** placed an order. Use NOT EXISTS.
3. **Multiple JOINs:** List **orders** with columns: order id, customer first name, customer last name, **product name** (only one product per order for simplicity — pick one order_item per order if there are several, or use MIN/MAX). Hint: orders → order_items → products, and you may need to join customers too.

---

Tomorrow we'll learn **window functions**: ROW_NUMBER, RANK, and OVER () so we can rank and segment without losing row detail.

— **Reed**
