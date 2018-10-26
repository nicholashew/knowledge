# SQL Style Guides

## Tables Name

* Use a collective name or, less ideally, a plural form. For example (in order of preference) `staff` and `employees`.
* Do not prefix with `tbl` or any other such descriptive prefix or Hungarian notation.
* Never give a table the same name as one of its columns and vice versa.
* Avoid, where possible, concatenating two table names together to create the name of a relationship table. Rather than `cars_mechanics` prefer `services`.

```sql
USE NorthWind
GO

CREATE TABLE dbo.staff (
  staff_id INT IDENTITY(1,1),
  first_name NVARCHAR(50) NOT NULL,
  last_name NVARCHAR(50) NOT NULL,
  dob DATETIME NULL
)
```

## Columns

* Always use the singular name.
* Where possible avoid simply using id as the primary identifier for the table.
* Do not add a column with the same name as its table and vice versa.
* Always use lowercase except where it may make sense not to such as proper nouns.

## Reserved Words

Always use UPPERCASE for reserved keywords. 

Eg: `SELECT, AS, FROM, WHERE, LIKE, OR, ORDER BY, ASC, DESC, etc...`.

## Aliasing

Always include the AS keyword when aliasing a variable, it's easier to read when explicit.

```sql
SELECT
  substr(subscribe_date, 1, 6) AS month
FROM dbo.subscribe_packages
LIMIT 10
```

## Left Align Root Keywords and Code Blocks

Root keywords should all start on the same character boundary and on their own line. 
This is counter to the common "rivers" pattern described here.

```sql
SELECT
  user_id,
  created_date
FROM dbo.users
WHERE created_date > '20180101'
  AND email LIKE '%@sample.com'
LIMIT 10
```

**Do not include multiple arguments in one line.**

```sql
-- BAD
SELECT user_id, created_date
FROM dbo.users
WHERE created_date > '20180101'
  AND email LIKE '%@sample.com'
LIMIT 10
```

## Line Spacing

`AND` and `OR` should always be at the beginning of the line. For example:

```sql
-- Good
WHERE
  created_date > '20180101'
  AND email LIKE '%@sample.com'

-- Good
WHERE created_date > '20180101'
  AND email LIKE '%@sample.com'

-- Bad
WHERE created_date > '20180101' AND email LIKE '%@sample.com'

-- Bad
WHERE 
  created_date > '20180101' AND 
  email LIKE '%@sample.com'
```

## Parentheses

If parentheses span multiple lines:
* The opening parenthesis should terminate the line.
* The closing parenthesis should be lined up under the first character of the line that starts the multi-line construct.
* The contents of the parentheses should be indented one level.

```sql
WITH sample_cto AS (
  SELECT user_id
  FROM dbo.users
  WHERE email LIKE '%@sample.com'
)
```

## JOIN Queries

```sql
-- Good
SELECT
  sp.subscribe_id,
  sp.substr(subscribe_date, 1, 6) AS month,
  u.user_id,
  u.email
FROM dbo.subscribe_packages sp
INNER JOIN dbo.users u ON u.user_id = sp.user_id
WHERE sp.subscribe_date > '20180101'
  AND u.email LIKE '%@sample.com'
LIMIT 50

-- Bad
SELECT sp.subscribe_id, sp.substr(subscribe_date, 1, 6) AS month, u.user_id, u.email
FROM dbo.subscribe_packages sp
INNER JOIN dbo.users u 
ON u.user_id = sp.user_id
WHERE sp.subscribe_date > '20180101' AND u.email LIKE '%@sample.com'
LIMIT 50
```

## Nested Queries

Avoid use nested queries. Instead, use common table expressions to improve readability.

```sql
-- Good
WITH sample_cto AS (
  SELECT user_id
  FROM dbo.users
  WHERE email LIKE '%@sample.com'
)

SELECT *
FROM sample_cto
LIMIT 10
```

```sql
-- Avoid
SELECT *
FROM (
  SELECT user_id
  FROM dbo.users
  WHERE email LIKE '%@sample.com' 
)
LIMIT 10
```

## References

* [SQL style guide by Simon Holywell](https://www.sqlstyle.guide/)
* [Firefox SQL Style Guide](https://docs.telemetry.mozilla.org/concepts/sql_style.html)