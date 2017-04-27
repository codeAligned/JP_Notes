## Galvanize Python Prep Class Notes:

Instructors: <br>
sean.sall@galvanize.com <br>
cary.goltermann@galvanize.com <br>
joshua.bernhard@galvanize.com <br>

### <p style="text-align: right;"> 3/29/16 - Wk1 Day1: Terminal </p>

**Basic Terminal Keyboard commands**

Command | Function
------- | --------
**Ctrl-c** | to issue a "Keyboard Interrupt" which terminates almost all scripts and server connections.  Note this ends IPython notebook usage and returns to terminal once done.
**Ctrl-r** | readline-style reverse history search (partial matching)
**Ctrl-a** | move cursor to beginning of line
**Ctrl-e** | move cursor to end of line
**Ctrl-k** | deletes all text after cursor
**Ctrl-u** | discard all text on line
**Ctrl-f** | move cursor one character forward
**Ctrl-b** | move cursor one character back
**Ctrl-l** | clear screen
**q** | exits source view or stream view modes (Basically when terminal enters a different scope that says 'return' and beeps at any key press, hit **q**)

<br>

**Basic Terminal commands**

Command | Function
------- | --------
`pwd` | print working directory
`cd` | change directory
`ls` | list items visible in current directory
`touch` | creates file from Terminal (`jpw$ touch new_file.txt` makes new_file.txt)
`mkdir` | make directory
`rmdir` | remove directory (dir must be empty)
`cp` | copy specified file (`cp [file] [destination if desired]`)
`mv` | move file (default overwrites existing files in named directory; careful!); use to rename files too
`rm` | removes file
`which` | info about named item
`who` | lists variables in namespace


#### Terminal Notes:

+ 	`cd ..`

    to make working directory become the parent/enclosing directory
	(i.e. back up one level).  Can string together as `cd ../..` (to go up two folders) etc.
	This can be done for all relative file paths, and can be used to string together navigation
	to go up and then down into a different folder using relative paths (../../diff folder)


+ 	`ls -a`

    shows hidden files (config / profile / prefs are commonly hidden)


+ 	`touch`

    can make multiple files in one line, "touch file1.txt file2.txt"


+ 	`cd`

    alone returns to root directory (/Users/jpw)


+ 	`rmdir -r [dir]`

    attempts to recursively remove all files in named dir, will give
	warning if certain files are protected or missing.


+ 	`rmdir -rf [dir]`

    recursively force removes all files, gives no warnings.  Nukes it.  Use caution.


+ 	`cp`

    can specify dir to copy to as second arg of cp (`cp file.txt ~/desktop`)
	And can specify new name for copied file with a slash and name, `cp [file] [dest]/[new name.ext]`


+ 	`mv`

    use \* for wildcards, such as `mv \*.txt` will move all .txt files in current directory.  Can use `mv \*` to move all files in dir, but moves hidden files and can cause probs (Unsure?)


+ Abbreviate root dir (`/Users/jpw`) with a `~`

    Ex: `/Users/jpw/Desktop` = `~/Desktop`


+ Absolute path names in terminal begin with a forward slash.

    ex: `/Users/jpw/desktop`

+ **Terminal** is also called **bash** or **the shell**.  For practical purposes, these terms are synonymous.


<br>

##### I-Python:
**Basic IPython Magic commands**

Command | Function
------- | --------
`ipython` | initiates an IPython session with its own namespace in the terminal
`%quickref` | display the IPython Quick Ref card
`%magic` | display detailed info for all Magic commands
`%debug` | begin interactive debugger at the bottom of the last traceback
`%pdb` | automatically enter debugger after any traceback
`%history` | print command input (and optionally output) history
`%history ~1/1-5` | print lines 1-5 of last session
`%paste` | paste code from clipboard, as formatted (!), and immediately execute it
`%cpaste` | same as `%paste` except do not immediately execute, allowing more to be pasted if desired
`%reset` | move file (default overwrites existing files in named directory; careful!); use to rename files too
`%page` *object* | pretty print the object and display through a pager
`%run` *filename.py* | run the given Python script inside the IPython shell in an empty namespace, with no imports or variables inherited from the shell (runs the script isolated, fresh; this is the main way to run scripts in IPython)
`%run -i` *filename.py* | runs script with access to existing namespace variables (can cause conflicts)
`%run -d` *filename.py* | run in debug mode
`%run -t` *filename.py* | measures execution time
`%prun` *statement* | execute *statement* with `cProfile` and report the profiler output
`%time` *statement* | report time taken to execute a *statement* **one time** (seems like `%timeit` is always better)
`%timeit` *statement* | report average time taken to execute a *statement* **multiple times** (good for testing performance of given code, especially with short execution times!)
`%who`, `%who_ls`, `%whos` | display variables defined in IPython namespace with varying levels of info.
`%xdel` *variable* | delete a variable and attempt to clear any refs to the object in IPython namespace
`%logstart` | begins logging entire console session, input and output.
`%bookmark` | set bookmark to current directory
`%alias` | define custom shortcut for shell commands
`%env` | return systen environment variables as a dictionary
`exit()` | exits the IPython terminal and returns you to standard bash terminal
`q` | exits source view or stream view modes, such as profiler mode (basically when terminal enters a different scope that says 'return' and beeps at any key press, hit **q**)






