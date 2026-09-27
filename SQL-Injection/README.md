# SQL Injection

SQL Injection (SQLi) is a vulnerability that occurs when untrusted user
input is incorporated into SQL queries without proper parameterization.

This can allow an attacker to alter the intended SQL query and potentially
access, modify, or delete data.

---

## 1. Basic SQL Injection

Consider a vulnerable query:

SELECT * FROM products
WHERE category = 'Gifts'
AND released = 1;

If user input is directly inserted into the query, special SQL syntax may
alter the intended logic.

For example, a boolean condition can change the behavior of the WHERE clause.

### Key idea

The important question is not simply:

> "What payload should I use?"

Instead:

> "How will my input change the SQL query constructed by the application?"

---

## 2. Authentication Bypass

A vulnerable authentication query might conceptually look like:

SELECT * FROM users
WHERE username = 'USER'
AND password = 'PASSWORD';

If input is not safely handled, SQL syntax can potentially modify the
authentication logic.

This can result in authentication being bypassed.

### What I learned

Authentication should never depend on directly concatenating user input
into SQL queries.

---

## 3. UNION-Based SQL Injection

UNION attacks can be used when an application returns the results of a SQL
query in its HTTP response.

Example:

SELECT name, description
FROM products
WHERE category = 'Gifts';

An attacker may attempt to append another SELECT statement using UNION.

### Requirements for UNION

The combined queries generally need:

1. The same number of columns.
2. Compatible data types in corresponding columns.

---

## 4. Determining the Number of Columns

One technique is to increment the column index:

ORDER BY 1--
ORDER BY 2--
ORDER BY 3--

When the specified column index exceeds the number of columns in the query,
the database may return an error.

Another approach is to use NULL values:

UNION SELECT NULL--
UNION SELECT NULL,NULL--
UNION SELECT NULL,NULL,NULL--

The number of NULL values can be increased until the UNION query is accepted.

---

## 5. Finding Columns With Useful Data Types

Once the number of columns is known, different columns can be tested with
string values.

For example:

UNION SELECT 'a',NULL,NULL--

Then:

UNION SELECT NULL,'a',NULL--

The objective is to identify which returned columns accept string data.

---

## 6. Identifying the Database

Different database systems expose different functions and syntax.

Examples of version queries include:

Microsoft SQL Server:
SELECT @@version

Oracle:
SELECT banner FROM v$version

PostgreSQL:
SELECT version()

MySQL:
SELECT @@version

Database identification helps determine which SQL syntax and functions
are available.

---

## 7. Discovering Tables and Columns

Some database systems expose metadata through information-schema views.

For example:

SELECT * FROM information_schema.tables

and:

SELECT * FROM information_schema.columns

These can be used to understand the database schema when the application is
vulnerable to UNION-based SQL injection.

---

## 8. Blind SQL Injection

In blind SQL injection, the application does not directly return the
results of the injected query.

Instead, information may be inferred from changes in application behavior.

Two common forms are:

### Boolean-based blind SQLi

The attacker asks a question that produces either a true or false condition.

For example, the response may differ depending on whether:

condition = TRUE

or:

condition = FALSE

### Time-based blind SQLi

The attacker introduces a database delay when a condition is true.

If the HTTP response takes significantly longer, this can provide information
about the result of the injected condition.

---

## 9. Error-Based SQL Injection

Error-based SQL injection uses database errors as an information channel.

An attacker may deliberately cause an error whose behavior depends on a
database value.

The difference between:

TRUE → error

FALSE → no error

can sometimes be used to infer information.

---

## 10. SQL Injection Methodology

My general methodology:

1. Identify input that reaches the server.
2. Establish a normal baseline request.
3. Test whether special characters change behavior.
4. Determine whether the input is interpreted as SQL.
5. Identify the database type where possible.
6. Determine the number of columns for UNION attacks.
7. Identify useful data types.
8. Identify database structure.
9. Extract only the information required for the authorized lab.
10. Understand the root cause and mitigation.

---

## 11. Burp Suite Workflow

Typical workflow:

Browser
   ↓
Burp Proxy
   ↓
HTTP request
   ↓
Modify parameter
   ↓
Repeater
   ↓
Compare responses

Burp Repeater is particularly useful because it allows individual
parameters to be modified repeatedly while observing changes in the
server response.

---

## 12. Prevention

The primary defense against SQL Injection is parameterized queries /
prepared statements.

Instead of constructing SQL by concatenating user input, applications
should separate SQL code from data.

Additional defenses include:

- Input validation
- Least-privilege database accounts
- Safe ORM usage
- Avoiding unnecessary database error disclosure
- Secure coding practices
