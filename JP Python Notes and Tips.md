## JP Python Notes and Tips </p>


### <p style="text-align: right;"> File Input and Output </p>
***
To read or write to a file, we have to open it first.
When opening a file, we can specify the exact (full) file path, which will locate the file regardless of the directory we are operating our script from, or we can use a relative file path, which requires the file to be in the same directory as the script we are running.  If no file of the given name exists, the file will be created in the referenced directory.

```python
filename = raw_input("Enter file:")         # Enter filename
try:
    if len(filename) < 1:
        filename = "de-tab-me.txt"          # Relative path
        filename = '/Users/jpw/desktop/.de-tab-me.txt'      # Full path
        print "Using", filename
except:
    print 'File not found; creating file.'

handle = open(filename)
```

To open the file we use the `open()` command.  By default, Python makes the open command read-only. If  we want to do anything besides simply read the file, we have to enter a second argument to the `open()` function.

<br>

##### **Open File Arguments**

Command | Function
------- | --------
`open(filename, 'r')` | open text file in read-only mode, cursor positioned at beginning of the file.
`open(filename, 'w')` | open text file in write-only mode, truncate file to zero length by erasing existing file! Cursor positioned at beginning of the file.
`open(filename, 'a')` | open text file in write-only mode, cursor positioned at end of existing file (no erasure).
`open(filename, 'r+')` | open text file in read-and-write mode, cursor positioned at beginning of the file.
`open(filename, 'w+')` | open text file in read-and-write mode, truncate file to zero length by erasing existing file! Cursor positioned at beginning of the file.
`open(filename, 'a+')` | open text file in read-and-write mode, cursor positioned at end of existing file (no erasure).
`open(filename, 'rb')` | open file to read in binary mode, ideal for .csv files, among others.
`open(filename, 'wb')` | open file to write in binary mode, ideal for .csv files, among others
`open(filename, 'ab')` | open file to append to in binary mode, ideal for .csv files, among others





If you are to use a dual-mode such as `r+` or `w+`, you need to familiarize yourself with the `.seek()` method too, as using both reading and writing operations will move the current position in the file and you'll most likely want to move that current file position explicitly between such operations.

<br>

##### CSV files
One issue that may arise when reading from and writing to files is that the `open()` command treats every file as a text file by default.  Obviously, this is not always the case.  

For example, when working with a .csv file, commonly found in Excel, we need to add an argument to the `open()` function to specify that it is not a text file, else we might encounter unexpected issues such as skipping lines/cells when writing data. (Following info on .csv use is from this StackOverflow post: http://stackoverflow.com/questions/4249185/using-python-to-append-csv-files)

> CSV is a binary format, believe it or not. The csv module is writing the misleadingly-named "lineterminator (should be "rowseparator") as `\r\n` as expected but then the Windows C runtime kicks in and replaces the `\n` with `\r\n` so that you have `\r\r\n` between rows. When you "open" the csv file with Excel it becomes confused

> Always open your CSV files in binary mode (`'rb'`, `'wb'`, `'ab'`), whether you are operating on Windows or Linux. That way, you will get the expected rowseparator (CR LF) even on \*x boxes, your code will be portable, and any linefeeds embedded in your data won't be changed into something else (on writing) or cause dramas (on input, provided of course they're quoted properly).

> Other problems:

> + Don't put your data in your root directory (`C:\`). Windows inherited a hierarchical file system from MS-DOS in the 1980s. Use it.

> + If you must embed hard-wired filenames in your code, use raw strings `r"c:\test.csv"` ... if you had `"c:\test.csv"` the `'\t'` would be interpreted as a TAB character; similar problems with `\r` and `\n`

> + The examples in the Python manual are aligned more towards brevity than robust code.

> Don't do this:

> ```python
w = csv.writer(open('foo.csv', 'wb'))
```

> Do this:

> ```python
f = open('foo.csv', 'wb')
w = csv.writer(f)
```

> Then when you are finished, you have `f` available so that you can do `f.close()` to ensure that your file contents are flushed to disk. Even better: read up on the new `with` statement.


##### Seeking
Regarding seek() there's not too much to worry about. First of all, it is useful when operating over an open file. It's important to note that its syntax is as follows:

```python
fp.seek(offset, from_what)
```

where `fp` is the file pointer you're working with; `offset` means how many positions you will move; `from_what` defines your point of reference:

0: means your reference point is the beginning of the file
1: means your reference point is the current file position
2: means your reference point is the end of the file
if omitted, from_what defaults to 0.

Never forget that when managing files, there'll always be a position inside that file where you are currently working on. When just open, that position is the beginning of the file, but as you work with it, you may advance. `seek` will be useful to you when you need to walk along that open file, just as a path you are traveling into.


##### Installing Packages with Anaconda
Anaconda Python and Packages

We use the Anaconda scientific python stack which is just a vanilla version of Python 2.7 along with all the packages that a data scientist would need, including NumPy, SciPy, SciKit-Learn, Pandas, and matplotlib. Anaconda manages the Python environment for us. If you need to install other Python packages (unlikely), do so with the conda command-line utility (i.e. `conda install some-cool-package`). Use conda list to see what's installed.


