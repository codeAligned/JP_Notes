to run test files use: `nosetests <filename>` in the terminal at correct dir (test file and script should be in same dir)


When trying to import a module in a different directory than the script you're trying to run, navigate to the directory of the module itself in the terminal and see if there is a '\__init__.py' file.  If there isn't, execute `touch __init__.py` to create it.  Then in your python script you can drill down into the correct directory (only down from the script's directory) in your import statement by doing a dot-command.  Example: your script file is called "py_script" and is in `~/main` dir and your module is called "py_mod" and is in `~/main/data` dir, you would go into the `data` dir in terminal, issue `touch __init__.py` command, and then use the following import command atop your python script: `from data.py_mod import [class]` or whatever you need to import, or `*`.




##### modules
Common modules and imports
from __future__ import division #makes default div a float not int
import numpy as np
import pandas as pd
import matplotlib.pyplot as plt
import seaborn as ... #advanced plotting
from sklearn.cross_validation import train_test_split
from sklearn.utils import resample  #used for bootstrapping
