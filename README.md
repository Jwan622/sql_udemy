This is a udemy course. The language is postgresql

spreadsheets:
- good for one time analysis
- good for reasonable data set size
- quickly need to chart something else
- ability for untrained people to work with

database:
- data integrity, hard to change
- can handle massive amounts of data
- quickly combine different data sets
- can support data for websites

- spreadsheet tabs = database tables. You can think of it that way.
- columns in spreadsheet are columns in database
- rows in spreadhseets, rows in database.

various sql:
- postgresql
- mysql
- ms sql server express
- microsoft access
- sqllite
- mariasql

sql = structured query language  
**But this sql learned in this course can be applied to a variety of databases!**



## Section 2

#### SELECT queries:
- `Select col1, col2 from <table>`  
selects 2 columns from a table
  
- usage of * in selects might increase traffic between server and application. Try to query only specific columns.

- cases don't matter in sql \

#### DISTINCT queries:

`SELECT DISTINCT(rating) FROM film;`

#### COUNT queries:
- returns the number of input rows that match specific condition of a query

To count distinct names from a table:

`SELECT COUNT(DISTINCT(name)) from table;`

That will return a number

Parenthesis are necessary for here because `SELECT COUNT DISTINCT(name)) from table;` tells postgres to count a column called DISTINCT which doesn't exist.

How many distinct districts are customers from?

```sql
SELECT COUNT(DISTINCT(district))
FROM address;
```

#### WHERE queries:
- you can use operators like =, <> (not equal), AND, OR, NOT, <=

`SELECT name, choice, from <Table> where name = 'David'`
`SELECT name, choice, from <Table> where name = 'David' AND choice = 'Red'`
- you need to use single quotes.

```
SELECT title FROM film 
WHERE rental_rate > 4 
AND replacement_cost >= 19.99
AND rating='R';
```

the above is a query about movies where the rental rate cost is greater than 4 and the replacement cost is greater than or equal to 19.99 and the rating is R.

to count it you can do:

```
SELECT COUNT(title) FROM film 
WHERE rental_rate > 4 
AND replacement_cost >= 19.99
AND rating='R';
```

you can use COUNT(*) but the above query is more descriptive and informative and what the query is about.

### ORDER BY queries
- you can order by multiple columns:
```sql
SELECT company, name, sales FROM table ORDER BY company DESC, sales ASC
```

the second column is the secondary sort.
You can actually sort by columns that are not in select statement. It will still work.

```sql
SELECT  first_name, last_name FROM customer
ORDER BY store_id DESC, first_name ASC
```

### LIMIT queries:
- LIMIT goes at the end
- select only a few rows
- useful with ORDER BY to get top N rows of something

5 most recent payments:
```sql
SELECT * from payment
ORDER BY payment_date DESC
LIMIT 5;
```

#### BETWEEN queries:
- value >= low AND value >= high
- value BETWEEN low and high

- between is inclusive but NOT BETWEEN is not inclusive
- can be used in dates, must be in ISO format. date BETWEEN '2007-01-01' and '2007-02-01'. ISO is YYYY-MM-DD

payments between 8 and 9 dollars:
```sql
SELECT * From payments
WHERE amount BETWEEN 8 and 9
```

when using timestamps, it only goes up to 0:00 or midnight of that day so if you want payments on the 14th you have to write:

```sql
SELECT * from payment
WHERE payment_date BETWEEN '2007-02-01' AND '2007-02-15'
```


#### IN queries:

- checks to see if value is included list of multiple options

```sql
SELECT color FROM table where color IN ('red', 'blue')
```

the IN is like an OR statement

Other examples:
```sql
SELECT COUNT(*) from payment
WHERE amount IN (0.99, 1.98, 1.99)
```

```sql
SELECT DISTINCT(amount) from payment
WHERE amount IN (0.99, 1.98, 1.99)
ORDER BY amount;
```

you can use `NOT IN` too.


