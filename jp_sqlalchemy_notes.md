# JP SQLAlchemy Notes
Good documentation on their website.


### Basic stuff, sort and flesh out later
```python
from sqlalchemy import select, and_, or_, Table, MetaData, create_engine
from sqlalchemy.sql import column, text
from sqlalchemy.sql.expression import case

# Use a local SQLite DB (created from football data using Postgres)
# Can use MySQL or PostgreSQL as well, if have those file types
# 4 slashes before path on Unix, 3 otherwise
engine = create_engine('sqlite://///Users/jpw/Desktop/vtown.db')

# Make connection object
conn = engine.connect()

# Create a Table object, giving the matching name for the actual table in the DB and a MetaData object
vtable = Table('vtown', MetaData())

# Create a (very basic) SELECT statement
s = select([column('Team'), column('Owner')])

# Use that SELECT statement in a query
query = s.select_from(vtable)
```

We now have a query.  We can do various things with it.  Two common actions are to either use it as it is, which is an n-tuple container, in our script or to load it into a Pandas dataframe.  Both of these require that we actually _connect_ to the database and execute this query

##### As a Python Data Structure
```python
# connect to DB and execute our query
result = conn.execute(query)

# We can iterate through it as any container/iterable:
for row in result:
    print(row)  # Gives an n-tuple for n columns

# Print the query to see the full query
In [1]: print(query)
SELECT "Team", "Owner"
FROM vtown
```

This also let's us use all the methods that come with this SQLAlchemy class object.  We can access them for the query or the result.  For example, we can take our query and grab only the distinct results by using the `.distinct()` method on our query (which just adds a SQL `DISTINCT` keyword to our query internally):

```python
result = conn.execute(query.distinct())
for row in result:
    print(row)
```


##### As a Pandas DataFrame
```python
# Use the result and connection object to create a DF!
pd.read_sql(query, conn)
```
