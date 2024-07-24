# SQL Cheat Sheet #
These are some commands to refresh memory when coming back to SQL. 

Here is a link that will show you have to pull data from one server to another

https://www.youtube.com/watch?v=MZ1UfBPoxvg

__To pull a database you__
```
SELECT * FROM cd.members
```
__Filter function__
```
WHERE memid BETWEEN 0 AND 5
```
__Pull specific data__
```
WHERE memid IN (1,2)
```
__Order data__ 
```
ORDER BY memid ASC
```
__Regular Expressions__

Similar to python regular expressions we can parse find data that is similar. '%a%' means anything with an a in it
```
'A%' = starts with an A
'%a' = ends with an a
'_a%' = has an a in the second position
'a___%' = starts with an a and is at least three letters long
'a%o' = starts with an a and ends with an o
```
Regular Expressions using NOT LIKE
```
WHERE members.firstname NOT LIKE '%a%'
```

__GROUP BY__

Aggregate data together such as, the returned data table is the number of people for a given surname
```
SELECT COUNT(*),surname FROM cd.members
GROUP BY surname 
```
Use the __having__ function to filter a group by even further, need to use on a GROUP BY after!
```
SELECT COUNT(*),surname FROM cd.members
GROUP BY surname
HAVING COUNT(*) > 2
```
This is how you do a GROUP BY function in practise, almost think of it as a SUMIF
```
SELECT facid,SUM(slots)
FROM cd.bookings
WHERE EXTRACT(Month FROM starttime) = 9
GROUP BY facid
```
__Change the name of tables__
```
SELECT surname AS members FROM cd.members
```
__Joins__

Joins are where you stich together multiple data tables
```
SELECT column_name(s)
FROM table1
JOIN table2
ON table1.column_name = table2.column_name;
```
There are multiple ways to join a table:
```
INNER = only present values that overlap
LEFT = present values that overlap + null values for table1
RIGHT = present values that overlap + null values for table2
OUTER = Everything
```
__Timestamp Extraction__ 
```
SELECT EXTRACT(YEAR FROM payment_date)
FROM payment
```
Instead of YEAR you can also use
```
DAY
MONTH
HOUR
```

__Sub-querys__

Pull certain data columns and filter using the WHERE & SELECT fuction
```
SELECT a.studentid, a.name, b.total_marks
FROM student a, marks b
WHERE a.studentid = b.studentid AND b.total_marks >
(SELECT total_marks
FROM marks
WHERE studentid =  'V002');
```
__Create a table__
```
CREATE TABLE info(
   info_id SERIAL PRIMARY KEY
   title VARCHAR(500) NOT NULL
   person VARCHAR(50) NOT NULL UNIQUE
)
```
```
CREATE TABLE employees(
  emp_id SERIAL PRIMARY KEY,
  first_name VARCHAR(50) NOT NULL,
  last_name VARCHAR(50) NOT NULL,
  birthdate DATE CHECK (birthdate > '1900-01-01'),
  hire_date DATE CHECK(hire_date > birthdate),
  salary INTEGER CHECK (salary > 0 )
)
```
__Alter a table__
```
ALTER TABLE information
RENAME TO new_info

ALTER TABLE new_info
RENAME COLUMN person TO people

ALTER TABLE new_info
ALTER COLUMN people DROP NOT NULL 

ALTER TABLE new_info
ALTER COLUMN people SET NOT NULL 

ALTER TABLE new_info
DROP COLUMN people
```
__Amend a Column__
```
INSERT INTO employees(
	first_name, 
	last_name, 
	birthdate,
	hire_date,
	salary)
 VALUES
 ('Jose', 
  'Portilla',
  '1990-11-03', 
  '2010-01-01', 
  100)
```
__GENERAL CASE Statment__
```
SELECT customer_id,  
CASE 
WHEN (customer_id <= 100) THEN 'Premium'
WHEN (customer_id BETWEEN 100 AND 200) THEN 'Plus'
ELSE 'Normal'
END
FROM customer
```
__SUMIF function__
```
SELECT
SUM(CASE rental_rate
WHEN 0.99 THEN 1
ELSE 0 
END)
FROM film
```
__Multiple SUMIFS__
```
SELECT
SUM(CASE rental_rate
WHEN 0.99 THEN 1
ELSE 0 
END) AS bargains,
SUM(CASE rental_rate
WHEN 2.99 THEN 1
ELSE 0
END) AS regular
FROM film
```
__Make NULL values 0__ 
```
COALESCE( )
```


__Cast Function__
The CAST function to convert something into an integer
```
SELECT CAST('5' AS INTEGER) AS new_int
```
Count the number of integeters in a columns using CAST
```
SELECT CHAR_LENGTH(CAST(inventory_id AS VARCHAR)) FROM rental
```
If a result fails return a null (i.e. if the SUM(CASE... returns a 0 return a null for the whole query
```
SELECT (
SUM(CASE WHEN department = 'A' THEN 1 ELSE 0 END)
/NULLIF(SUM(CASE WHEN department = 'B' THEN 1 ELSE 0 END), 0)
) AS dept_ratio
FROM depts
```
