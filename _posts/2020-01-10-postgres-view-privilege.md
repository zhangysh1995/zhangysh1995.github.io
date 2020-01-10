
## Introduction to view privilege in PostgreSQL

Suppose you create a table using `CREATE TABLE phone_data (person text, phone text, private boolean);`,
that is:

````
| Column  | Type    |
|:--------|:--------|
| person  | text    |
| phone   | text    |
| private | boolean |
````

What's in the table:

````
zhangys=# select * from phone_data;
 person |     phone     | private
--------+---------------+---------
 me     | Redmi K20 Pro | t
 Tom    | Redmi K20     | f
(2 rows)
````
And you create a view `phone_number` on the table:

````
CREATE VIEW phone_number AS
    SELECT person, CASE WHEN NOT private THEN phone END AS phone
    FROM phone_data;
````

Show the view:
````
zhangys=# select * from phone_number;
 person |   phone
--------+-----------
 me     |
 Tom    | Redmi K20
(2 rows)
````

Now who could access the table and the view?

````
| User     | phone_number | phone_data |
|:---------|:-------------|:-----------|
| postgres | yes          | yes        |
| you      | yes          | yes        |
| others   | no           | no         |
````

Will another non-superuser be able to access the tables? **NO**.

You need to grant a user the privileage based on what operations it uses:

````
GRANT SELECT ON phone_number TO accessor;
````

Then `accessor` could use `SELECT` on the view.

````
| User     | phone_number | phone_data |
|:---------|:-------------|:-----------|
| postgres | yes          | yes        |
| you      | yes          | yes        |
| accessor | no           | select     |
| others   | no           | no         |
````

###  References:

*  https://www.postgresql.org/docs/10/rules-privileges.html
*  https://www.postgresql.org/docs/current/functions-conditional.html