#### IPython Notes:

+ **IPython v. Standard Python in Terminal**
  + Note that all commands in the standard bash terminal, such as `pwd`, `ls`, or `touch` still work when in an IPython shell. The only difference is that IPython allows you to have a 'mini' coding environment, or namespace, that can directly interact with the Python language, such as storing variables, calling functions, and performing operations, *directly in the terminal*, as opposed to having to run an entire "filename.py" script.  

  + Ex: you can type `a = 5`, `b = 7`, `a + b` and you will get an output of "12".  These variables will be stored in the IPython shell's namespace while in use and can be recalled at any time.  Hence, a 'mini' coding environment.  Very useful.  IPython is the *de facto* environment for modern data science.

  + Preface any command with an exclamation mark `!` (also called a "bang") to interact with the Operating System shell via IPython, such as `!make`

+ `IPython`
  + To begin an I-Py session in Terminal, simply type `ipython` and it will load IPython instead of Python in the terminal.  You can load IPython notebooks or run any python script by typing the `ipython notebook` command followed by the file.  You can also just write `jupyter notebook` to load all ipy notebooks in a given directory.

  + CTRL + H gives all keyboard shortcuts

  + Ex: `ipython notebook test1.ipynb` opens the "test1.ipynb" IPython Notebook in your browser, fully interactive.

+ `?`
  + In IPython in Terminal, use a `?` after any command, such as `a_str.upper?` to get info about the func. This is like `help()` in Spyder.  Use two `??` to also include source code of the command (nice).

+ `%pdb`
  + Use the following import for debugging in IPython: `import pdb`.  Set the start point of the debugging process with `pdb.set_trace()` in your script (*not in IPython shell*) to initiate an interactive IPython debugger.

+ `%bookmark`
  + `%bookmark <name> <dir>` - set bookmark to &lt;dir&gt;
  + `%bookmark -l` - list all bookmarks
  + `%bookmark -d <name>` - remove bookmark
  + `%bookmark -r` - remove all bookmarks
  + `%cd -b <name>` - access a bookmarked folder with this command, or simply `%cd <name>` if there is no directory called <name> AND there is such a bookmark defined.
  + Your bookmarks persist through IPython sessions, but they are associated with each profile.

<br>


#### Github Notes:

**Git & Github Terminal Commands:**  
All commands in terminal are prefaced with 'git'.

Command | Function
------- | --------
`git` | displays common git commands and a brief overview.
`git config` | displays configuration commands for your account.
`git init` | initializes a git repository with git commands. This also creates a .git folder within the git repository; *must have this folder to work.*
`git add [file]` | add named file to the git branch/directory and track it. *Note:* `git add .` adds all changes in current  dir.
`git commit -m "type your summary here"` | sets a checkpoint for file/branch versioning in your git. *Note:* must add summary message when committing, use single quotes, keep brief
`git status` | gives status of files, branches, and commits
`git remote -v` | gives target URLs for push/pull in a given folder (repo)
`git remote set-url` | changes URL for push/pull in a given folder (follow instr)
`git log` | gives log of commits
`git clone [url]` | clones a git repository into working dir. Do **not** make a new git repository within an existing git repository!  *Note:* usually done to clone a git repo from github to your local drive.  By default you can't edit a repo you cloned from someone else (can edit your own).  Use this to get/create/initialize an copy of an existing online git repository to begin working.  Once cloned, use push/pull to send/receive modified files to/from the online repository.
`git push` | pushes edited git repository & files up to the original github repository. This is how you share changes over github
`git pull` | pulls updated changes from online repository on github down to your computer,	assuming you have that existing repo on your laptop  
`git rm [file]` | removes file/folder *from local machine*. Use `-r` for folders. See: https://git-scm.com/docs/git-rm
`git rm [file] --cached` | removes file/folder from git index but *saves local*.  Once deleted, you must `add`, `commit`, and `push` to your repo to sync it with your local index, to reflect the deletions on your github repo.

