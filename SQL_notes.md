### SQL Notes -- 8.30.16

##### Entity Relationship Diagram - ERD

+ Primary Key (PK)
  + The main column header, identifies all the data in the column beneath it, in a specific table.   
  + When a PK is referenced in a separate table it is known as a Foreign Key (FK) to the separate table.
    + Note: when a PK is used in another table that has its own PK with the exact same name as the FK, it must be specified explicitly in reference by using the table-name.FK method.  
  + The most common relationship between multiple tables (databases?) is a "one-to-many" relationship.  The notion of "many-to-many" is highly complicated and is, for most practical purposes, going to be avoided.


##### Terminal and PSQL

+ In terminal, type `psql` to begin a PSQL session, then can do a command-space search for "postgress", open it, and go from there.  Opening Postgres without having PSQL running will fail to open Postgres.  
+ To exit PSQL use `\q`



##### Aggregate functions

When using an aggregate function, such as `COUNT()` or `AVERAGE()`, you have to include the remaining columns in the `SELECT` statement that aren't part of the aggregate functions in a `GROUP BY` statement.  Period.  

Note you can do an `ORDER BY`, a `GROUP BY`, or any other aggregate wrap-up function on a column you aren't `SELECT`ing for.  I'm not sure why you'd want to do this, but it is valid.

`HAVING` is used basically as a `WHERE` statement for aggregate functions.  For example, you can't do a query with a `WHERE` clause asking for cells that have a `COUNT` of 5, say.  It will crash (unless there is a Primary Key column of the count already listed).  You can't do a `WHERE` on an aggregate calculation made in the `SELECT` statement.  So, use `HAVING` a `COUNT` = 5, instead.


##### Creating SQL databases

[www.sqlfiddle.com](SQL Fiddle) to create databases.  You can also copy and paste entire files, like .csv files, into a database builder and it will create a SQL database from the stuff you pasted.  Neat!  Great for me and NFL spreadsheets...



#### PSQL and Postgres
Use postgres on your laptop.
**Follow instructions in Week1 / SQL / individual.md** and/or **pair.md**

In terminal, type `psql` to start up psql.  You MUST have Postgres running for this to all work (cmd+space search for postgres and start it).  Once in psql on terminal, create database as instructed above, then you'll have the database on your actual machine.


To create a temporary table, do the following:
```sql
SELECT userid, date_7d
INTO TEMPORARY TABLE logins_mob 
FROM logins_7d JOIN logins ON logins_7d.userid = logins.userid
WHERE logins.type = "mobile"
```

or what have you. Enter this *into psql terminal*, where it will live as long as the session is open, allowing you to reference it as you need, including creating permanent tables from info taken from the temp tables.

#### psycopg2 -- editing SQL within Python via 'Pipelines'
You will edit stuff all you want in psql terminal and then once ready you will enter it into terminal (or ipython).  You must create a connection using psycopg2, then a cursor for that connection.  You must enclose the SQL query as a cursor.execute() argument, then once done you have to actually commit() the query before the actual database will be modified.  At the end you must close the connection.   If you have ANY ERROR AT ALL in any c.execute() command/query, you MUST use `conn.rollback()` to clear the cursor, as it has been 'tainted' by the error.

See "postgres_script.py" and /sql-python/individual.md for more info.

```python
import psycopg2
from datetime import datetime

conn = psycopg2.connect(dbname='socialmedia', user='postgres', host='/tmp')
c = conn.cursor()

today = '2014-08-14'

timestamp = datetime.strptime(today, '%Y-%m-%d').strftime("%Y%m%d")

c.execute(
    '''CREATE TABLE logins_7d AS
    SELECT userid, COUNT(*) AS cnt, type, timestamp %(timestamp)s AS date_7d
    FROM logins
    WHERE logins.tmstmp > timestamp %(timestamp)s - interval '7 days'
    GROUP BY userid;''', {'timestamp': timestamp}
)

conn.commit()
conn.close()
```
