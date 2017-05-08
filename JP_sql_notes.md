# JP SQL, SQLAlchemy, PyMongo, and PostgreSQL Notes

<BR>

## SQL General Notes
***
**SQL** stands for **Structured Query Language** and has been around since the 1970s.  It's a language used to interact with databases and is industry standard, with various "flavors" being used in a given industry or company.  Python supports many of these flavors, with the two most common likely being **SQLite3** and **MySQL**.  Certain frameworks or modules for utilizing SQL in Python, such as **SQLAlchemy** and **PostgreSQL**, are popular and more powerful/convenient than vanilla SQL. There are other database access languages and management systems, such as **NoSQL** or **MongoDB** (**PyMongo**).  For these notes we will be focusing on SQL.

<BR>

#### Entity Relationship Diagram (ERD)
+ **Primary Key (PK)**
  + The main column header, identifies all the data in the column beneath it, in a specific table.   
  + When a PK is referenced in a separate table it is known as a Foreign Key (FK) to the separate table.
    + Note: when a PK is used in another table that has its own PK with the exact same name as the FK, it must be specified explicitly in reference by using the `table-name.FK` method.  
  + The most common relationship between multiple tables (databases?) is a "one-to-many" relationship.  The notion of "many-to-many" is highly complicated and is, for most practical purposes, going to be avoided.
<BR><BR>

#### Aggregate functions  
When using an aggregate function, such as `COUNT()` or `AVERAGE()`, you have to include the remaining columns in the `SELECT` statement that aren't part of the aggregate functions in a `GROUP BY` statement.  Period.  

Note you can do an `ORDER BY`, a `GROUP BY`, or any other aggregate wrap-up function on a column you aren't `SELECT`ing for.  I'm not sure why you'd want to do this, but it is valid.

`HAVING` is used basically as a `WHERE` statement for aggregate functions.  For example, you can't do a query with a `WHERE` clause asking for cells that have a `COUNT` of 5, say.  It will crash (unless there is a Primary Key column of the count already listed).  You can't do a `WHERE` on an aggregate calculation made in the `SELECT` statement.  So, use `HAVING` a `COUNT` = 5, instead.

<BR>