+ Some more advanced commands can be found at https://services.github.com/kit/downloads/github-git-cheat-sheet.pdf

<br>

**Git & Github Terminal Terms:**  

Command | Function
------- | --------
**Repository / Repo** | This is the project's source code that resides on github.com's servers. You cannot modify the contents of this repository directly unless you were the one who created it in the first place.
**Fork** | Forking a project will create a copy of the original repository that you can modify as you please. Forked projects will appear in your own github.com account.
**Cloning** | this will clone an online repository to your hard drive so you may begin working on your modifications. This local copy is called your local repository.
**Branch** | A branch is a different version of the same project. In the case of T2DMIT, you will see 2 branches : the master branch and the development branch.
**Remote** | A remote is simply an alias pointing to an online repository. It is much easier to work with such aliases than typing in the complete URL of online repositories every single time.  Example: `git remote set-url origin <new url>` will replace the old url with the new one as the origin (master) for that repo.  This is very handy.
**Staging Area** | Whenever you want to update your online repository (the one appearing in your github.com account), you first need to add your changes to your staging area. Modifying files locally will not automatically update your staging area's contents.
**Fetch** | git fetch will download the current state (containing updated and newly created branches) of an online repository without modifying your local repository. It places its results in .git/FETCH_HEAD.
**Merge** | git merge will merge the modifications of another branch into the current working branch.
**Pull** | git pull is actually a combination of git fetch and git merge. It fetches the information from an online repository's branch and merges it with your local copy.
**Add** | Whenever you modify a file in your local repository or create a new file, that file will appear as unstaged. Calling git add allows you to specify files to be added to your staging area.
**Commit** | A commit records a snapshot of your staging area, making it ready to be pushed to an online repository.
**Push** | git push will take all of your locally committed changes and upload them to a remote repository's branch.


#### Git Notes:
+ This is the best basic tutorial I've found:
  + https://github.com/GarageGames/Torque2D/wiki/Cloning-the-repo-and-working-with-Git  

+ Visual Cheatsheet:
  + http://ndpsoftware.com/git-cheatsheet.html#loc=remote_repo;

+ Other basic tutorials:
  + https://www.atlassian.com/git/tutorials/
  + http://rogerdudler.github.io/git-guide/

+ Do **not** `commit` very large files, like anything over ~20 MB!

+ When forking, always save forked repo into your account (*jp-wright*) unless otherwise noted.

+ Once you clone a repo (from a forked repo in your online profile), you can move it anywhere on your local HDD and it will be fine.  Git doesn't care about the absolute path on your local HDD, it only wants the relative paths within the cloned repo.  There is a hidden *.git* folder in the top-most level of every repo that has all the important syncing commands.  As long as it is in the repo, your git will work.

+ If we've made changes to the repository after you forked it and you want to update your repository to reflect them, you can use the 'master' command.  For example: `git pull https://github.com/zipfian/precourse master`

+ If you try to push and get "Repository does not exist" this probably means that you cloned from the Zipfian repo rather than your fork. Make sure you created a fork to your account. Then run this command to push: `git push https://github.com/<your username>/precourse master`

+ If VIM loads in terminal in response to some commands and prevents you from exiting/acting, hit **shift + colon**, then **w**, then **q**, then **enter** to exit VIM and return to terminal





<br>
### <p style="text-align: right;"> 3/31/16 - Wk1 Day2: Using Python </p>
***
There is a function, `enumerate()`, that will allow us to iterate through a list or string
(grabbing each of the individual elements in the list or characters in the string)
while at the same time keeping track of the index. The trick is that instead of using
just one variable (such as num above) to store the elements of the list as you loop through
them, we use two variables. The first of these variables stores the current index, and the
second stores the corresponding element in the list. Let's see how it works...

```python
In [1]: my_lst = ['a', 'b', 'c', 'd', 'e']

In [2]: for idx, num in enumerate(my_lst):
   ...:     print idx, num
   ...:
0 a
1 b
2 c
3 d
4 e
```

The trick here is that when we call `enumerate()` on our list, `enumerate()` gives us back two
values at each iteration through the loop. The first value is the current index
(which we chose to store as `idx` above), and the second value is the current element of the list
(which we chose to store as `num`). Note how `idx` tracks one behind `num`... this is because
`idx` starts at 0 and `num` starts at 1.