### LIKE and ILIKE
- pattern matching with strings
- useful for questions like "all emails ending in '@gmail.com'"
or all names that begin with 'A'
  
you can use wildcard characters % (any number of chars) and _ for single chars.

ALL names that begin with 'A':
- `WHERE name LIKE 'A%'`

All names that end with an 'a':
- WHERE name LIKE '%a'

notice the percentage sign can be anywhere.

LIKE is case sensitive.\
ILIKE is not case sensitive.

underscore (_) is just for single characters\
`...WHERE title like 'Mission Impossible _'`

for 2 characters, you can use 2 underscores!

to match Cheryl, Theresa, and Sherri you could do: `...WHERE name LIKE '_her%'`

for case insensitive:

```sql
SELECT * FROM customer
WHERE first_name ILIKE 'j%' AND last_name ILIKE 's%'
```

people with 'er' anywhere in the name:

```sql
SELECT * FROM customer
WHERE first_name ILIKE '%er%' OR last_name ILIKE '%er%'
```

`NOT LIKE` is also viable

# GROUP BY statements
GROUP BY allows us to aggregate data and see how data is distributed by category
HAVING - filtering with a GROUP BY

Main purpose is for single output from multiple inputs.\
Many aggregate functions, MAX, MIN, SUM, AVG, COUNT
- happy only in SELECT or HAVING clause.
- AVG returns a floating point with many decimals but then you can use ROUND to specify precision.

Aggregate functions are functions so you need parenthesis.\
Some examples:

```sql
SELECT MIN(replacement_cost) FROM film;
```

Below doesn't work because `MIN(replacement_cost)` only returns 1 value and so what film_id would you even associate with that? You need to use GROUP_BY. 
```sql
SELECT MIN(replacement_cost), film_id FROM film;
```


You can do:
```sql
SELECT MIN(replacement_cost), MAX(film_id) FROM film;
```

For avg and rounding:

```sql
SELECT ROUND(AVG(replacement_cost), 2) FROM film;
```


#### GROUP BY
- aggregate columns by some category
First you need to choose a categorical column to run the GROUP BY on. COULD be numbers, but are non-continuous but are still categories. Categorical columns could still be numeric like class levels on a ship or something.
  
Remember, aggregate function takes multiple values and reduces it to 1. GROUP by splits it up and then each section is run into the aggregate function.

![group_by](images/group_by.png)

syntax:
```sql
SELECT category_col, AGG(data_col)
FROM table
GROUP BY category_col
```

GROUP By must appear after FROM or where.

In the SELECT statement, columns must either have an aggregate statement function OR be in the GROUP BY call. If thecolumn is just being selected, it just be in the GROUP BY statement.

Can have multiple GROUP BY:

```sql
SELECT company, division, SUM(sales)
FROM finance_table
GROUP BY company, division
```
the above is number of sales per company per division.

**WHERE statements do not refer to the aggregation result, that should be what the HAVING statement is for. HAVING is for filtering GROUP BY aggregations** The WHERE Filtering happens before the GROUP BY statement.

IF you want to sort using ORDER, reference the entire function:

```sql
SELECT company, SUM(sales)
FROM finance_table
GROUP BY company
ORDER BY SUM(sales)
LIMIT 5
```

If you want to sort results based on the aggregate, reference the entire function.

Find customers who spent the most in order:
```sql
SELECT customer_id, SUM(amount) FROM payment
GROUP BY customer_id
ORDER BY SUM(amount) DESC
```

How much each customer spent with each staff member:

```sql
SELECT customer_id, staff_id, SUM(amount) FROM payment
GROUP BY customer_id, staff_id
ORDER BY customer_id
```


days with least amount transcations in terms of $$$:
```sql
SELECT DATE(payment_date), SUM(amount) FROM payment
GROUP BY DATE(payment_date)
ORDER BY SUM(amount) DESC
```

