# Day 5: Joining Tables — INNER JOIN and LEFT JOIN

**Reed says:** *"Data is spread across tables: orders in one table, customers in another. Today we combine them with JOINs so we can see order and customer name in one result."*

---

## Why join?

- **orders** has `customer_id` but not the customer’s name.
- **customers** has `first_name`, `last_name`, and `id`.

To show "order id + customer name" we **join** the two tables on the column that links them: `orders.customer_id = customers.id`.

---

## INNER JOIN — only matching rows

**INNER JOIN** keeps only rows where the join condition is true in **both** tables.

**Orders with customer first and last name**

```sql
SELECT o.id AS order_id, o.total_amount, c.first_name, c.last_name
FROM orders o
INNER JOIN customers c ON c.id = o.customer_id
ORDER BY o.id
LIMIT 15;
```

- **o** and **c** are **table aliases** so we can write `o.customer_id` and `c.id` shortly.
- **ON c.id = o.customer_id** is the **join condition**: we match each order to the customer whose id equals that order’s customer_id.

**Order items with product name**

```sql
SELECT oi.id, oi.order_id, oi.quantity, oi.unit_price, p.name AS product_name
FROM order_items oi
INNER JOIN products p ON p.id = oi.product_id
ORDER BY oi.order_id
LIMIT 15;
```

**Orders with customer name and shipping city (three tables)**

```sql
SELECT o.id, c.first_name, c.last_name, o.total_amount, a.city
FROM orders o
INNER JOIN customers c ON c.id = o.customer_id
INNER JOIN addresses a ON a.id = o.shipping_address_id
ORDER BY o.id
LIMIT 15;
```

We chain JOINs: first order–customer, then order–address.

---

## LEFT JOIN — keep all rows from the left table

**LEFT JOIN** keeps every row from the **left** table. When there’s no matching row in the right table, the right side columns are **NULL**.

**All customers and how many orders they have (including zero)**

```sql
SELECT c.id, c.first_name, c.last_name, COUNT(o.id) AS order_count
FROM customers c
LEFT JOIN orders o ON o.customer_id = c.id
GROUP BY c.id, c.first_name, c.last_name
ORDER BY order_count DESC
LIMIT 20;
```

If we had used INNER JOIN, customers with no orders would disappear. With LEFT JOIN they stay, and COUNT(o.id) is 0 for them.

**All products and how many times they were ordered**

```sql
SELECT p.id, p.name, COUNT(oi.id) AS times_ordered
FROM products p
LEFT JOIN order_items oi ON oi.product_id = p.id
GROUP BY p.id, p.name
ORDER BY times_ordered DESC
LIMIT 15;
```

---

## Choosing INNER vs LEFT

- Use **INNER JOIN** when you only want rows that have a match in both tables (e.g. "orders that have a customer" — which is always true if the data is correct).
- Use **LEFT JOIN** when you want **all** rows from the left table and optional data from the right (e.g. "all customers and their order count" including customers with zero orders).

---

## You try

1. List **orders** with columns: order `id`, `total_amount`, and customer `first_name`, `last_name`. Use INNER JOIN. LIMIT 10.
2. List **order_items** with columns: `order_id`, `quantity`, `unit_price`, and product `name`. Use INNER JOIN. LIMIT 10.
3. List **all products** with their **category name** (join products to categories). Use INNER JOIN. LIMIT 10.
4. List **all customers** with the **count of orders** they have. Include customers with 0 orders. Use LEFT JOIN and GROUP BY. LIMIT 15.

---

Tomorrow we'll use **subqueries** and **derived tables** — queries inside queries — to answer more complex questions.

— **Reed**