#### Creating SQL databases
You can use the free [SQLite DB Browser](http://sqlitebrowser.org/) to create a database from SQL (or export to SQL).

[SQL Fiddle](www.sqlfiddle.com) to create databases.  You can also copy and paste entire files, like .csv files, into a database builder and it will create a SQL database from the stuff you pasted.  Neat!  Great for me and NFL spreadsheets...

<BR><BR>




## SQLite
***
This section is taken from the [Python-Course page](http://www.python-course.eu/sql_python.php) on SQLite.  

SQLite is a simple relational database system, which saves its data in regular data files or even in the internal memory of the computer, i.e. the RAM. It was developed for embedded applications, like Mozilla-Firefox (Bookmarks), Symbian OS or Android. SQLITE is "quite" fast, even though it uses a simple file. It can be used for large databases as well. If you want to use SQLite, you have to import the module sqlite3. To use a database, you have to create first a Connection object. The connection object will represent the database. The argument of connection - in the following example "`company.db`" - functions both as the name of the file, where the data will be stored, and as the name of the database. If a file with this name exists, it will be opened. It has to be a SQLite database file of course! In the following example, we will open a database called company. The file does not have to exist.:

```python
>>> import sqlite3
>>> connection = sqlite3.connect("company.db")
```  

We have now created a database with the name "company". It's like having sent the command "`CREATE DATABASE company;`" to a SQL server. If you call "`sqlite3.connect('company.db')`" again, it will open the previously created database.  

After having created an empty database, you will most probably add one or more tables to this database. The SQL syntax for creating a table "employee" in the database "company" looks like this:  

```sql
CREATE TABLE employee (
staff_number INT NOT NULL AUTO_INCREMENT,
fname VARCHAR(20),
lname VARCHAR(30),
gender CHAR(1),
joining DATE,
birth_date DATE,  
PRIMARY KEY (staff_number) );
```

This is the way, somebody might do it on a SQL command shell. Of course, we want to do this directly from Python. To be capable to send a command to "SQL", or SQLite, we need a cursor object. Usually, a cursor in SQL and databases is a control structure to traverse over the records in a database. So it's used for the fetching of the results. In SQLite (and other Python DB interfaces)it is more generally used. It's used for performing all SQL commands.

We get the cursor object by calling the cursor() method of connection. An arbitrary number of cursors can be created. The cursor is used to traverse the records from the result set. We can define a SQL command with a triple quoted string in Python:

```python
sql_command = """
CREATE TABLE employee (
staff_number INTEGER PRIMARY KEY,
fname VARCHAR(20),
lname VARCHAR(30),
gender CHAR(1),
joining DATE,
birth_date DATE);"""
```

Concerning the SQL syntax: You may have noticed that the `AUTOINCREMENT` field is missing in the SQL code within our Python program. We have defined the staff_number field as "`INTEGER PRIMARY KEY`" A column which is labelled like this will be automatically auto-incremented in SQLite3. To put it in other words: If a column of a table is declared to be an `INTEGER PRIMARY KEY`, then whenever a `NULL` will be used as an input for this column, the `NULL` will be automatically converted into an integer which will one larger than the highest value so far used in that column. If the table is empty, the value 1 will be used. If the largest existing value in this column has the 9223372036854775807, which is the largest possible INT in SQLite, an unused key value is chosen at random.

Now we have a database with a table but no data included. To populate the table we will have to send the "`INSERT`" command to SQLite. We will use again the execute method. The following example is a complete working example. To run the program you will either have to remove the file *company.db* or uncomment the "`DROP TABLE`" line in the SQL command:

```python
import sqlite3
connection = sqlite3.connect("company.db")

cursor = connection.cursor()

delete
cursor.execute("""DROP TABLE employee;""")

sql_command = """
CREATE TABLE employee (
staff_number INTEGER PRIMARY KEY,
fname VARCHAR(20),
lname VARCHAR(30),
gender CHAR(1),
joining DATE,
birth_date DATE);"""

cursor.execute(sql_command)

sql_command = """INSERT INTO employee (staff_number, fname, lname, gender, birth_date)
    VALUES (NULL, "William", "Shakespeare", "m", "1961-10-25");"""
cursor.execute(sql_command)


sql_command = """INSERT INTO employee (staff_number, fname, lname, gender, birth_date)
    VALUES (NULL, "Frank", "Schiller", "m", "1955-08-17");"""
cursor.execute(sql_command)

# never forget this, if you want the changes to be saved:
connection.commit()

connection.close()
```

Of course, in most cases, you will not literally insert data into a SQL table. You will rather have a lot of data inside of some Python data type e.g. a dictionary or a list, which has to be used as the input of the insert statement.

The following working example, assumes that you have already an existing database company.db and a table employee. We have a list with data of persons which will be used in the `INSERT` statement:

```python
import sqlite3
connection = sqlite3.connect("company.db")

cursor = connection.cursor()

staff_data = [ ("William", "Shakespeare", "m", "1961-10-25"),
               ("Frank", "Schiller", "m", "1955-08-17"),
               ("Jane", "Wall", "f", "1989-03-14") ]

for p in staff_data:
    format_str = """INSERT INTO employee (staff_number, fname, lname, gender, birth_date)
    VALUES (NULL, "{first}", "{last}", "{gender}", "{birthdate}");"""

    sql_command = format_str.format(first=p[0], last=p[1], gender=p[2], birthdate = p[3])
    cursor.execute(sql_command)
```

The time has come now to finally query our employee table:

```python
import sqlite3
connection = sqlite3.connect("company.db")

cursor = connection.cursor()

cursor.execute("SELECT * FROM employee")
print("fetchall:")
result = cursor.fetchall()
for r in result:
    print(r)
cursor.execute("SELECT * FROM employee")
print("\nfetch one:")
res = cursor.fetchone()
print(res)
```

If we run this program, saved as "sql_company_query.py", we get the following result, depending on the actual data:

```python
$ python3 sql_company_query.py
fetchall:
(1, 'William', 'Shakespeare', 'm', None, '1961-10-25')
(2, 'Frank', 'Schiller', 'm', None, '1955-08-17')
(3, 'Bill', 'Windows', 'm', None, '1963-11-29')
(4, 'Esther', 'Wall', 'm', None, '1991-05-11')
(5, 'Jane', 'Thunder', 'f', None, '1989-03-14')

fetch one:
(1, 'William', 'Shakespeare', 'm', None, '1961-10-25')
```



<BR><BR><BR>

## Postgres and PSQL
***
Note that **Homebrew** and **Postgres** are *not* part of the Anaconda distribution, so you will have to install these independently using **brew** even if you are using an Anaconda distribution of Python.

### Installing Postgres
*JP Note*: According to an interview, I think Postgres is available via **Conda** (it may not be in the main distribution)
1. Install **homebrew** and **brew cask**.
    + Install homebrew. Instructions [here](http://brew.sh/)
    + Install brew cask with: `brew install caskroom/cask/brew-cask`
    + If you already have them, update: `brew update && brew upgrade brew-cask`

2. Install your **Postgres** database.
    + The easiest way is to install the pre-built application (with an elephant icon) using the following command: `brew cask install postgres`

After the installation is complete, use *Spotlight* to search for `postgres` and open the application. It will ask you if you want to move it to the Applications folder, say "Move it"
<BR><BR>

### Setting up PSQL on a Mac
1. In terminal go to your home directory (`cd ~/`)
2. Open terminal configurations:
    + `atom ~/.bash_profile` (This will vary somewhat based on OS. If `.bashrc` or `.profile` exist, edit those rather than using `.bash_profile`, which creates a new file).
    + If you are using **zsh**: `atom .zshrc` (if you are on your personal computer and don't know what this is, you are probably using bash in \#1).
3. Insert the following line at the end of the file and save the file: <br>
    `export PATH=/Applications/Postgres.app/Contents/Versions/latest/bin:$PATH`

4. Open a new terminal and run `psql`
<BR><BR>

### Using Postgres (see [documentation](http://www.postgresql.org/docs/9.3/interactive/)):
For a full walkthrough, follow instructions in `/DSI_Camp/Week1/SQL/individual.md` and/or ...`/pair.md`.  Some of the below steps are culled from these files.  

**Important Note**:  
`psql` allows you to interface with an existing .sql file as well as create a new database.  Commonly we will use `psql` to investigate, modify, and return certain information from an existing .sql database.  In these cases we have to create an empty `psql` database and then populate it with the existing .sql database.  The instructions below are written for this scenario.  

1. In terminal, type `psql` to begin a PSQL session, then can do a command-space search for `postgres`, open it, and go from there.  Opening Postgres without having PSQL running will fail to open Postgres.  (Also, you can click the elephant menubar icon for psql and open it from there)

2. From the command line run `psql` and then this command to create the database.

    ```sql
    CREATE DATABASE database_name;
    \q -- quits psql upon creation of db
    ```

3. Navigate to where your .sql database exists on your hard drive and run the following commands to import the database:

    ```bash
    cd data
    psql database_name < database_name.sql
    ```
    You should see a bunch of SQL commands flow through the terminal.

4. To use the interactive Postgres prompt enter the following to be connected to your database.

    ```bash
    psql database_name
    ```

5. After you are done with PSQL, use `\q` to exit.

Now we are in a command line client. This is how we will explore the database to gain an understanding of what data we are playing with.
<BR><BR>

#### Basic Exploration
First, we will want to see what the available tables are. Remember, now that we are in the database, all of our commands or actions will be on the database you created `database_name`.  Please see below for a full list of PSQL commands.

1. What are the tables in our database? Run `\d` to find out.
2. What columns does each table have? Run `\d <tablename>` to find out.
3. `q` will end any query, but not the program (`\q` ends the program)
4. **ALL** queries must end in a `;`
<BR><BR>

#### Create a Temporary Table
To create a temporary table, do the following:
```sql
SELECT userid, date_7d
INTO TEMPORARY TABLE logins_mob
FROM logins_7d JOIN logins ON logins_7d.userid = logins.userid
WHERE logins.type = "mobile"
```

...or what have you. Enter this *into psql terminal*, where it will live as long as the session is open, allowing you to reference it as you need, including creating permanent tables from info taken from the temp tables.

### psycopg2
##### Editing SQL within Python via 'Pipelines'
You will edit stuff all you want in psql terminal and then once ready you will enter it into terminal (or ipython).  You must create a connection using psycopg2, then a cursor for that connection.  You must enclose the SQL query as a `cursor.execute()` argument, then once done you have to actually `commit()` the query before the actual database will be modified.  At the end you must close the connection.   If you have *ANY ERROR AT ALL* in any `c.execute()` command/query, you MUST use `conn.rollback()` to clear the cursor, as it has been 'tainted' by the error.

See "**postgres_script.py**" and **/sql-python/individual.md** for more info.

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
<BR><BR><BR>

### PSQL Commands
***
##### GENERAL OPTIONS:
Command | Action
--------|-------
`-c COMMAND` | run only single command (SQL or internal) and exit
`-d, --dbname=NAME` | specify database name to connect to (default: "logged in username here")
`-f, --file=FILENAME` | execute commands from file, then exit
`--help` | show this help, then exit
`-l, --list` | list available databases, then exit
`-v NAME=VALUE` | set psql variable NAME to VALUE
`--version` | output version information, then exit
`-X` | do not read startup file (~/.psqlrc)
 <BR>

##### TYPES:
Command | Action
--------|-------
`\h` | for help with SQL commands
`\?` | for help with psql commands
`\g` | or terminate with semicolon (`;`) to execute query
`\q` | to quit
<BR>

##### GENERAL:
Command | Action
--------|-------
`\c[onnect] [DBNAME\|- USER\|- HOST\|- PORT\|-] |` | connect to new database
`\cd [DIR]` | change the current working directory
`\encoding [ENCODING]` | show or set client encoding
`\h [NAME]` | help on syntax of SQL commands, `*` for all commands
`\set [NAME [VALUE]]` | set internal variable, or list all if no parameters
`\timing` | toggle timing of commands (currently off)
`\unset NAME` | unset (delete) internal variable
`\prompt [TEXT] NAME` | prompt user to set internal variable
`\! [COMMAND]` | execute command in shell or start interactive shell
<BR>

##### QUERY BUFFER:
Command | Action
--------|-------
`\e [FILE]` | edit the query buffer (or file) with external editor
`\g [FILE]` | send query buffer to server (and results to file or `|pipe`)
`\p` | show the contents of the query buffer
`\r` | reset (clear) the query buffer
`\w FILE` | write query buffer to file
<BR>

##### INPUT/OUTPUT:
Command | Action
--------|-------
`\echo [STRING]` | write string to standard output
`\i FILE` | execute commands from file
`\o [FILE]` | send all query results to file or `|pipe`
`\qecho [STRING]` | write string to query output stream (see `\o`)
<BR>

##### INFORMATIONAL:
Command | Action
--------|-------
`\d [NAME]` | describe table, index, sequence, or view
`\d{t|i|s|v|S} [PATTERN]` (add `+` for more detail) | list tables/indexes/sequences/views/system tables
`\da [PATTERN]` | list aggregate functions
`\db [PATTERN]` | list tablespaces (add `+`)
`\dc [PATTERN]` | list conversions
`\dC` | list casts
`\dd [PATTERN]` | show comment for object
`\dD [PATTERN]` | list domains
`\df [PATTERN]` | list functions (add `+`)
`\dF [PATTERN]` | list text search configurations (add `+`)
`\dFd [PATTERN]` | list text search dictionaries (add `+`)
`\dFt [PATTERN]` | list text search templates
`\dFp [PATTERN]` | list text search parsers (add `+`)
`\dg [PATTERN]` | list groups
`\dn [PATTERN]` | list schemas (add `+`)
`\do [NAME]` | list operators
`\dl` | list large objects (same as `\lo_list`)
`\dp [PATTERN]` | list table, view, and sequence access privileges
`\dT [PATTERN]` | list data types (add `+`)
`\du [PATTERN]` | list users
`\l` | list all databases (add `+`)
`\z [PATTERN]` | list table, view, and sequence access privileges (same as `\dp`)
<BR>

##### FORMATTING:
Command | Action
--------|-------
`\a` | toggle between unaligned and aligned output mode
`\C [STRING]` | set table title, or unset if none
`\f [STRING]` | show or set field separator for unaligned query output
`\H` | toggle HTML output mode (currently off)
`\pset NAME [VALUE]` | set table output option: (NAME := {format \| border \| expanded \| fieldsep \| footer \| null \| numericlocale \| recordsep \| tuples_only \| title \| tableattr \| pager})
`\t` | show only rows (currently off)
`\T [STRING]` | set HTML <table> tag attributes, or unset if none
`\x` | toggle expanded output (currently off)
<BR>

##### COPY, LARGE OBJECT:
Command | Action
--------|-------
`\copy ...` | perform SQL COPY with data stream to the client host
`\lo_export LOBOID FILE` | LOBOID FILE
`\lo_import FILE [COMMENT]` | FILE [COMMENT]
`\lo_list` | list
`\lo_unlink LOBOID` | large object operations
<BR>

##### Connection Options:
Command | Action
--------|-------
`-h, --host=HOSTNAME` | database server host or socket directory
`-p, --port=PORT `| database server port number
`-U, --username=NAME` | connect as specified database user
`-W, --password` | force password prompt (should happen automatically)
`-e, --exit-on-error` | exit on error, default is to continue
`-d DBNAME` | some database


<BR><BR><BR>

## Notes From SQL ZOO Trials
***
For strings, SQL requires single quotes.
Statements end in semi-colons.

```sql
SELECT [column] FROM [table]
WHERE [row entry/name] = ‘string’ or val
WHERE [col] LIKE ‘xxx%’    …xxx are characters, % is wildcard.  So ‘G%’ would choose all names starting with G
WHERE [col] IN (‘item A’, ‘item B’, ‘item C’)   …gives all matches listed in IN
WHERE [col] OR [col]
WHERE [col] AND [col]
WHERE [col] BETWEEN [val] AND [val]
```

Can do calculations in syntax (e.g. `SELECT gdp/pop FROM [table]` gives the gdp per person)
Can return length of entry with `SELECT LENGTH(row)`

##### CASE:  
The `CASE` statement shown is used to substitute North America for Caribbean
`CASE` is used before the column of choice in this case, giving only the modified column and not the original

```sql
SELECT name, 		--[note the trailing comment before use of CASE]
CASE WHEN continent='Caribbean' THEN 'North America'
ELSE continent END
FROM world
```

##### OR:  
Note you have to write name of `column` (continent, here) and its definition for each `OR` when using `OR` statements.

```sql
WHEN continent = 'Europe' OR continent = 'Asia' THEN 'Eurasia'
```

Example with `CASE` and `OR`:

```sql
SELECT name,
CASE
WHEN continent = 'Europe' OR continent = 'Asia' THEN 'Eurasia'
WHEN continent = 'North America' OR continent = 'South America' OR continent = 'Caribbean' THEN 'America'
ELSE continent END
FROM world

WHERE name LIKE 'A%' OR name LIKE ('B%')
```

##### Exclusive OR (XOR):
Show the countries that are big by area or big by population but not both. Show name, population and area.
```sql
SELECT name, population, area FROM world
WHERE population > 250000000 XOR area > 3000000
```


Note that logic operators like `AND` and `OR` can be grouped via parenthesis to form a single argument, just like in normal code or math.

```sql
WHERE name = ‘joe’ AND age < 25 OR age > 40     
--is different than..
WHERE name = ‘joe’ AND (age < 25 OR age > 40)
```


##### DISTINCT:  
Used to return distinct results
```sql
SELECT DISTINCT City FROM Customers;
```

##### Select All Columns
There are two primary ways to select all columns from a table.
1. List the column name of each column.
2. Use the symbol \*.  
3.
For example, to select all columns from `Store_Information`, we issue the following SQL:

```sql
SELECT * FROM Store_Information;
```


##### AGGREGATE FUNCTIONS
```sql
AVG(), COUNT(), MAX(), MIN(), SUM() [FIRST(), LAST()]
```

If using the above funcs, must use `GROUP BY` at end of overall query with the columns queried for in the original `SELECT` query
i.e. Every field we `SELECT` needs to be either in the `GROUP BY` clause (which `name_length` is) or an aggregate computation

Example:

```sql
SELECT matchid, mdate, COUNT(teamid)
FROM game JOIN goal ON (game.id = goal.matchid)
WHERE teamid = 'GER'
GROUP BY matchid, mdate
```


##### JOIN
You can combine two steps into a single query with a `JOIN`.
```sql
SELECT *
FROM game JOIN goal ON (game.id = goal.matchid)
```

The `FROM` clause says to merge data from the `goal` table with that from the `game` table. The ON says how to figure out which rows in `game` go with which rows in `goal` - the `id` from `goal` must match `matchid` from `game`.






<BR><BR><BR>

#### SQL ZOO EXAMPLES:
***
Show the countries which have a name that includes the word 'United'
```sql
SELECT name FROM world
WHERE name LIKE 'United%'
```

Show the countries that are big by area or big by population. Show name, population and area.
```sql
SELECT name, population, area FROM world
WHERE area > 3000000 OR population > 250000000
```

Exclusive OR (XOR).  
Show the countries that are big by area or big by population but not both. Show name, population and area.
```sql
SELECT name, population, area FROM world
WHERE population > 250000000 XOR area > 3000000
```

Show the name and population in millions and the GDP in billions for the countries of the continent 'South America'. Use the `ROUND` function to show the values to two decimal places.
```sql
SELECT name, ROUND(population/1000000, 2), ROUND(gdp/1000000000, 2) FROM world
WHERE continent = 'South America'
```

Show the name and per-capita GDP for those countries with a GDP of at least one trillion (1000000000000; that is 12 zeros). `ROUND` this value to the nearest $1000. (note negative decimals for `ROUND` func)
```sql
SELECT name, ROUND(gdp/population, -3) FROM world
WHERE gdp >= 1000000000000
```


Show the name - but substitute Australasia for Oceania - for countries beginning with N. (Note the trailing comma after name!)
```sql
SELECT name,
CASE WHEN continent = 'Oceania' THEN 'Australasia'
ELSE continent END
FROM world
WHERE name LIKE 'N%'
```


Show the name - but substitute Eurasia for Europe and Asia; substitute America - for each country in North America or South America or Caribbean. Show countries beginning with A or B
```sql
SELECT name,
CASE WHEN continent = 'Asia' OR continent = 'Europe' THEN 'Eurasia'
WHEN continent = 'North America' OR continent = 'South America' or continent = 'Caribbean' THEN 'America'
ELSE continent END
FROM world
WHERE name LIKE 'A%' OR name LIKE 'B%'
```


List the winners, year and subject where the winner starts with Sir. Show the winners by name order.
```sql
SELECT * FROM nobel
WHERE winner LIKE 'Sir%'
ORDER BY winner
```

List the winners, year and subject where the winner starts with Sir. Show the the most recent first, then by name order.
```sql
SELECT winner, yr, subject FROM nobel
WHERE winner LIKE 'Sir%'
ORDER BY yr DESC, winner
```

The expression subject IN ('Chemistry','Physics') can be used as a value - it will be 0 or 1.
Show the 1984 winners and subject ordered by subject and winner name; but list Chemistry and Physics last.
```sql
SELECT winner, subject
FROM nobel
WHERE yr=1984
ORDER BY subject IN ('Chemistry', 'Physics'), subject, winner
```

Select the code which shows the years when a Medicine award was given but no Peace or Literature award was
```sql
SELECT DISTINCT yr
FROM nobel
WHERE subject='Medicine'
AND yr NOT IN(SELECT yr FROM nobel WHERE subject='Literature')
AND yr NOT IN (SELECT yr FROM nobel WHERE subject='Peace')
```


##### nested select, distinct
Pick the code that shows the amount of years where no Medicine awards were given
```sql
SELECT COUNT(DISTINCT yr) FROM nobel
WHERE yr NOT IN (SELECT DISTINCT yr FROM nobel WHERE subject = 'Medicine')
```


##### nested select
Select the code which would show the year when neither a Physics or Chemistry award was given
```sql
SELECT yr FROM nobel
WHERE yr NOT IN(SELECT yr FROM nobel WHERE subject IN ('Chemistry','Physics'))
```

Show the population of China as a multiple of the population of the United Kingdom
```sql
SELECT
population/(SELECT population FROM world WHERE name='United Kingdom')
FROM world
WHERE name = 'China'
```

Show each country that has a population greater than the population of ALL individual countries in Europe.
```sql
SELECT name FROM world
WHERE population > ALL
(SELECT population FROM world WHERE continent='Europe')
```

List each country name where the population is larger than that of 'Russia'.
```sql
SELECT name FROM world
WHERE population > (SELECT population FROM world WHERE name = 'Russia')
```

List the name and continent of countries in the continents containing eitherArgentina or Australia. Order by name of the country.
```sql
SELECT name, continent FROM world
WHERE continent IN (SELECT continent FROM world WHERE name = 'Argentina' OR name = 'Australia')
ORDER BY name
```


Germany (population 80 million) has the largest population of the countries in Europe. Austria (population 8.5 million) has 11% of the population of Germany.
Show the name and the population of each country in Europe. Show the population as a percentage of the population of Germany.
```sql
SELECT name, CONCAT(ROUND(population / (SELECT population FROM world WHERE name = 'Germany') * 100, 0), '%')
FROM world
WHERE continent = 'Europe'
```

For each continent show the continent and number of countries.
```sql
SELECT continent, COUNT(name) FROM world
GROUP BY continent
```

List the continents that have a total population of at least 100 million.  (HAVING always comes after GROUP BY)
```sql
SELECT continent FROM world
GROUP BY continent
HAVING SUM(population) > 100000000
```

Show the player, teamid, stadium and mdate and for every German goal.
```sql
SELECT player, teamid, stadium, mdate
FROM game JOIN goal ON (game.id = goal.matchid)
WHERE teamid = "GER"
```


Show the name of all players who scored a goal against Germany.
```sql
SELECT DISTINCT player
FROM goal JOIN game ON (game.id = goal.matchid)
WHERE (team1 = 'GER' OR team2 = 'GER') AND teamid NOT IN ('GER')
```


Show teamname and the total number of goals scored.
```sql
SELECT teamname AS 'Country', COUNT(player) AS 'Goals Scored'
FROM goal JOIN eteam ON (goal.teamid = eteam.id)
GROUP BY teamname
```

For every match involving 'POL', show the matchid, date and the number of goals scored.
```sql
SELECT matchid, mdate, COUNT(matchid)
FROM game JOIN goal ON (game.id = goal.matchid)
WHERE team1 = 'POL' OR team2 = 'POL'
GROUP BY matchid, mdate
```


List every match with the goals scored by each team as shown. This will use `CASE WHEN` which has not been explained in any previous exercises.
```html
        mdate       team1   score1  team2   score2
1       July 2012	ESP     4	    ITA	    0
10      June 2012	ESP	    1	    ITA	    1
10      June 2012	IRL	    1	    CRO	    3
...
```
Notice in the query given every goal is listed. If it was a `team1` goal then a 1 appears in `score1`, otherwise there is a 0. You could `SUM` this column to get a count of the goals scored by `team1`. Sort your result by `mdate`, `matchid`, `team1` and `team2`.

```sql
SELECT mdate, team1,
SUM(CASE WHEN goal.teamid = game.team1 THEN 1 ELSE 0 END) Score1, team2,
SUM(CASE WHEN goal.teamid = game.team2 THEN 1 ELSE 0 END) Score2
FROM game LEFT JOIN goal ON (game.id = goal.matchid)
GROUP BY mdate, matchid, team1, team2
```


Which were the busiest years for 'John Travolta', show the year and the number of movies he made each year for any year in which he made more than 2 movies.
```sql
SELECT yr, COUNT(title)
FROM movie
JOIN casting ON (movie.id = movieid)         
JOIN actor ON (actorid = actor.id)
WHERE name = 'John Travolta'
GROUP BY yr

HAVING COUNT(title) = (SELECT MAX(c)
FROM
(SELECT yr, COUNT(title) AS c
FROM movie
JOIN casting ON movie.id=movieid
JOIN actor ON actorid=actor.id
WHERE name='John Travolta'
GROUP BY yr) AS t
)
```



List the film title and the leading actor for all of the films 'Julie Andrews' played in.
```sql
SELECT DISTINCT title, name
FROM movie JOIN casting ON (casting.movieid = movie.id) JOIN actor ON (casting.actorid = actor.id)
WHERE movieid IN (SELECT movieid FROM casting
WHERE actorid IN (SELECT id FROM actor WHERE name='Julie Andrews')) AND ord=1
```


Obtain a list, in alphabetical order, of actors who've had at least 30 starring roles.
```sql
SELECT name
FROM movie JOIN casting ON (casting.movieid = movie.id) JOIN actor ON (casting.actorid = actor.id)
WHERE ord=1
GROUP BY name
HAVING COUNT(name) >= 30
```


List the films released in the year 1978 ordered by the number of actors in the cast.
```sql
SELECT title, COUNT(actorid)
FROM movie JOIN casting ON (casting.movieid = movie.id) JOIN actor ON (casting.actorid = actor.id)
WHERE yr = 1978
GROUP BY title
ORDER BY COUNT(actorid) DESC
```

List all the people who have worked with 'Art Garfunkel'.
```sql
SELECT actor.name
FROM movie JOIN casting ON (casting.movieid = movie.id) JOIN actor ON (casting.actorid = actor.id)
WHERE actor.name !='Art Garfunkel'
AND movie.id IN (SELECT movie.id
FROM movie JOIN casting ON (casting.movieid = movie.id) JOIN actor ON (casting.actorid = actor.id)
WHERE actor.name='Art Garfunkel')
```