*Note*: you can add an argument to the `enumerate()` func to tell it at what index to begin:
```python
for idx, val in enumerate(lst1, 7):
	...
```
This starts the enumerate loop at index 7.

<br>
### <p style="text-align: right;"> 4/5/16 - Wk2 Day3: Basic Data Structures </p>
***
List Constructor, `list()`, is pretty powerful.  Builds a separated list of the single item entered.
However, argument must be an iterable (e.g. no number -- must be converted to string first.)
So, to make a list of the number 1234, use `list('1234')`.  You'll need to re-convert the
list of string `['1', '2', '3', '4']` to ints afterwards.

<br>
### <p style="text-align: right;"> 4/7/16 - Wk2 Day4: More Data Structures </p>
***
Neat trick to traverse an iterable in reverse is to slice with `[::-1]`
```python
for x in lst1[::-1]:
	print x
```


`id()`:
+ gives location / unique ID in memory of object.  Use to check which variables point
to which objects.

`zip(...)`: (e.g. `zip(seq1 [, seq2 [...]]) -> [(seq1[0], seq2[0] ...), (...)]`)
+ Return a list of tuples, where each tuple contains the *i-th* element from each of the argument sequences.  The returned list is truncated in length to the length of the shortest argument sequence. When there are multiple arguments which are all of the same length, `zip()` is similar to `map()` with an initial argument of `None`. With a single sequence argument, it returns a list of *1-tuples*. With no arguments, it returns an empty list.

+ The `zip()` func can help create a list of tuples of your determined grouping arrangement  

+ `zip()` in conjunction with the \* operator can be used to unzip a list:
```python
In [1]: x = [1, 2, 3]
In [2]: y = [4, 5, 6, 7]
In [3]: zipped = zip(x, y)
In [4]: zipped
Out [4]: [(1, 4), (2, 5), (3, 6)]       
In [5]: x2, y2 = zip(*zipped)
In [6]: x == list(x2) and y == list(y2)
Out [6]: True
```

+ Note here how the 7 in the second list (`y`) is not zipped because it has no equivalent partner in the first list (`x`), so there is nothing to zip it with!


In **Sets**:
+ `union()` simply returns the common elements b/w two sets.
+ `update()` actually changes the set it is used on.


In parsing user input (or other strings/lists), you can string together methods on strings, such as `userinp = raw_input().replace(' ', '').split('-')` for a string of numbers with any number of hyphens or spaces in it.
+ Example: 1 - 22  - 7-4-12 -15


### <p style="text-align: right;"> 4/12/16 - Wk3 Day5: Basic Functions & Comprehensions </p>
***
Absent  -- Landmark Grad Night
See Lecture in Github


### <p style="text-align: right;"> 4/14/16 - Wk3 Day6: Advanced Functions </p>
***

When using file input read methods, we can use ``with open([file_name]) as f`` in lieu of using the ``read`` function, which means we don't have to remember to use ``close`` function as well. This merely imports the file desired as `f`

Importing v. Running Python scripts:
We can import a .py script using the familiar ``import`` keyword, such as ``import test.py`` in Ipython. This allows us an ability to test stuff outside of the main block of code w/o running the main block.

We use the following:
```python
def main():
	#[stuff/code/the main program]...

if __name__ == '__main__':
    main()
```





...to create a barrier blocking stuff in the main block from an import. So, when we import we do NOT run anything in the main block of code. When we run the .py script, we, of course, DO run the main block.

<br>
### <p style="text-align: right;"> 4/19/16 - Wk4 Day7: Intro to Classes (OOP) </p>
***
In naming Classes v. Functions, we use "Camel Case" (Each word capitalized, no seperators) compared to "Snake Case" (all lowercase, underscore separators) for functions.

Class Example:
+ `class PizesTrophy(self):`

Function Example:
+ `def pizes_trophy():`

Note the direct access of variables within the class via the dot operator, even in the init setup of the instance/object.

```python
In [1]: class OurClass():
...: 	def __init__(self):
...:     	self.name = 'Intro Python'

In [2]: our_class = OurClass()
In [3]: our_class.name
Out[3]: 'Intro Python'
```

Note that we could create attributes in methods other than the `__init__()` method. However, this is in general not considered to be good practice. To see why, imagine what would have happened if we called the check if `at_capacity` method before `self.at_capacity` was set? It would have thrown an error! Typically, we want to at least define all of the attributes that might ever be accessed on our object in the `__init__()` method. We can give that attribute a default value, or we can simply assign `None` to it.

