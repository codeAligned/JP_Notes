# JP Spark Notes

Notes about Spark generally and PySpark specifically.
Taken from the udemy.com class [spark and python for big data with pyspark](https://www.udemy.com/spark-and-python-for-big-data-with-pyspark/learn/v4).  ALL course materials and notes are available in the `Python-and-Spark-for-Big-Data-master` folder.  The lectures are presented in nice Jupyter Notebooks.

See the [official documentation](http://spark.apache.org/docs/2.1.0/api/python/pyspark.sql.html).

## Getting Started
The Udemy class details four separate ways to install Spark.
1. Use Ubuntu and a VirtualBox (no thanks; doing this to make all systems work for vid)
2. Install locally, run on AWS EC clusters (slow, free for a year)
3. Use databricks.com for online hosting of Spark instances, free 6 GB tier (yes)
4. Install locally, run on AWS RedShift (fast and nice, not free)

The most logical way for work outside the class is to install locally and then run it on a cluster somewhere.  Running it all locally is doable for the class, but for truly "big" data that isn't an option (hence why Spark was developed in the first place, to handle sets of data that are too large to fit on a single machine).  Thus, installing locally and hosting on a cluster will be the ultimate use case at some point.  I just didn't do it for this class because of the AWS headache.  

Spark was written in Scala (and, if possible, it is best to use Scala with Spark.  I don't have any Scala experience so I'm using Python's PySpark for now), which itself comes from Java.  In order to install and run Spark locally, you will need the latest Java Virtual Machine (JVM) [development kit installed](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html).  Since it comes from Oracle, there is a fair amount of surreptitious bloatware and insidious packages installed.  Google for the best ways to remove them from your machine, especially if you have an otherwise spyware-free Mac.

### Installing Spark
PySpark needs two things installed: Java and Spark.  
If you use [databricks.com](databricks.com) you don't have to install any of this since everything is handled on their servers.  I've used databricks.com largely as a testing site to accompany the online class; it appears to be fully fledged in cluster deployment if you wish to use it that way.

##### Java
Datacamp has a good overview of PySpark installation on [this page](https://www.datacamp.com/community/tutorials/apache-spark-python).  Note the [JDK](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) link in it is slightly dated (JDK SE8).  To get the most recent version of JDK, type `java` into the Terminal and choose "more info" on the ensuing dialog box.  As of March 2018, the newest version is [version 10](http://www.oracle.com/technetwork/java/javase/downloads/jdk10-downloads-4416644.html).  If you already have Java installed, this dialog box might not appear.  I'm unsure how you update Java then -- likely delete it and install a fresh new version.

Please note that Apple discontinued pre-installing Java on all their machines due to high frequency of security issues with Java.  Java will require you to frequently update it, but even in so doing you are exposing yourself to more malware risks through browser exposure.  Because of this, unless actively using it, [disabling Java is recommended](http://osxdaily.com/2012/09/08/how-to-disable-java/) and easy.  

Oracle, the makers of Java are [notorious for bundling adware](https://www.computerworld.com/article/2893607/software-integration/java-mac-ask-toolbar-oracle-itbwcw.html) into Java, causing users to have to [manually uninstall](https://www.imore.com/oracle-bundles-adware-mac-java-installer) the adware bloat.

If you wish to [uninstall Java entirely](http://osxdaily.com/2017/06/16/uninstall-java-mac/), you can.

The lasting impression to take is this:  
Unless you need to install Java on your Mac (in this case, for Spark), then don't do it.  If you do install it, complete your project in Spark, and then no longer acutely need it, you should disable it at the minimum.

##### Spark
Download the [latest version of Spark](https://spark.apache.org/downloads.html).  Unzip / tar the .zip/.tar file.  Move the resulting folder, which will be named something like `spark-2.3.0-bin-hadoop2.7`, to the following directory:  

`/usr/local/spark`

This directory may not exist yet, so create it first or use the command line from the `Downloads` directory, or wherever you untarred the Spark file (again, use whatever your Spark download version is):  

`mv spark-2.3.0-bin-hadoop2.7 /usr/local/spark`

##### PySpark
As of 2018, PySpark is now available in PyPI and Anaconda (woohoo).  Install it with:
`conda install pyspark` or `pip install pyspark`, depending upon your setup (favoring `conda` if you have Anaconda installed, of course).

You can run an interactive PySpark shell by entering the following command in the Terminal from the directory of your Spark install (e.g. `/usr/local/spark/spark-2.3.0-bin-hadoop2.7`):  

`./bin/pyspark`

If you do not have Java installed, you will be prompted to install it here.


#### Local Script / Notebook
Note I used [databricks.com](databricks.com), a site created by one of the founders of Spark, as my Spark app.  
If using a script / Notebook, would need to first create a Spark session with the following import:  

```python
from pyspark.sql import SparkSession
spark = SparkSession.builder.appName("<AppNameHere>").getOrCreate()
df = spark.read.csv('fft_clas_stats.csv')           # many read methods available
```

This builder process can take a while depending on machine used.

#### Databricks.com
databricks.com runs instances of Spark in online Notebooks and allows a small 6 GB free cluster to be used.  We do not need to build a Spark session because every workspace in it is a session.  For PySpark, we only have to import it and define a SQL context for files we've uploaded, assuming they can be accessed via a SQL schema.

For example, I uploaded my testing csv _du jour_, `fft_class_stats.csv`, to the "data" storage in my databricks account and then used it in my first PySpark DF test.  When you upload a local data file, you can and should specify the datatype for each column. (This is another reason this FFT csv is a great tester file, because it has `str`, `int`, and `float` dtypes).

The `sqlContext` class was for Spark 1.0.  With the advent of Spark 2.0, it has been replaced by the `SparkSession` class above, though is kept alive for backward compatibility.  We cannot (to my knowledge) use the `.read.csv()` method in Databricks because it doesn't store uploaded data as a CSV (even if you upload an actual CSV) -- it is converted to a SQL DB.

Here is the old way in databricks:
```python
import pyspark
df = sqlContext.sql("SELECT * FROM fft_class_stats")
```

Here is the new way:
```python
from pyspark.sql import SparkSession
spark = SparkSession.builder.appName("fft").getOrCreate()
spark.sql("SELECT * FROM fft_class_stats")
```

Either way, we are using Spark SQL to return data from a SQL database instead of loading it directly from a .csv as above.

Now I have a Spark DF of my FFT csv ready to roll, either via a local script or databricks.com.



<BR>


## Spark Data Frames
As of Spark 2.0, Spark and its various apps are transitioning to the more universal _data frame_ object, built on SQL databases, instead of the initial _RDD_.  Spark Data Frames are very similar to data frames of Pandas and R, happily.  Using PySpark offers welcomed overlap with Pandas commands and attributes.  Since Spark DFs are built on SQL databases, we also have the ability to leverage raw SQL queries to interact with Spark DFs.

There are some key differences between Spark DFs and Pandas/R DFs.  These differences stem from the distributed nature of a Spark DF vs. the local in-memory nature of Pandas and R DFs.  One significant fact to grasp is that a distributed Spark DF is _immutable_, thus we cannot assign new values or properties to an existing Spark DF and "overwrite" it.  Every operation that changes a Spark DF will result in a new Spark DF.  For example, we cannot change Column A in a Spark DF from a `string` to a `float` value via assignment, as we would in Pandas and R.

```python
## This is not allowed: 'DataFrame' object does not support item assignment
df['Column_A'] = df['Column_A'].cast('float')
```

#### From Pandas
We can convert a Pandas DF into a Spark SQL DF with the basic `createDataFrame` command by supplying an existing Pandas DF as the data:  

`df_spark = SparkSession.createDataFrame(df_pandas)` -- v2.0 or newer  
`df_spark = sqlContext.createDataFrame(df_pandas)` -- pre v2.0

To go the other way and turn a Spark DF into a Pandas DF, simply use the `df.toPandas()` command.

#### General Commands
```python
df.read.csv("<path>.csv")  
df.printSchema()
# Output:
root
 |-- Job_ID: string (nullable = true)
 |-- Job: string (nullable = true)
 |-- Identity: string (nullable = true)
 |-- Gender: string (nullable = true)
 |-- Level: integer (nullable = true)
 |-- Role: string (nullable = true)
 |-- Association: string (nullable = true)
 |-- HPm: integer (nullable = true)
 |-- MPm: integer (nullable = true)
 |-- PAm: integer (nullable = true)
 |-- MAm: integer (nullable = true)
 |-- SPm: integer (nullable = true)
 |-- Move: integer (nullable = true)
 |-- Jump: integer (nullable = true)
 |-- CEV: float (nullable = true)
 |-- HPc: integer (nullable = true)
 |-- MPc: integer (nullable = true)
 |-- PAc: integer (nullable = true)
 |-- MAc: integer (nullable = true)
 |-- SPc: integer (nullable = true)    

df.columns  
df.describe()  
df.head()  
```

#### Showing the Data
Anytime we want to see the data, we must use a `.show()` method!  
`.show(numRows, truncate)` -- both arguments optional; can use `.show(truncate=False)` to show all results.  __UPDATE__ I think this is incorrect...

`df.describe().show(100)` -- would show up to 100 rows if that many are present.   
`df.describe().show(truncate=False)` -- would show results.   
`df.columns` -- show column names; note this is an _attribute_ and not a method, a'la Pandas.


#### Collecting the Data
As expected, the `.show` method simply prints out the results of an operation.  When we want to actually return the results so we can save them in a variable or process them, we must use `.collect`.

`df.filter(df['Level'] > 20).collect()` would return (not show) a DF of characters whose level was >20.  

The return type is a `list` of `Row` objects (see more below).  This means you will have to index into the collected list to access specific rows of data!



#### Defining Schema / Datatypes
As usual, most true data is messy and Spark's internal type estimator will make mistakes or be stumped by mixed types in a column.  To manually enforce type as needed, use the following:

```python
from pyspark.sql.types import StructField, StringType, IntegerType, StructType
```

We will use these constructors to create our own Schema for the DB we are using.

##### Creating Fields and Types
Schemas can be defined by `StructType` constructors, which themselves are made up of `StructField` fields (columns).

`StructField(field_name, dtype, nullable)` is how we set a `dtype` for a specific column.  It has three arguments: __field name__ (str), __type__ (dtype), __bool nullable__ (T/F, can it be a `null` value?)

For example, let's say the FFT csv import incorrectly interpreted the column `Level` as a `string` instead of an `int`.  Note I don't know if when defining passing a schema to the `.read` function, such as using `final_struct` below, if we must specify a `StructField` for _every_ column in the table or not.   I can't check until I install Spark on my local.

Once a list of our desired StructFields is assembled, we pass that into a `StructType` constructor and use that as our `schema` argument upon read in.

```python
data_schema = [StructField("Level", IntegerType(), True)]]
final_struct = StructType(fields=data_schema)
df = spark.read.csv('fft_clas_stats.csv', schema=final_struct)
## Will this only modify the "Level" column leave the rest as is, or will it error b/c I left other columns undefined?
```


#### Accessing Data in the Data Frame
##### Dictionary-style Indexing
Like Pandas DFs, a Spark DF uses dictionary-style indexing where we supply the key to the DF column(s) we want.  However, there are some additional steps to grabbing data.  If we just index in a'la a Pandas DF, we return a `Column` object (called a `Series` in Pandas), and this object is not something we can currently see via `.show()`.  For many of the operations needed when working in the Spark DF we will need to provide a `Column` data type -- don't forget that this is achieved using this Pandas style dictionary index.

```python
## This returns a 'Column' object:

df['Level']
## output: Column<b'Level'>

type(df['Level'])
## output: pyspark.sql.column.Column
```

##### `.select()` Method
In order to actually access the data, we must use the `.select()` method, akin to SQL.  And to _see_ the data, we must use the `.show()` method as noted above.  The printed result is akin to Pandas in IPython.  Using the `.select()` method returns a `DataFrame` object, not a Spark `Column`.  Pandas doesn't operate this way, instead returning a `Series` whenever a single row or column is sliced.  This is an important difference, as it means all DF operations are uniformly valid whether we have chosen a single row/column or multiple!  

_Small technical note_:  
We can `.select()` a single column without enclosing it in a list, such as `df.select('Level')`, and Spark should handle this.  However, there are apparently times when this might not be the case, so it is a best practice to always enclose the columns desired in a `list` inside a `.select()` statement to avoid unexpected issues.

```python
## This returns a 'DataFrame' object:

df.select(['Level'])
## output: DataFrame[Level: int]

df.select(['Level']).show()
## output:
+-----+
|Level|
+-----+
|    1|
|   10|
|   26|
|    1|
...

type(df.select('Level'))
## output: pyspark.sql.dataframe.DataFrame
```

Like Pandas, Spark DFs are `DataFrame` objects.  Unlike Pandas, Spark DFs also have separate `Column` and `Row` objects.  Slicing a row returns a `Row` object, same for a column as we saw above.  In Pandas, rows _and_ columns are actually the same type of object: a `Series`.  There are advantages to having a generalized grouping of data like a `Series`, but there are also advantages to having separate objects for both a row and a column, like Spark has.  The primary purpose for Spark's granular data types in the DF are a result of the precision needed in dealing with distributed data and computing.  Spark needs to know _exactly_ what operation is being done to which data and where that data is in the distributed system.  This makes sense.

If we had wanted to select multiple columns, we simply pass in a list of columns, `['Level', 'Role']`, as in Pandas.  _Note that since Spark DFs are distributed, we cannot (easily) access a single row, unlike in Pandas._  This is one of the larger differences between the two and one of the more difficult friction points in adapting to Spark.

#### Adding or Renaming Columns
Use the `.withColumn(name, data)` method to add new cols.  
Arguments are __name__ (str) and __data__ (`Column` data type!).  
Use `.withColumnRenamed(old_name, new_name)` to rename columns.

These are __not__ in-place operations!

```python
df.withColumn('Double_Level', df['Level'] * 2)
## This adds a new column "Double_Level" which is just "Level" * 2

df.withColumnRenamed('Level', 'A_New_Level')
## Of confidence and power...
```

#### Using SQL Directly
Since this is a `spark.sql` DF, we can use SQL to access it directly.  To do so, we must first create a "temporary SQL view" and then use traditional SQL queries.  As someone who has basic knowledge of SQL, this doesn't appeal to me very much, but it is a nice feature and allows colleagues who have advanced SQL knowledge the ability to interact with Spark via SQL queries if desired.

```python
## View name as str, replaces if previously made.
df.createOrReplaceTempView("FFT_Sql")

results = spark.sql("SELECT * FROM FFT_Sql")
results.show()
```

It is also possible to use some DF methods without creating the SQL View table:

```python
## Uses the generalized df.filter() method with SQL query syntax.
df.filter("Level > 20").show()

## Can chain with .select() to return only specified columns.
df.filter("Level > 20").select(['Identity', 'Level']).show()
```


#### Filtering / Sub-selecting Data
This is how we will be operating on the data, generally.  It is very similar to Pandas masking, syntactically. The chained `.select()` method is optional, of course.  The same rules of using `&`, `|`, and `~` for the `and`, `or`, and `not` operators also apply, as does the rule of enclosing each condition of a multiple condition filter/mask in parentheses.

```python
df.filter(df['Level'] > 20).select(['Identity', 'Level']).show()

df.filter((df['Level'] > 20) & (df['Level'] < 40)).select(['Identity', 'Level']).collect()
## Note .collect() -- returns a List of Row objects; must index in for desired Rows!
```

#### `Row` and `Column` Objects
They have [many methods](http://spark.apache.org/docs/2.1.0/api/python/pyspark.sql.html#pyspark.sql.Row) available to them.  
Object | Method       | Description
-------|--------------|------------
Both   | `.asDict()`  | returns as a `dict`, can index in per normal.

The important thing to remember is the different object types returned by `.select()` vs. indexing.

Command             | Returned Object
--------------------|----------------
`.select()`         |  `DataFrame`
`df['<col_name>']`  |  `Column` / `Row`




#### Grouping / Aggregation
PySpark DFs use the `groupBy()` method.
See [documentation](http://spark.apache.org/docs/2.1.0/api/python/pyspark.sql.html#pyspark.sql.GroupedData) here.

You cannot select specific columns from a grouped object to return via the `.select` method because the cols get renamed via traditional SQL usage (e.g. "Level" becomes "avg(Level)").  Instead you must specify the col and aggregation desired in a `.agg(dict)` call.  Dict (or list) comprehension is our friend, here.  Also can traditional aggregation methods, like `.sum` or `.mean`, etc. for the entire grouped frame.  Can also use SQL star `*` to select all cols in the `.agg` call.

```python
group_cols = ['Association', 'Role']
extra_cols = ['Level', 'HPm', 'MPm', 'PAm', 'MAm']

## Show entire DF
df.groupBy(group_cols).mean().show()

## Show only desired cols - Dict Comp
df.groupBy(group_cols).agg({col: 'mean' for col in group_cols + extra_cols}).show()

## Show only desired cols - List Comp
## Note DataBricks appears to run Py3.5 so "f-strings" are not valid (fml).
df.groupBy(group_cols).mean().select(group_cols + ["avg({col})".format(col=col) for col in extra_cols]).show()
```

##### Transpose
Since Spark DFs are distributed by row, there is no way to _transpose_ them as we can in Pandas (or even Excel).  If the Spark DF is small enough to fit into local memory, we can convert it to a Pandas DF and then use Pandas' `.T` method to transpose it. Many Spark datasets are likely too large for this.

```python
df.toPandas().T
```

##### MultiIndex
Spark SQL has no "index", so things like a "multi-index" in Pandas do not apply.  In order to convert a Pandas MultiIndex to a Spark SQL DF, you have to flatten the Pandas DF first (i.e. `reset_index(drop=False, inplace=True)`)


### NaNs
PySpark DFs use the `.na` class attribute to interact with DF / row / col NaN values.  The functionality is nearly identical to Pandas.
```python
df.na.drop()                    # drops via row, any with NaN
df.na.drop(thresh=3)            # drops any row that has fewer than 3 non-null entries
df.na.drop(how='any')           # default arg.
df.na.drop(how='all')           # drops any row that has ONLY NaN vals
df.na.drop(subset=['ColA'])     # choose cols to consider NaNs in
```

Interestingly, when filling NaNs, Spark only fills by matching data types.
```python
df.na.fill(0)                   # Will only replace NaN in numeric columns
df.na.fill("0")                 # Will only replace NaN in string columns
df.na.fill(0, subset=['ColA'])  # Will only replace NaN in ColA if it is numeric...
```

To fill NaN with imputed or aggregated data, such as the mean, we must import these functions first.
```python
from pyspark.sql.functions import mean
mean_result = df.select(mean(df['Yards'])).collect()    # must .collect() to return actual data
## output: [Row(avg(Yards)=400.5)]      # note result is Row obj inside a list!
mean_val = mean_result[0][0]            # returns 400.5
df.na.fill(mean_val, subset=['Yards'])

## one line:
df.na.fill(df.select(mean(df['Yards'])).collect()[0][0], subset=['Yards'])
```

### Dates and Timestamps
Must import desired functions.
```python
from pyspark.sql.functions import hour, month, year, dayofmonth, dayofyear, weekofyear, format_number, date_format

df.select(dayofmonth(df['Date'])).show()                    ## Use functions like attributes in Pandas
new_df = df.withColumn("Year", year(df['Date']))                ## add new col 'Year'
df_yr = new_df.groupBy("Year").mean().select(['Year', 'avg(Close)'])  ## renames close -> avg(Close)
df_yr = df_yr.orderBy("Year", ascending=False)                  ## same syntax as Pandas .sort_values
df_yr = df_yr.withColumnRenamed("avg(Close)", "Avg_Close")      ## rename SQL-style col to normal
df_yr.select(["Year", format_number("Avg_Close", 2)]).show()    ## format -> round to 2 decimal places

## Output of last command:
+----+---------------------------+
|Year|format_number(Avg_Close, 2)|      ## Note Horrible col name -- fix below
+----+---------------------------+
|2016|                     104.60|
|2015|                     120.04|
|2014|                     295.40|
|2013|                     472.63|
|2012|                     576.05|
|2011|                     364.00|
|2010|                     259.84|
+----+---------------------------+

## .alias method allows col renaming inline after a groupBy operation, like so.  
## Even after renaming via withColumnRenamed, the format_number method renamed col again
df_yr.select(["Year", format_number("Avg_Close", 2).alias("Avg_Close")])

## Output:
+----+---------+
|Year|Avg_Close|
+----+---------+
|2016|   104.60|
|2015|   120.04|
|2014|   295.40|
|2013|   472.63|
|2012|   576.05|
|2011|   364.00|
|2010|   259.84|
+----+---------+
```

#### Casting Columns to Datatypes
Apparently many operations on a Spark DF cause the datatype to be converted to a `string`.  So, a DF with correct numeric dtypes will be converted to all `string` upon using, say, `df.describe()`.  This creates problems for subsequent methods, obviously.

Use `.cast()` method to cast a column to a different data type.
```python
df['Level'].cast('float')
```

### Operating on Multiple Columns
I'm unsure of how to operate using a `.map()` or `.apply()` type method.  For now, I am using list comprehensions to act as a sort of mapping function.  So, using the `format_number` and `.cast()` methods from above, I will show an example of how to use list comps to greatly reduce the coding overhead for repeated operations. It leverages the `*` unpacking operator inside the `.select()` function.

In the FFT DF, there are a mix of numeric and `string` columns.  If I want to perform an operation on only the numeric columns, such as `format_number`, I have to first get them and then repeat the operation.  These numeric cols are assigned to the variable `numeric_cols` below.  

Pretend however that we've done some DF-level operation, such as `.describe()` that (apparently) re-types every column as a `string`, much to our chagrin.  The results of the `.describe()` operation are very long and unreadable numbers (of `string` type, mind you).  We want to fix this by using `format_number` to limit the values to two decimal places.  But since they're all `string` dtype, we can't use `format_number` on them until we first `.cast()` them into a numeric dtype, like `float`.

The base, brute force way to achieve this is to code every single desired column into the `.select()` statement, which is laborious, error-prone, and nigh unreadable.

##### Brute Force Way
```python
## After an operation that casts the numeric cols to string
df.select(format_number(dfd['Level'].cast('float'), 2).alias('Level'),
              format_number(dfd['HPm'].cast('float'), 2).alias('HPm'),
              format_number(dfd['MPm'].cast('float'), 2).alias('MPm'),
              format_number(dfd['PAm'].cast('float'), 2).alias('PAm'),
              format_number(dfd['MAm'].cast('float'), 2).alias('MAm'),
              format_number(dfd['SPm'].cast('float'), 2).alias('SPm'),
              format_number(dfd['Move'].cast('float'), 2).alias('Move'),
              format_number(dfd['Jump'].cast('float'), 2).alias('Jump'),
              format_number(dfd['CEV'].cast('float'), 2).alias('CEV'),
              format_number(dfd['HPc'].cast('float'), 2).alias('HPc'),
              format_number(dfd['MPc'].cast('float'), 2).alias('MPc'),
              format_number(dfd['PAc'].cast('float'), 2).alias('PAc'),
              format_number(dfd['MAc'].cast('float'), 2).alias('MAc'),
              format_number(dfd['SPc'].cast('float'), 2).alias('SPc'),
             ).show()
```

Ideally we could use a `.map()` method to apply the operations above repeatedly, but until I figure that out, here is the list comp approach.

##### Concise Way
```python
numeric_cols = ['Level', 'HPm', 'MPm', 'PAm', 'MAm', 'SPm', 'Move', 'Jump', 'CEV', 'HPc', 'MPc', 'PAc', 'MAc', 'SPc']
format_cols = [format_number(dfd[col].cast('float'), 2).alias(col) for col in numeric_cols]
## Note the unpacking '*' use, since .select() is a function. Otherwise we'd just pass in a list of cols and that wouldn't work.
df.select(*format_cols).show()
```

These two blocks of code achieve the same result!

Also, note the use of `.cast()` inside the `format_number` method, since we must convert to a numeric dtype before we can format it as such.  Recall the `.alias()` call at the end is simply renaming the now-changed column to its original name, else it would have a garish name like: `format_numberdfd['SPc'].cast('float'), 2)`, which is an affront to all literate humanity.




<BR>

## Machine Learning
We use the new and expanding [MLlib library](http://spark.apache.org/docs/latest/ml-guide.html).  
In Spark, dataframe structure for ML is a bit different than in Pandas / R.  Primarily, Spark DFs only use one or two columns as follows:

1. Supervised - two columns: _labels_ and _features_
2. Unsupervised - one column: _labels_

This structure requires a bit more data prep on our side before using ML.  In particular, features for supervised learning must be condensed into one column, meaning each row is a vector of all the features that align with the given label.  For a mental image, in Excel this would mean taking an entire row (in a _tidy_ dataset) and putting it into a single vector (list) to pair with its corresponding label.  Spark / MLlib has some preprocessing methods that help achieve this.

Here's an example of correctly structured data for a supervised learner:  

```bash
+-------------------+--------------------+
|              label|            features|
+-------------------+--------------------+
| -9.490009878824548|(10,[0,1,2,3,4,5,...|
| 0.2577820163584905|(10,[0,1,2,3,4,5,...|
| -4.438869807456516|(10,[0,1,2,3,4,5,...|
|-19.782762789614537|(10,[0,1,2,3,4,5,...|
...
```

__Note__: the _label_ column must be numeric.  If using a classification algorithm, we must correctly encode the target classes to numbers.

### Regression
#### Linear Regression
Imports come from the `.ml` library and corresponding ML family, such as regression.

```python
from pyspark.ml.regression import LinearRegression
data = spark.read.format("libsvm").load("sample_linear_regression_data.txt")
lr = LinearRegression(featuresCol='features', labelCol='label', predictionCol='prediction')
mod = lr.fit(data)
```

The `libsvm` format is uncommon in Python, but Spark documentation uses them because of their formatting.  It is used to load our training data, here.  The three arguments for `LinearRegression` are the required arguments, shown here with their default names.  If you change the name of your data columns, you must pass in the correct name to the model constructor.

MLlib has a very nice `summary` attribute which contains all the results of the model fitting.  Using it, we can access any result or attribute of the model as such:

```python
mod_summary = mod.summary

mod_summary.coefficients
## output: [Ax1, Bx2, Cx3... Xxn]
mod_summary.intercept
## output: -2.54938
mod_summary.rootMeanSquaredError
## output: 10.17564
## and so on...
```