avg replcement cost of movies by film rating:
```sql
SELECT rating,
ROUND(AVG(replacement_cost), 2)
FROM film
GROUP BY rating
```

top 5 customers payments to award coupons to later:
```sql
SELECT customer_id, SUM(amount)
FROM payment
GROUP BY customer_id
ORDER BY SUM(amount) DESC
LIMIT 5
```

#### HAVING clause

What if we wanted to filter based on the `SUM(sales)` here? WHERE cannot do that. We need HAVING

![having.png](images/having.png)

Using WHERE, the sum(sales) doesn't happen until after the GROUP BY and WHERE and GOOGLE will be left out here.

We can write `HAVING SUM(sales) > 1000`

To find companies with sales > 1000 properly:

![having2.png](images/having2.png)

```sql
SELECT customer_id, SUM(amount) FROM payment
GROUP BY customer_id
HAVING SUM(amount) > 100
```

Number of customers per store:
```sql
SELECT store_id, COUNT(customer_id) FROM customer
GROUP BY store_id
HAVING COUNT(customer_id) > 300
```


## JOINS

- AS clause or aliasing
- discussing FULL joins, INNER joins, OUTER joins, UNIONS
- once you understand one join, you'll get the others quickly!

#### AS clause

Aliases can clarify:

```sql
SELECT SUM(amount) as net_revenue
FROM payment;
```

**AS gets executed at end of query so you cannot use it in a WHERE. Cannot use the alias in a WHERE or GROUP BY segment. You can only use it in the SELECT**

You can do this:

```sql
SELECT customer_id, SUM(amount) AS total_spent
FROM payment
GROUP BY customer_id
HAVING SUM(AMOUNT) > 100
```

cannot do this:

```sql
SELECT customer_id, SUM(amount) AS total_spent
FROM payment
GROUP BY customer_id
HAVING total_spent > 100
```


#### INNER JOIN

Why do JOINS?
- allows us to combine multiple tables together
- WHY different JOINS? deal with data present only in one of the joined tables.

INNNER JOIN will result with the set of records that match in both tables.

![inner_join](images/inner_join.png)

INNER JOIN is the only join that is symmetrical, meaning you can switch the table you SELECT from and JOIN from and to and it'll be the same.

```sql
SELECT payment_id, payment.customer_id, first_name
FROM customer
INNER JOIN payment
ON payment.customer_id = customer.customer_id
```

is the same as:

```sql
SELECT payment_id, payment.customer_id, first_name
FROM payment
INNER JOIN customer
ON payment.customer_id = customer.customer_id
```

![inner_join_diagram](images/inner_join_diagram.png)

This example gives you back all the columns in an INNER JOIN:

![inner_join_example](images/inner_join_example.png)

If you want to filter out columns and there is a duplicate column name, you have to specify the table. If you want to SELECT a column that only appears in one of the tables, you don't need to specify the table name prior to the column name in the SELECT.

![inner_join_example_select_columns](images/inner_join_example_select_columns.png)

JOIN without the INNER; postgresql will treat it as an INNER JOIN.

#### OUTER JOins
- allows us to specify how to deal with values only being presnet in one of the tables being joined vs INNER JOIN which only gets rows with values in both tables.

FULL OUTER JOIN:
- also symmetrical: table order doesn't matter since we're grabbing eveerything.

To get opposite of INNER JOIN, you need to use WHERE with FULL OUTER JOIN, to get rows unique to each table in otherwise rows that are not found in both tables:

![full_outer_join_where](images/full_outer_join_where.png)

Another example: make sure we don't have payment info attached to a customer, or customer info not attached to payments. Let's see if we have any rows:

```sql
SELECT * FROM customer
FULL OUTER JOIN payment
ON customer.customer_id = payment.customer_id
WHERE customer.customer_id IS NULL
OR payment.customer_id IS NULL
```

That's how you would do it. That takes all the records at first using FULL OUTER JOIN then filters using WHERE

