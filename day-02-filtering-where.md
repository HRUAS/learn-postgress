# Day 2: Filtering Rows with WHERE

**Reed says:** *"Today we learn how to ask for only the rows we care about: filtering with WHERE, comparison operators, AND/OR, and how to handle NULL."*

---

## Why filter?

Tables can have thousands of rows. We usually want a **subset**: e.g. "only delivered orders" or "only customers whose first name is Mary." **WHERE** lets you describe that subset.

---

## Basic WHERE

**WHERE** comes after **FROM**. Only rows that satisfy the condition are returned.

**Customers whose first name is Mary**

```sql
SELECT id, first_name, last_name, email FROM customers WHERE first_name = 'Mary' LIMIT 10;
```

Strings are in single quotes: `'Mary'`. The comparison is case-sensitive in many databases (so `'mary'` may not match "Mary").

**Products that cost more than 100**

```sql
SELECT id, name, price FROM products WHERE price > 100 LIMIT 10;
```

**Orders with total_amount less than or equal to 50**

```sql
SELECT id, customer_id, total_amount, status FROM orders WHERE total_amount <= 50 LIMIT 10;
```

---

## Comparison operators

| Operator | Meaning | Example |
|----------|--------|--------|
| `=` | Equal | `WHERE status = 'delivered'` |
| `<>` or `!=` | Not equal | `WHERE status <> 'cancelled'` |
| `<` | Less than | `WHERE price < 50` |
| `>` | Greater than | `WHERE price > 100` |
| `<=` | Less than or equal | `WHERE total_amount <= 200` |
| `>=` | Greater than or equal | `WHERE quantity >= 2` |

**Example: Orders that are not pending**

```sql
SELECT id, customer_id, status, total_amount FROM orders WHERE status != 'pending' LIMIT 10;
```

---

## AND and OR

- **AND** — all conditions must be true.
- **OR** — at least one condition must be true.

**Orders that are delivered and over 100**

```sql
SELECT id, customer_id, status, total_amount FROM orders
WHERE status = 'delivered' AND total_amount > 100
LIMIT 10;
```

**Products with price less than 20 OR greater than 500**

```sql
SELECT id, name, price FROM products
WHERE price < 20 OR price > 500
LIMIT 10;
```

**Combine AND and OR (use parentheses)**

```sql
SELECT id, first_name, last_name FROM customers
WHERE (first_name = 'James' OR first_name = 'Mary') AND last_name = 'Smith'
LIMIT 10;
```

Parentheses make it clear: we want (James or Mary) and last name Smith.

---

## IN and NOT IN

**IN** — value must be in a list.

**Orders with status either 'pending' or 'shipped'**

```sql
SELECT id, customer_id, status FROM orders
WHERE status IN ('pending', 'shipped')
LIMIT 10;
```

**NOT IN** — value must not be in the list.

**Products with price not 50 or 100**

```sql
SELECT id, name, price FROM products
WHERE price NOT IN (50, 100)
LIMIT 10;
```

---

## BETWEEN

**BETWEEN** includes both ends.

**Products with price between 50 and 100**

```sql
SELECT id, name, price FROM products
WHERE price BETWEEN 50 AND 100
LIMIT 10;
```

Same as: `WHERE price >= 50 AND price <= 100`.

---

## NULL: missing values

Some rows have **no value** in a column (e.g. no phone number). That’s **NULL**. You cannot use `= NULL`; use **IS NULL** or **IS NOT NULL**.

**Customers who have no phone**

```sql
SELECT id, first_name, last_name, phone FROM customers WHERE phone IS NULL LIMIT 10;
```

**Customers who have a phone**

```sql
SELECT id, first_name, last_name, phone FROM customers WHERE phone IS NOT NULL LIMIT 10;
```

---

## LIKE: pattern matching

**LIKE** works with placeholders:

- **%** — any sequence of characters (including none).
- **_ ** — exactly one character.

**Customers whose first name starts with 'J'**

```sql
SELECT id, first_name, last_name FROM customers WHERE first_name LIKE 'J%' LIMIT 10;
```

**Customers whose email contains 'example'**

```sql
SELECT id, email FROM customers WHERE email LIKE '%example%' LIMIT 10;
```

---

## You try

1. List orders where `total_amount` is greater than 200. Show `id`, `customer_id`, `total_amount`. Use LIMIT 5.
2. List customers whose `last_name` is 'Smith'. Show `id`, `first_name`, `last_name`.
3. List products where `price` is between 10 and 30 (inclusive). Show `id`, `name`, `price`. Use LIMIT 5.
4. List orders where `status` is 'delivered' or 'shipped'. Use IN.

---

Tomorrow we'll **sort** results with **ORDER BY** and control how many rows with **LIMIT**.

— **Reed**
