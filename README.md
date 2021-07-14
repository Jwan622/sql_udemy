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