### <p style="text-align: right;"> Data Structures and Modules </p>
***

'Containers' such as lists, dictionaries, sets, and tuples are called *iterables*.

#### Dictionaries
<br>


#### Generators

When joining the items of a list, it is commonly best to use a generator instead of a list.  Generators store and return items one at a time, regardless of how large the containing list is.  Lists, however, construct and store all items in memory at once.  When you have a list of only 100 items, there is little benefit to using a generator over a list, but when you are dealing with large data sets which might have one million -- or more -- data points, for example, then you are well served not to commit all one million to memory and instead churn through them one at a time.  We create a generator in two ways: the first is to use parenthesis instead of brackets in a 'list' comprehension.  The second is to use the `yield` command (in lieu of the `return` command) in a function, causing the function itself to become a generator.  Here is a great explanation of Python generators: https://jeffknupp.com/blog/2013/04/07/improve-your-python-yield-and-generators-explained/

```python
# Constructing a 'list' of any size via a generator
new_data = (' '.join(word) for word in word_list)

# Make a generator function by using 'yield' instead of 'return'
def get_primes(number):
    while True:
        if is_prime(number):
            yield number
        number += 1
```
<br>

#### Sorting

To sort an iterable, usually a list or dictionary, we can use the `sorted()` function.

```python
liz = [1, 2, 6, 8, 4, 5, 9, 7, 3, 10]
sorted_liz = sorted(liz)
print sorted_liz
# [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
```

To sort in descending order, add the `reverse=True` argument to the `sorted()` call.
```python
liz = [1, 2, 6, 8, 4, 5, 9, 7, 3, 10]
sorted_liz = sorted(liz, reverse=True)
print sorted_liz
# [10, 9, 8, 7, 6, 5, 4, 3, 2, 1]
```

To sort a dictionary or a multi-dimensional list (one that is composed of other lists or tuples), we have to add a `key` argument using a `lambda` function to our `sorted()` call.

```python
liz = [(1, 'a'), (2, 'c'), (43, 'e'), (65, 'g'), (6, 'r'), (28, 'f'), (9, 'z'), (3, 'y'), (87, 'a')]
# This will sort by the first sub-index, the integers in this case, of each tuple in this list.
sorted_liz = sorted(liz, key=lambda item: item[0], reverse=False)
print sorted_liz
# [(1, 'a'), (2, 'c'), (3, 'y'), (6, 'r'), (9, 'z'), (28, 'f'), (43, 'e'), (65, 'g'), (87, 'a')]
```

If we wanted to sort by the second sub-index, the letters, we'd use item[1] above.  And so on for as many n-dimensions of the iterable which you'd like to sort by.

For a dictionary, we need to add the `.items()` method to the dictionary name in the `sorted()` call.

```python
diction = {'bones': 1, 'c': 1, 'dude': 1, 'ramza': 1, 'lite': 1, 'Trisection': 1, 'ff': 2, 'Y': 3}
# This will sort diction by the second sub-index, in this case the count, in descending order.
sorted_diction = sorted(diction.items(), key=lambda item: item[1], reverse=True)
print sorted_diction
# [('Y', 3), ('ff', 2), ('bones', 1), ('c', 1), ('dude', 1), ('ramza', 1), ('lite', 1), ('Trisection', 1)]
```



#### Counter (from collections module)
```python
c = Counter('abcdeabcdabcaba')  # count elements from a string

c.most_common(3)                # three most common elements
[('a', 5), ('b', 4), ('c', 3)]

sorted(c)                       # list all unique elements
['a', 'b', 'c', 'd', 'e']

''.join(sorted(c.elements()))   # list elements with repetitions
'aaaaabbbbcccdde'

sum(c.values())                 # total of all counts
15

c['a']                          # count of letter 'a'
5

 for elem in 'shazam':           # update counts from an iterable
...     c[elem] += 1                # by adding 1 to each element's count

c['a']                          # now there are seven 'a'
7

 del c['b']                      # remove all 'b'
 c['b']                          # now there are zero 'b'
0

d = Counter('simsalabim')       # make another counter
c.update(d)                     # add in the second counter
c['a']                          # now there are nine 'a'
9

c.clear()                       # empty the counter
c
Counter()
```

Note: If a count is set to zero or reduced to zero, it will remain
in the counter until the entry is deleted or the counter is cleared:

```python
c = Counter('aaabbc')
c['b'] -= 2                     # reduce the count of 'b' by two

c.most_common()                 # 'b' is still in, but its count is zero
[('a', 3), ('c', 1), ('b', 0)]

c = Counter()                          # a new, empty counter
c = Counter('gallahad')                # a new counter from an iterable
c = Counter({'a': 4, 'b': 2})          # a new counter from a mapping
c = Counter(a=4, b=2)                  # a new counter from keyword args

```




#### Pandas

When using importing a .csv in pandas, the first steps you should take to ensure things will work are:

1. df.head()
2. df.describe()
3. df.info()
4. check col names for spaces, and clean them up with:

```python
df2 = df.copy()
cols = df2.columns.tolist()
cols = [col.replace(' ', '_') for col in cols]
df2.columns = cols
```
