# JP Pandas and Numpy Notes:

## Pandas

[Great article on optimizing Pandas performance](https://tomaugspurger.github.io/modern-4-performance.html)


***
When using importing a .csv in pandas, the first steps you should take to ensure things will work are:

1. df.head()
2. df.describe()
3. df.info()
4. check col names for spaces, and clean them up with:

```python
cols = [col.replace(' ', '_') for col in df.columns.tolist()]
df.columns = cols
```

check out `pd.melt(df)` -- neat function for reorganizing a DF by "ungrouping" or "melting" a DF and reorganizing by a specified column and value.

```python
np.bincount([iterable_name])
```


#### Categorical Data for Performance
`pd.Categorical` -- special dtype for huge speed boost (converts col to a C-array for Pandas) for categorical data; obviously applies best to columns with many repeated entries, such as gender.  Stores in single bytes instead of KB+ for Python's "object" dtypes.  Learn and use.  See [this write up](https://www.continuum.io/content/pandas-categoricals)
(`.astype('category')`)
`df['CategoryCol'].cat.codes`
`df['CategoryCol'].cat.categories`




When running into "index not unique" issues, converting DF to a MultiIndex is commonly a sensible solution (since having repeated entries in the index suggest there are groups of repeated data/categories/fields).  Using `df.set_index` to specific columns to create a hierarchy will result in a unique index.  Of course, a brute force (and possibly unhelpful/unwise choice is to simply use `df.reset_index()` which gives a new integer range index, but this might confuse your results!)


`df.sort_index(axis=)`
`df.index.is_unique`

`pd.isnull(df).any(1).nonzero()[0]`



`pd.MultiIndex`
`pd.IndexSlice` (jeff reback video @ 38:49)

`df.where()`
`df.mask()`
`df.assign` -- create new col, but can do it _in line_, which is great for chained methods.
`df.select_dtypes()`
`df.get_level_values()`
`df.reindex()`
`df.droplevel()`
`df.unstack()`
`df.isnull()` / `pd.isnull(df)`
`df.notnull()`...


`df.rename(columns={})`


Access or change the `name` attribute of both columns and index:
`df.index.name`
`df.columns.name`

### Create New Column Conditionally in One Go
One way, not vectorized, of creating new DF cols is to churn through the DF row by row, making your evaluations, and assign a value to the new col based on these results, like this:
```python
for idx in df.index:
    if df.loc[idx, 'Col_A'] == <some value>:
        df.loc[idx, 'New_Col'] = <new value>
    else:
        df.loc[idx, 'Col_A'] = ....    #etc.
```
This is obviously not a vectorized approach and is inefficient. It is also somewhat ugly and has needless code. It is very much _non-pythonic_.  

There are multiple ways to shorten this -- though not vectorize it, unfortunately. Four of the most common, and flexible, methods are discussed here. We can use `np.where()`, a list comp, `df.map`, or a conditional boolean mask. Note however that the comparison target (i.e. the conditional value) in these methods is constant.  To compare one cell in a DF to another, changing value (row by row, for example), you have to index into each one.  This variation is shown at the bottom of this section.

##### 1. `np.where()`
```python
df = pd.DataFrame({'Col_A': list('ZZXYW'), 'Col_B': list('ABBCD')})
df['New_Col'] = np.where(df['Col_A']=='Z', 'green', 'red')
print(df)

# gives us:
  Col_A Col_B   New_Col
0   Z    A      green
1   Z    B      green
2   X    B      red
3   Y    X      red
4   W    D      red

# Can also use multiple conditionals via '&' and parenthesis:
df['New_Col'] = np.where((df['Col_A'] == 'Z') & (df['Col_B'] == 'B'), 'green', 'red')
# gives us:
  Col_A Col_B   New_Col
0   Z    A      red
1   Z    B      green
2   X    B      red
3   Y    X      red
4   W    D      red
```

##### 2. List Comps
```python
df['New_Col'] = ['red' if a == 'Z' else 'green' for a in df['Col_A']]
# Need to double check the multiple conditional list comp:
df['New_Col'] = ['red' if (a == 'Z') and (b == 'B') else 'green' for a in df['Col_A']] for b in df['Col_B']]
```

Can use `np.ravel` to flatten multiple columns into a single column for parsing/summing, like so:

```python
total = np.sum([1 if cell == 'X' else 0 for cell in np.ravel([df['Col_A'], df['Col_B']])])
>>> 2
```
Take note of how a nested loop is handled in a list comp. The outermost loop is the first loop in the comp, and so on:

```python
a = [1 if x > 0 else 0 for x in [0, 0, 22] for x in [1, 1, 1, 1]]
>>> 12
# Nested loop:
a=0
for x in [0, 0, 22]:
    for x in [1, 1, 1, 1]:
        if x > 0:
            a += 1
>>> 12
```

##### 3. `df.map()`
```python
df['color'] = df['Col_A'].map(lambda x: 'red' if x == 'Z' else 'green')
```


##### 4. Conditional Boolean Mask
Note how this method works.  We mask into the df by using a `.loc` indexer (which is not usually how we mask) to give us only the desired rows to _assign_ to.  The `index` argument (i.e. the `i` argument inside the `.loc[i, c]` indexer) is the conditional bool mask. The second, `column`, argument is the new column we are creating.  To achieve the results above, we would do the following:

```python
df.loc[df['Col_A'] == 'Z', 'New_Col'] = 'green'
df.loc[df['Col_A'] != 'Z', 'New_Col'] = 'red'
```

Compared to the other methods, this approach seems more wasteful (2 lines) for a simple binary assignment (either `green` or `red`).  However, the value in this method becomes apparent when we have more than a binary assignment.  As shown above, using list comps or `np.where` can become a bit tedious when we have more than two conditions to juggle, in any number of columns.  This approach handles that easily, as we see now:

```python
df.loc[df['Col_A'] == 'Z', 'New_Col'] = 'green'
df.loc[df['Col_A'] == 'X', 'New_Col'] = 'red'
df.loc[df['Col_A'] == 'Y', 'New_Col'] = 'yellow'
df.loc[df['Col_B'] == 'D', 'New_Col'] = 'purple'

# gives us:
Col_A Col_B   New_Col
0   Z    A      green
1   Z    B      green
2   X    B      red
3   Y    X      yellow
4   W    D      purple
```

See how easy and specific that was?  It is not the most concise, necessarily, but it allows exact specificity without prohibitively complicated syntax that the same statement would involve if you tried to accomplish it in a list comp.  This is also a way to create _dummy vars_ by hand in a specific manner. (Just create the new col and set it `= 0`, then assign `1` via the masks above.  Thus you have `0` by default and `1` for the positive matches)

##### Variable comparisons
In this example we see a variable comparison -- testing whether the value in `df_test['Association']` is equal to the value in `df_test['Pred_Assoc']` for each row of the DF and assigning '`Yes`' if so, else '`No`'.  Note we are using a list comp here, but the other methods are also valid.  I personally just find list comps to be the most natural in this case.

```python
df_test['Pred_Correct?'] = ['Yes' if df_test.loc[idx, 'Association'] == df_test.loc[idx, 'Pred_Assoc'] else 'No' for idx in df_test.index]
```  


Of the three, List Comps and `map` are usually the fastest but not extremely so.  However, in a DF of a given size all are faster than the for loops, and much nicer to see.


<BR><BR>

### Selecting Cols based on dtype
```python
df.select_dtypes(include=['float64']).apply(your_function)
df.select_dtypes(exclude=['string','object']).apply(your_other_function)
```

<BR><BR>

### `.str.contains(...)`
Very useful Series-level method which returns a bool mask.  Here, we get a mask for all rows that have a capital 'P', such as 'Packers'.
```python
df_eda['Home_Team'].str.contains('P')
```

### GREAT Pandas Function - show all DF for just one time
```python
from IPython.display import display
def show_more(df, lines=None):
    """
    Function to display specified length of a DataFrame on a per-call basis.  Default number of lines is full length of DataFrame.  Can be specified as second argument in function call.
    """
    default_rows = pd.get_option("display.max_rows")
    if lines == None:
        lines = df.shape[0]

    pd.options.display.max_rows = lines
    display(df)
    pd.options.display.max_rows = default_rows

```

### `pd.qcut` and `pd.cut`
Divide and bin an array/Series into N bins for set quantile (5 = quintiles, etc.)
`pd.cut` makes the cuts based on _value_ of the data points (so, outliers cause extreme results with one item in a top or bottom bin and the rest clustered opposite it).  This means the membership of each bin is not balanced.
`pd.qcut` makes the cuts based on _frequency_ of the data points, returning equally sized bins as much as possible.

Use argument `retbins=True` to return the bin ranges.


#### .size() and .value_counts()
We can use `.size()` (instead of `.counts()`) as an aggregator when we use `.groupby()` to return the number of appearances of the grouped-by items.
We can also simply use `.value_counts()` on any Series, but it will sort them (descending) by default, and we may not want the series to be sorted necessarily.

Pandas can [read HTML directly](http://pandas.pydata.org/pandas-docs/stable/io.html#io-read-html) into a DF.  When possible, this is the best option for simplicity.  The HTML file it reads can be an actual URL, a local file, or a single string of HTML.  One thing to note if using a URL to pass to the `read_html()` method is that this will send a request to the server for this URL.  Depending on how many HTML tables you need, pinging the server for each table is likely a bad idea, leading to disconnection, temporary site-wide 404s, or even IP blocking.  If you need many tables per page and on many pages, you would like be better off using *BeautifulSoup* to get the HTML contents (HTML strings included) and use the `read_html()` method on each part of the HTML soup you need.

### Comparing DFs to singletons like `None`
From [this Stack Overflow post](http://stackoverflow.com/questions/36217969/how-to-compare-pandas-dataframe-against-none-in-python)  
Use `is` or `is not`, do not use equality operators `==` or `!=`:

```python
if self.pandas_df is not None:
    print('Do stuff')
```
[PEP 8](https://www.python.org/dev/peps/pep-0008/) says:

> Comparisons to singletons like `None` should always be done with `is` or `is not`, never the equality operators.  

There is also a nice [explanation](http://jaredgrubb.blogspot.de/2009/04/python-is-none-vs-none.html) why.

### Index


Resetting the Index via `reset_index()` can be a bit tricky.  As I understand it, we need to `set_index('col_name')` first, else we get all `NaN`.

`df.set_index('col_A').reset_index()` resets the index, starting at 0, to the len of `col_A`.  Technically I believe this does set the index itself to `col_A` and then the `reset_index()` method makes a new column from the existing index and then reindexes along that col.

##### Get index of a row if val in column matches arg:
Using the `[0]` index of index returns the first match only.
`df[df['Date'] == 'Playoffs'].index[0]`
can use `.tolist()` or `.values` as well for multiple matches.   

`df.isnull().sum()` -- counts of all NaNs in all cols.
`df.isin([container])` or `df['col'].isin()` are how you check for matching in a container or val.  Returns bool, so can index into DF via this: `df[df['Job_ID'].isin(id_list)]` returns only the rows whose Job_IDs are in the specified ID list.  Note this means it can also be inverted with the `~` operator.
use `argsort()` in numpy to sort by a column in a matrix



Reading from Excel files is much slower than reading a CSV file (Excel files have much overhead and have to be parsed differently.  As a result, it is always best to convert the .xlsx file to a .csv file if possible for reading into a Pandas dataframe. When reading large spreadsheets, the difference can be staggering.  For example, reading in the DVOA_Big_List, a spreadheet with 89,414 cells, as an .xlsx file takes around 34 seconds, while the same data as a .csv takes only 18.5 ms!)




When adding new rows, must use `df.loc` and not `df.iloc`.  `df.iloc` could potentially misstep on which index to add.  If you want to add multiple values in one go to a single row, you can use a list of values exactly as long as the number of columns in the DF.  Otherwise, a single value will be assigned to all columns.  You can also add values to multiple rows in a single statement by passing a list of the desired rows to be assigned to.  

```python
In [1]: df
Out[1]:
            A       B
Hungry      No      Yes                        
Thirsty     No      No

# Create a new row via the following
df.loc['Dessert'] = 'Yay'               # Fills A and B with 'Yay'
df.loc['Dessert'] = ['Yay', 'Never!']   # A=Yay, B=Never, must be same len as len(df.columns)
df.loc['Dessert', 'A'] = 'Yay'          # Fills A with 'Yay', rest of row = NaN

```
Note that all the new row assignments above assign values to the entire row.  The row itself does not exist prior to the assignment statement above, but upon its creation we are filling the entire row in a blanket assignment statement.  If we want to create multiple new rows in one statement, even if we assign the same value to all cols, then Pandas needs every row to already exist in the index.  So, you can assign to multiple rows in a single statement, but we need to create these rows in a separate statement (likely a loop). It is easiest to assign them as `None` just to make an empty cell we can fill in later with whatever data we want.

```python
for row in ['Dessert', 'Wine']:
    df.loc[row] = None

# Create TWO new rows in single statement via list
df.loc[['Dessert', 'Wine']] = 'Always'
```
The same rules apply as above, including the ability to assign to either only a certain col (the rest will fill with `NaN`) or to use a list to choose each col's val.

### Datetime

#### Datetime Accessor
See [Datetime properties documentation](http://pandas.pydata.org/pandas-docs/stable/api.html#datetimelike-properties).  

`pd.to_datetime()` has a sort of "hidden" attribute, `.dt`, that allows you to snag only the part of the time that you want, like minutes or the day of the week.  The following example takes a full `Timestamp` or `DateTimeIndex` object (typically in the format of `2017-02-23 00:46:02`) and returns only the minutes from it.  

```python
df['Minutes'] = pd.to_datetime(df['Duration'], format='%H:%M:%S').dt.minute
```






```python
#clean up col names by replacing spaces with underscores -- needs to handle float
cols = df14.columns.tolist()
cols = [str(col) for col in cols] #convert all header names to str first\n",
cols = [col.replace(' ', '_') for col in cols]
cols = [''.join([i if 32 < ord(i) < 126 else "" for i in col]) for col in cols] #remove non-ASCII chars in headers
df14.columns = cols
```

find max value in a dict:
max(dict, key=lambda key: dict[key])





##### Get indices of cols based on which cols match a data type:
Here are two ways to get the indices of desired columns which, in this case, are either ints for floats.  
1. This way is possibly inefficient for large DFs because it doesn't take advantage of Pandas-specific methods, but is accomplished in one run.  

```python
for ind, col in enumerate(df.columns):
    if df[col].dtype in ['int64', 'float64']:
        print(ind, col)

# Here is the list comp:
inds = [ind for (ind, col) in enumerate(df.columns) if df[col].dtype in ['int64', 'float64']]
```

2. Uses Pandas `select_dtypes` and `get_loc` methods to get same result.

```python
cols = df.select_dtypes(include=['int64', 'float64']).columns
for col in cols:
    print(df.columns.get_loc(col))

# List Comp
inds = [df.columns.get_loc(col) for col in df.select_dtypes(include=['int64', 'float64']).columns]
```




Use `ipython --pylab` (or `%gui` ?) to enable interactive mode with IPython and MPL.  You can continue to work in IPy while a plot is displayed.


sort
df3.sort_values(['PA', 'MA'], ascending=[0,0])


`pd.to_csv()` exports to a csv, can pass the name you wish to save it under as a string; saves in dir the script was run in by default, put desired path in front of name to change destination.
Convert a series to datetime and return only the date by appending `.date()`:
`df['date'] = pd.to_datetime(df['real_dates'])[0].date()`


How to rename a column(s).  Note this function is used b/c it preserves column data instead of simply changing the names w/o also keeping the correct data:
`df = df.rename(columns = {'Job':'Jobe', 'OldCol2': 'NewCol2'})`

How to rename all cols by using a row:
`df.columns = df.iloc[0]` or `df.loc['row_name']`

How to reorganize cols:
cols = df.columns.tolist()
cols = cols[-2:] + cols[:-2] #or whatever order you need
cols.pop(whatever index you need to remove)
df = df[cols] (or df = df.ix[:, cols]) #remake df with right order


look into using `.eval()` in pandas
`dfl[dfl['visitor_ml']>=500].eval('Home - Visitor')`
The above command wouldn't work with spaces in the column names ('Home Score - Visitor Score' would fail). **update: don't use eval**


using aggregation then transform (VanderPlas) in Pandas
`df['PF/G_vs_Avg'] = df.groupby('Year')['PF/G'].transform(lambda x: x - x.mean())`
This creates a new column that is the normalized difference between the given data point (PF/G) and that year's mean PF/G.  Really nice.



Two steps to get date from a datetime series:
df['date'] = pd.to_datetime(df['date'])
df['date'] = pd.DatetimeIndex(df['date']).date

**note**: Don't have to convert `to_datetime` in order to get the `.date` but if you don't (in other words if you snag the `.date` from a series that is a string ('object'), it is much slower, and will result in frozen dataframes if the dataframe is large.  So, just convert first.)


How to drop by COLS:
`df.drop(['Col A', 'Col B', 'Col C'], axis=1, inplace=True)`

Note: axis = 1 means drop the column, and `inplace=True` makes the change permanent on this copy of the df.


How to drop by ROWS using a column criterion:
`df = df[df['date'] != '2015-07-10']`


Note you can't (so far as I have found) drop specific rows based on given critera (i.e. drop all rows where date = '2015-07-10') using the `drop` command.  If you can, I've not found how to do it correctly, and both Stack Overflow and an online book use the method above instead. So, we just make a new dataframe excluding the rows we don't want.

You CAN drop rows by specified indices, though:
`df3.drop(df3.index[4323:4333], inplace=True)`

Drop by the index name (whatever the data type is, and if it is an int it will drop based on the int displayed -- its 'name' -- and not the actual row-index integer.  So, if row 280 is labeled '7', it will be dropped if you use `df.drop(7)`, as will all other rows named '7'):
`df.drop(7, inplace=True)`


```python
df = pd.DataFrame({'a':[0,0,1,1], 'b':[0,1,0,1]})
df = df[(df.T != 0).any()]
df
   a  b
1  0  1
2  1  0
3  1  1
```

reset index:
df.reset_index(drop=True, inplace=True)


append
df.append(df2, ignore_index=True)


wrs = wrs.groupby('PNAME').agg(np.mean)



df.loc[] gives the index based on name passed (even if name is int, which could be anything, say 5000, in a df of only 20 rows.  )
df.iloc[] gives the actual integer position of the index (which goes from 0 to the len of the df)
df.ix[] takes either argument




index into a specific cell and change its value:
(This changes the value of HP to 777 for the row where Job == Monk)
`dfm.loc[dfm['Job'] == 'Monk', 'HP'] == 777`

For using multiple criteria:
`dfa.loc[((dfa['Job'] == 'Knight') & (dfa['MP'] == 80)), 'Job'] = 'dood'`

**note** there are other ways to do this, but this is the preferred method and doesn't trip up on a caveat or deprecation.

How to merely mask into (not change) a df based on multiple criteria:
`df3[(df3['exercise'] == 'Pec Deck') & (df3['date'] <= '2013-06-01')]`


**Pandas and Excel**
All row/col are zero-indexed in pd.read_excel()  
+ Sheetname: pick sheet to read in from Excel file. Can use index or string with exact name.
+ index_col: use a col to set as index for the DF (same as df.set_index())
+ skip_footer: number of rows from the bottom to skip when importing, 0=ignore no rows (default), 10=ignore last 10 rows of the spreadsheet, etc. Might be tough to use on some huge spreadsheets
+ header: explicitly set a row to be the header row, default 0th row.
+ parse_cols: set which cols to import into DF. Can be an int (col to stop at) or a list of ints. Can use string of col index such as "A:C, F" for Excel col refs. If None, import all calls (no parsing.)
+ skiprows: how many rows to skip from the top of the sheet when importing, 0=import all

Note: all of these operations can be done after importing via pandas, however if possible it is easier to do upon DF creation, and can potentially save memory if using big spreadsheets.

### String Manipulation
Pandas is actually quite adept at string manipulation, having some nice functions in addition to the standard `str` class.  One example is `extract`, which uses Regular Expressions to return matched text.  Here is an example of how we can use `extract` to split a string into separate parts and create new columns from those split parts. In the following string we have a compound word blob which actually represents a sex and age range, lower age to higher.  "m2435" represents male from 24-35:
```python
string = 'm2435'
words = pd.Series(string)
tmp_df = words.str.extract("(\D)(\d+)(\d{2})") #RegEx for match single char, then the next chars until the final two, then the final two.  See RegEx for more info.  

# Name columns
tmp_df.columns = ["sex", "age_lower", "age_upper"]

# Create `age` column based on `age_lower` and `age_upper`
tmp_df["age"] = tmp_df["age_lower"] + "-" + tmp_df["age_upper"]

# Merge
df = pd.concat([df, tmp_df], axis=1)

```


### Subset sorting
Don't sort an entire DF if you're only interested in the n-largest or n-smallest values in the DF.  Instead use Pandas' built-in `nlargest` and `nsmallest` methods.
```python
# Bad:
In [1]: delays.sort_values().tail(5)
>>> 63.3 ms per loop

# Good:
In [2]: delays.nlargest(5).sort_values()
>>> 12.3 ms per loop
```


### Append, Concat, Join, and Merge
Pandas can be tricky with these four options.  From my experience, they break down as follows: `append` and `concat` are similar, and `join` and `merge` are similar.  By default, `merge()` looks to join on common columns, `join()` on common indices, and `concat()` by just appending on a given axis.

#### Append and Concat
After much tinkering, I find that `append` and `concat` only stack the second DF onto the bottom of the first DF for `axis=0`, or the the right side for `axis=1`. `Concat` can intelligently combine the DFs if they share the same columns or same index. Append is more forceful in that it just stacks the new DF onto the bottom of the old DF, even if they share an index.  Because of its nature, `append` is also commonly used when adding a single series instead of an entire DF (though you will likely need to ignore the index, `ignore_index=True`).

(You can transpose both DFs in the function call to stack them sideways, then re-transpose the final product to have it back in normal order.  Doing so risks losing all columnar dtypes as the columns now have varied dtypes do to them actually being the rows of the original DFs. You also lose all column headers, so you will have to address both these issues if this is, for some strange reason, the method you need.  Can't imagine a situation in which it would be if you want to preserve that information.)  

As such, these two fit well with manipulation and combination of smaller tables or DFs (in my mind) for use largely in functions or whatnot.  One notable exception is that if you have data that is all in the same table structure (i.e. have the exact same columns in the exact same order), and you simply need to add to a "running list" of a DF, then these (`append` in particular) would be right to use.  

Example: you have Week 1 of NFL results for whatever columns (say the columns are points, turnovers, and punts).  After Week 2, you have the new week's data in the exact same structure.  You could just `append` Week 2 to Week 1, and then have a resulting DF of both weeks.  Etc.

#### Join and Merge

[Join](http://pandas.pydata.org/pandas-docs/version/0.19.2/generated/pandas.DataFrame.join.html) and
[Merge](http://pandas.pydata.org/pandas-docs/version/0.19.2/generated/pandas.DataFrame.merge.html#pandas.DataFrame.merge) behave much like the traditional SQL `join` function, but `merge` seems to be a slightly more focused, narrower sub-function of `join`.  See below.  
Here is a great [overview with examples](https://pythonprogramming.net/join-merge-data-analysis-python-pandas-tutorial/?completed=/concatenate-append-data-analysis-python-pandas-tutorial/) of these two functions.

##### Join
`join` in particular can be fussy.  If the two DFs have any of the same columns, the `join` function will use them both in the new DF.  To distinguish between them, Pandas appends a suffix to each to let you know which original DF the duplicate columns came from. As such you will have to provide the suffix which it appends, in the `lsuffix` and `rsuffix` arguments. So, `lsuffix = 'leftside'` would return the duplicate column from the left table in the join with "leftside" appended to the column name as its differentiator. (use `_[df_name]` to access the DF's index for this).

Pandas is strict about this, and Failure to provide suffix names gives a *ValueError*:  
`ValueError: columns overlap but no suffix specified: Index([u'Job_ID', u'Job', u'Identity', u'Role', u'Association'], dtype='object')`.  
Anytime you get this error, you have extra duplicate columns and must give a suffix for each (note the suffix can be the same word, if you want).

Use `caller.join(other, lsuffix='_caller', rsuffix='_other')` to join them based on their indices. Realize you will get duplicate columns, obvi.

If you want to join two DFs and use a shared column as the index for the new DF, then use: `caller.set_index('colA').join(other.set_index('colA'))`

So, if you want to join DF2 to DF1, on the same index, and only join the columns that are unique to them (probably the most commonly desired type of join...), then, as far as I can tell, using an `outer` join you will have to manually specify which columns from DF2 to join to DF1 (do this either ahead of time by creating a new, trimmed DF3 from DF2, or by passing DF2 in with a range of columns listed).  

Here is an example:  
`df1.join(df2.iloc[:, 3:8], how='outer')`  

This will get you all of `df1` and will add columns 3, 4, 5, 6, 7 from `df2` to `df1`, with the shared index (assuming the indices match, I believe.)  So, it is doable.  But it's touchy and frustrating.  Which leads us to a much better solution in `merge`.

##### Merge
Here is how easy it is to perform the final, commonly desired join using `merge` described at the end of the `join` section:  
`df1.merge(df2, how='outer')`

Boom. Done.  

`merge` has many other options, like what to join on and what types of joins, as well as separate indices to join on.  Read about it.  But in general, if you are trying to take parts of one DF and just slot them into a different DF because they both have the same indices, then you are going to use `merge`.  



dfm3.merge(dfc3, how='outer')






##### Stack/Unstack
Here is a [great overview blog post](http://nikgrozev.com/2015/07/01/reshaping-in-pandas-pivot-pivot-table-stack-and-unstack-explained-with-pictures/)
> Let us assume we have a DataFrame with MultiIndices on the rows and columns. Stacking a DataFrame means moving (also rotating or pivoting) the innermost column index to become the innermost row index. The inverse operation is called unstacking. It means moving the innermost row index to become the innermost column index.

Example using FFT DF:
```python
In [1]: df = pd.read_csv('~/Desktop/fft_class_stats.csv')
In [2]: df = df[df['Association'] == 'Generic']
In [3]: df.shape
Out [3]: (23, 20)
In [4]: df.head()
Out [4]: >>>
```

```bash
   Job_ID      Job Identity  Gender  Level     Role Association  HPm  MPm  \
70     4A   Squire   Squire  Either      1   Squire     Generic  100   75   
71     4B  Chemist  Chemist  Either      1  Chemist     Generic   80   75   
72     4C   Knight   Knight  Either      1   Squire     Generic  120   80   
73     4D   Archer   Archer  Either      1   Squire     Generic  100   65   
74     4E     Monk     Monk  Either      1   Squire     Generic  135   80   

    PAm  MAm  SPm  Move  Jump     CEV  HPc  MPc  PAc  MAc  SPc  
70   90   80  100     4     3    0.05   11   15   60   50  100  
71   75   80  100     3     3    0.05   12   16   75   50  100  
72  120   80  100     3     3    0.10   10   15   40   50  100  
73  110   80  100     3     3    0.10   11   16   45   50  100  
74  129   80  110     3     4    0.20    9   13   48   50  100
```

In order to `stack` or `unstack`, we need a MultiIndex (technically we can do it on a "flat" (2D, no nested indices) DF, but the result is just a `Series` that has either the rows or cols nested into the other, depending upon whether you stack or unstack).  If your DF is currently flat the primary way to create a hierarchy of indices (a MultiIndex)  is to use `set_index()`.  The flow of this operation is to choose columns in the existing DF to become part of the index (rows), and then choose which of those to become part of the columns nested structure.  

Using the example DF above, we would choose some columns (commonly the categorical ones) to be in our index.  Then we will choose to unstack either a specific index level or many of them.  By default `unstack` moves the _innermost_ row index to become _innermost_ column index.  Index levels are ordered outside-in, meaning the outermost index is level=0, the one immediately to the right of level 0 is level=1, and so on as the nesting of indices increases.  When calling `unstack` or `stack`, we can specify which level of index we want to move by either the number of the level or giving the name of the index itself.  By default the methods use `-1` as the level, which means the _innermost_ level (i.e. most nested).  

Here's an example using the FFT DF:  

1. Choose two (categorical) columns to be the index.  Order matters, here, as the first column named becomes the outermost index and they nest progressively after that. Then unstack the inner one (Role) to now be the new innermost column index.  Note the base `df` has no column (or row) nesting, so by unstacking the inner row index here (Role) we are going to place it as the inner column index under the existing columns, leaving 'Job' as the index for the rows.

2. The default `.unstack()` call is equivalent to `.unstack(1)` or `.unstack('Role')` in this case because 'Role' is level 1, and this is the innermost index level here.  This is also equivalent to `.unstack(-1)` which means the innermost level.  If we had three or more indices to begin with we could choose any sub-selection of them to unstack/stack.
    ```python
    In [1]: df_unstack = df.set_index(['Job', 'Role']).unstack('Role')
    In [2]: df_unstack.head()
    Out [2]: >>>
    ```
    ```bash
                Job_ID                    Identity                    Gender                    
    Role       Chemist Generic Squire      Chemist Generic  Squire   Chemist Generic  ...
    Job                                                                             
    Archer        None    None     4D         None    None  Archer      None    None  ...
    Bard            5B    None   None         Bard    None    None    Either    None  ...
    Calculator      5A    None   None   Calculator    None    None    Either    None  ...
    Chemist         4B    None   None      Chemist    None    None    Either    None  ...
    Dancer        None    None     5C         None    None  Dancer      None    None  ...
    ...
    ...

    ```

    We see that the new, unstacked DF indeed has 'Job' as the index for the rows and the 'Role' index is now the innermost column index.  This means for every column in the original DF ('Identity', 'Gender', 'Level', ... etc.) now have their own nested columns of 'Role' (which itself has three categories: 'Chemist', 'Generic', 'Squire').

    If we want to see the names of the levels for the rows or columns we can just use the `.names` attribute:  
    ```python
    In [1]: df_unstack.index.names
    Out [1]: FrozenList(['Job'])

    In [2]: df_unstack.columns.names
    Out [2]: FrozenList([None, 'Role'])     # None is original set of cols, unnamed upon original import.
    ```


`df.swaplevel` and `df.reorder_levels` can be used to rearrange a multiindex.

`df.columns.get_level_values(0)`









example from pellucid using `large_df_trial.csv`:  
`df = df.set_index(['companyId', 'raw_date', 'fe_item', 'fe_per_rel', 'periodicity']).unstack(['fe_item', 'fe_per_rel', 'periodicity'])`





<BR><BR><BR><BR><BR>

## NumPy
`np.convolve`
`np.percentile(a, q)` -- `a` is the array you are finding the percentiles of, `q` is the quantile you are searching for (i.e. `q=50` returns the value that is at the 50th percentile of `a`)