So, in summary, it is considered smart (and welcomed) practice to define all variables of a class in the `__init__()` method of the class.


**Magic Methods** (methods within the class that are bookended by double underscores, such as the `__init__()` method, are used to increase functionality of the class object.  But do note they are largely limited to predefined methods in Python.  For example, using the `__add__` Magic Method...
```python
class PizeTrophy(self):
	...(init and methods...)

	def __add__(self, obj2):
	return self.weight + obj2.weight
```
allows us to simply add a number of trophies together by using the `+` operator which Python has linked to the `__add__` Magic Method by default.  
`Trophy1 + Trophy2` = `223`  (or whatever the sum of the two trophies' weights would be.)

Hence using the Magic Methods for the default/recognized operations by Python is the best way to increase functionality, so far as I understand.

`__str__()` and `__repr__()` are probably the two most common Magic Methods (apart from `__init__()`) which help with formatting and returning values to be printed

<br>
### <p style="text-align: right;"> 4/21/16 - Wk4 Day8: Advanced Classes </p>
***
No notes b/c of Katie fiasco

<br>
### <p style="text-align: right;"> 4/26/16 - Wk5 Day9: Reverse Polish Notation Calculator (RPN) </p>
***
Lab day -- come in and work on assignments

<br>
### <p style="text-align: right;"> 4/28/16 - Wk5 Day10: Intro to Pandas </p>
***
`import pandas as pd`
`df = pd.read_csv('data/winequality-white.csv')`
  + Here we import a file into a data structure called a dataframe.
`df.head(x)` and `df.tail(x)` give you x number of rows of the front (head) or end (tail) of the dataframe you are reading.
`df.describe` and `df.shape` return summary statistics about the dataframe you're using.  Good overview.
`df.ix[x:y, cols]` accepts both names/labels and integer-based indices to return certain rows/cols; inclusive indices. Start at row *x* and end at row *y*, cols can be actual header names ("Points Allowed") or their respective index ("6" for row 6).
`df.iloc[x:y, cols]` takes *only* integer-based column reference; non-inclusive indices.
`df.loc[x:y, cols]` takes only names of columns.

`%matplotlib inline`
  + this tells matplotlib to plot inline in the browser, so graphs will show up instead of going straight to memory



<br>
### <p style="text-align: right;"> 5/3/16 - Wk6 Day11: Numpy & Pandas continued </p>
***
(https://github.com/talloniv/python-fundamentals/blob/master/week5/day10-intro_pandas/intro_pandas_lecture.ipynb)

`import numpy as np`

It is important to know that lists in Python work as merely references to the location each item in the list.  Each item is likely stored in different locations in memory, and each list can contain mixed data types.  This is advantageous because it allows lists to be a flexible data structure in the Python language, storing mixed types of data and not worrying about their location in memory. It is a very user-friendly approach.  The tradeoff, however, is that having mixed types of data and scattered locations in memory are both structurally inefficient implementations in *any* programming language, Python included.  In short, Python has to make multiple checks for each item in a generic `list` to see if the data type is viable for any given commands.  For example, trying to capitalize a word is an acceptable operation while capitalizing an integer returns an error.  Since a `list` can contain both types of items, any operation on a list has to check each item in the entire list to see if the issued operation is valid for that data type.  As a result, when working with *large* databases, this inefficiency is amplified in some rough proportion to the size of the database.  For a `list` of one million items, Python has to first locate all one million items in memory -- each of which are in different locations -- and then run various 'checks' on each item to ensure they are valid data types for any issued commands.  

Wouldn't it be nice if there was a method in Python that restricted a data structure's items to being stored in the same spot in memory as well as all being of the same data type?  Fortunately, that is precisely what **Numpy** does.  Numpy is what the Pandas framework is built upon.  Numpy functions through the use of 'arrays' that are homogenous in data type and stored together in memory.  Knowing both of these aspects are controlled for allows Numpy to use specific methods to traverse or operate on the arrays in a much faster, (relatively) check-free manner.  Resulting speeds are frequently an order of magnitude faster than they would be performing the same operations on a `list` of the same size.  Therefore, use and proficiency with Numpy is necessary for employing Python in any real data science endeavor.


Columns of the first matrix must match the rows of the second matrix.

+ `flatten` always returns a copy, while `ravel` tries to return a view first if possible.



<br>
### <p style="text-align: right;"> JP Python Notes and Tips </p>
***
For more notes and effective coding tips, see the "JP Python Notes and Tips.md" file
