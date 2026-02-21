# Day 1: What Is SQL? SELECT and FROM

**Reed says:** *"Today we answer: What is SQL? What is a table? How do I read data? You'll run your first real queries."*

---

## What is SQL?

**SQL** (Structured Query Language) is the language we use to talk to a **database**. A database stores data in **tables**. A table has:

- **Rows** — one per record (e.g. one row per customer).
- **Columns** — one per attribute (e.g. first name, email, phone).

You write a **query** (a question in SQL), and the database returns a **result set** (rows and columns).

---

## Your first query: SELECT and FROM

To read data, you use **SELECT** and **FROM**:

- **SELECT** — which columns you want (or `*` for all).
- **FROM** — which table to read from.

**Get all columns from the customers table**

```sql
SELECT * FROM customers;
```

This returns every row and every column from `customers`. The `*` means "all columns."

**Get only some columns**

```sql
SELECT id, first_name, last_name, email FROM customers;
```

Now the result has only those four columns. The order of columns in your SELECT is the order in the result.

**Get the first 10 rows**

```sql
SELECT id, first_name, last_name FROM customers LIMIT 10;
```

`LIMIT 10` means "give me at most 10 rows." (We'll use LIMIT often so results stay small.)

---

## Table and column names

- **Table names** in this course: `customers`, `orders`, `products`, `categories`, `addresses`, `order_items`, `payments`, `inventory`, `reviews`.
- **Column names** are specific to each table. For example, `customers` has `id`, `first_name`, `last_name`, `email`, `phone`, `created_at`.
- If your database uses different casing (e.g. `Customers`), you may need to quote names: `"Customers"` (PostgreSQL) or `` `Customers` `` (MySQL/TiDB).

---

## Try different tables

**All columns from products (first 5 rows)**

```sql
SELECT * FROM products LIMIT 5;
```

**Only product name and price**

```sql
SELECT name, price FROM products LIMIT 5;
```

**All columns from orders (first 5 rows)**

```sql
SELECT * FROM orders LIMIT 5;
```

---

## Key terms

| Term | Meaning |
|------|--------|
| **Query** | A SQL statement you send to the database. |
| **Result set** | The rows and columns the database returns. |
| **SELECT** | Lists the columns you want (or `*` for all). |
| **FROM** | The table (or tables) to read from. |
| **LIMIT n** | Return at most n rows. |

---

## You try

1. Write a query that returns only `id`, `name`, and `price` from `products`. Use `LIMIT 10`.
2. Write a query that returns all columns from `categories` with `LIMIT 5`.
3. From `customers`, select only `first_name` and `last_name` and run it. How many rows do you get (without LIMIT)?

---

Tomorrow we'll **filter** rows with **WHERE** so you don't have to look at the whole table.

— **Reed**