#### LEFT OUTER JOIN
- results in set of records that are in the left table, if there is no match with the right table, the results are null

- the SELECT and LEFT OUTER JOIN table matter since not symmetrical in the venn diagram. Order matters.

- below all rows are in TABLE A but we don't return anything exclusvie to table B in:

```sql
SELECT * FROM TABLE A
LEFT OUTER JOIN TABLE B
ON TABLEA.col_match = TABLEB.col_match
```

![left_outer_join](images/left_outer_join.png)

notice things that were exclusive to table B we don't see in the left outer join. so we can see that table order matters.

To get the venn diagram where only the left part of the left circle is shaded, use WHERE.

![left_outer_join1](images/left_outer_join1.png)

rows exclusive to table A:

![left_outer_join2](images/left_outer_join2.png)

LEFT JOIN = LEFT OUTER JOIN in sql. same thing syntacically.

IN this query:
```sql
SELECT film.film_id, title, inventory_id, store_id
FROM film
LEFT JOIN inventory ON
inventory.film_id = film.film_id
```

all rows from film will appear because film is on left side. You'll only see rows from film or in both film or inventory, but not in inventory and not film. Some of the rows returned will have NULL for inventory_id

#### RIGHT JOIN
- same thing as a LEFT JOIN but the tables reversed. You could just switch order of tables in a LEFT JOIN and it's = RIGHT JOIN
- return back rows exclusively in table B (The right join table) or both table B and table A. Do not return back rows from table A (the non right join table).

- use a where tableA.id IS NULL to only return back rows exclusively in table B.

![right_join](images/right_join.png)

#### UNION operator
- used to combine results of 2 or more SELECT statements. serves to concatenate 2 results together.

ex:
```sql
SELECT column_name(s) FROM table1
UNION
SELECT column_name(s) FROM table2
```

should be able to stack results on top of each other. The rows should be pastable on top of each other so same columns.


#### Example of a Multiple JOin query:

```sql
SELECT title, first_name, last_name FROM actor as act
    INNER JOIN film_actor as factor ON
        factor.actor_id = act.actor_id
    INNER JOIN film as f ON
        f.film_id = factor.film_id
WHERE first_name = 'Nick' AND last_name = 'Wahlberg'
```

We needed to join 3 tables here in the above query. Find distinct titles of Nick Wahlberg films.

vid here: https://www.udemy.com/course/the-complete-sql-bootcamp/learn/lecture/18997814#overview


## Advanced Topics Section 6
Topics are:
- timestamps and extract
- math functions
- string functions
- sub-query
- self-joins

Postgresql can hold:
TIME - contains only time (hours minutes and seconds)
DATE - contains only date (only date month day and year)
TIMESTAMP - contains date and time (both)
TIMESTAMPZ - containts date, time, and timezone

you may not need TIMESTAMPZ. For keeping track of worker hours, you just need the time they clocked in and the time they clocked out... so only TIME.

```sql
SHOW TIMEZONE
```
The above shows the timezone we're working in.

```sql
SELECT NOW()
```
gets timestamp in timestamp data format:

![now_function](images/now_function.png)


```sql
SELECT TIMEOFDAY()
```
![timeofday](images/timeofday.png)

this is a string, not a timestamp


The below gets your just the date, not a function call:
```sql
SELECT CURRENT_DATE
```


#### EXTRACT function

- allows you to get a subcomponent of a date value: like the year, month, day, quarter, week

If you have a full timestamp, and you just want a year, you can get just the year

```sql
EXTRACT(YEAR from date_col)
```

example:

```sql
SELECT EXTRACT(YEAR FROM payment_date) myyear
FROM payment
```

results:

![extract](images/extract.png)


#### AGE

calculates current age of a timestamp in relation to current date
```sql
AGE(date_col)
```

takes current date and calculate how old timestamp in database is. If today is 2020, and column is 2000, returns 20 years.

