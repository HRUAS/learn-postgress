# Day 3: Sorting and Limiting Results

**Reed says:** *"Today we control the order of rows (ORDER BY) and how many we get (LIMIT and OFFSET). These are used in almost every real query."*

---

## ORDER BY: sort the result

By default, the order of rows is not guaranteed. **ORDER BY** sorts the result by one or more columns.

**Sort customers by last name (A–Z)**

```sql
SELECT id, first_name, last_name FROM customers ORDER BY last_name LIMIT 20;
```

**Sort by last name descending (Z–A)**

```sql
SELECT id, first_name, last_name FROM customers ORDER BY last_name DESC LIMIT 20;
```

- **ASC** — ascending (default): small to big, A to Z.
- **DESC** — descending: big to small, Z to A.

**Sort products by price, cheapest first**

```sql
SELECT id, name, price FROM products ORDER BY price ASC LIMIT 15;
```

**Sort orders by total_amount, highest first**

```sql
SELECT id, customer_id, total_amount, status FROM orders ORDER BY total_amount DESC LIMIT 15;
```

---

## Sort by multiple columns

When the first column has ties, the second column breaks the tie.

**Sort by last_name, then by first_name**

```sql
SELECT id, first_name, last_name FROM customers ORDER BY last_name, first_name LIMIT 20;
```

**Sort orders by customer_id, then by total_amount descending**

```sql
SELECT id, customer_id, total_amount FROM orders ORDER BY customer_id, total_amount DESC LIMIT 20;
```

---

## ORDER BY with column position

You can use the position of the column in the SELECT list (1-based):

```sql
SELECT id, name, price FROM products ORDER BY 3 DESC LIMIT 10;
```

Here `3` means "third column" (price). Using the column name is usually clearer: `ORDER BY price DESC`.

---

## LIMIT and OFFSET

- **LIMIT n** — return at most n rows.
- **OFFSET m** — skip the first m rows, then return the rest (often used with LIMIT for "pages").

**First 10 products by price**

```sql
SELECT id, name, price FROM products ORDER BY price LIMIT 10;
```

**Next 10 products (skip first 10)**

```sql
SELECT id, name, price FROM products ORDER BY price LIMIT 10 OFFSET 10;
```

**"Third page" of 10 rows**

```sql
SELECT id, name, price FROM products ORDER BY price LIMIT 10 OFFSET 20;
```

Order of clauses: **WHERE** (if any), then **ORDER BY**, then **LIMIT** and **OFFSET**.

---

## Full SELECT order

A typical query looks like this:

```sql
SELECT   id, name, price
FROM     products
WHERE    price > 50
ORDER BY price DESC
LIMIT    10 OFFSET 0;
```

Order of keywords: SELECT → FROM → WHERE → ORDER BY → LIMIT / OFFSET.

---

## You try

1. List the **5 most expensive** products. Show `id`, `name`, `price`. Use ORDER BY and LIMIT.
2. List customers sorted by `last_name` ascending, then `first_name` ascending. Show `first_name`, `last_name`. Use LIMIT 15.
3. List orders sorted by `total_amount` descending. Show `id`, `customer_id`, `total_amount`. Return rows 11–20 (second "page" of 10). Use LIMIT and OFFSET.
4. List products with `price` greater than 100, sorted by `price` ascending, and return only the first 5 rows.

---

Tomorrow we'll **aggregate** data: COUNT, SUM, AVG, and **GROUP BY** to get summaries.

— **Reed**