example:

```sql
SELECT AGE(payment_date) as myyear
FROM payment;
```


results:
![age](images/age_func.png)


combine:
![age_extract_func](images/age_extract_func.png)

To find number of payments on a monday:

```sql
SELECT COUNT(*)
FROM payment
WHERE EXTRACT(dow from payment_date) = 1;
```

#### to_char
- general function to convert date types to text

```sql
TO_CHAR(date_col, `mm-dd-yyyy`)
```

```sql
SELECT TO_CHAR(payment_date, 'dd/mm-YYYY')
FROM payment;
```

the last argument is the text formatting which you can use dashes or slashes to format:

example:

![to_char_date](images/to_char_date.png)


#### Mathematical Operators
- you can apply operators to one or more columns
- everything is available to you in postgresql, even bitwise

even functions: abs(), log(), round(), power() and trig functions and random() for complex sql queries.

examples:

```sql
SELECT ROUND(rental_rate/replacement_cost, 4) * 100 AS percent_cost 
FROM film
```
The above is percentage cost of rental rate of replacement cost
![percent_cost](images/percent_cost.png)


#### String Functions
- you can also do string functions

functions that you can run:
- string concatenation
- substring and searching YOu can extract substring from
- lower case
- position index
- length of string
etc

examples:
```sql
SELECT LENGTH(first_name) FROM customer
```

```sql
SELECT first_name || ' ' || last_name FROM customer AS full_name
```
the above adds a space in between first and last name


first letter and non-capitalized last name in an email:

```sql
SELECT LEFT(first_name, 1) || LOWER(last_name) || '@gmail.com' FROM customer AS full_name
```


#### Subqueries
Let's look at subqueries that return a value, subqueries using EXISTS

How to find students who did better than avg grade:

First part, find the avg grade:
```sql
SELECT AVG(grade) from test_scores
```

second part is something like this which finds the student and grade from a table:

```sql
SELECT student, grade
FROM test_scores
```

then find students above the avg:
```sql
SELECT student, grade
FROM test_scores
WHERE grade > (SELECT AVG(grade) 
FROM test_scores)
```

subquery gets run first inside the parenthesis and returns a value so that the query is like:

```sql
SELECT student, grade
FROM test_scores
WHERE grade > 70
```

previously you would have to run 1 query and use the reuslt in another in two steps. This allows us to do it in one step.

Using IN in subquery:
Instead of join, you can use IN to see if student is in a list

```sql
SELECT student, grade
FROM test_scores
where student IN
(SELECT student
    FROM honor_roll_table)
```

the above is easy to reason above, more so than a join.


More examples:
films where the rental rate is greater than avg:
```sql
SELECT title, rental_rate
FROM film
WHERE rental_rate > 
(SELECT AVG(rental_rate) FROM film)
```

subquery returns multiple results so use the IN operator. This query gives back film titles where the film_id is in the subquery list. We needed to do this because the film_id isn't in the rental table. Inventory does have the film_id and the rental_table does have an inventory_id so we can join inventory and rental tables using `inventory_id`. Below, the sub_query gives back a list of film_ids which you can then use to select films and titles from another table (the film table)

![subquery_join2](images/subquery_join2.png)

![subquery_join1](images/subquery_join1.png)


The following returns customers who made payment over $11 using EXISTS in the subquery:
```sql
SELECT first_name, last_name
FROM customer AS c
WHERE EXISTS(
    SELECT * FROM payment as p
    WHERE p.customer_id = c.customer_id
    AND amount > 11
)
```

the subquery checks if the payment > 11 exists for the customer and if so, the main query will return the first and last_name

NOT also works with EXISTS:

```sql
SELECT first_name, last_name
FROM customer AS c
WHERE NOT EXISTS(
    SELECT * FROM payment as p
    WHERE p.customer_id = c.customer_id
    AND amount > 11
)
```
