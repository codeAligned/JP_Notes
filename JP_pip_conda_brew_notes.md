# JP PIP, Conda, and Homebrew Notes
<BR>

`which python` : command that shows you root directory of your default python install  

### Important Clarification about Virtual Environments regarding PIP and Anaconda:
##### Anaconda Virtual Env
When using a virtual env ("venv") in Anaconda (`conda create -n [venv name] python=X anaconda`), if you wish to install or modify packages in this venv, you must activate it first (`source activate [venv name]`) and then using `conda` *only* can you modify the packages in this environment.  

**Note**: using the command above to create the venv with `anaconda` appended to the end will create a new venv with the full Anaconda distribution already installed.  From here, you can modify the version of a given package.  This approach creates a venv with more packages than are often required, but simplifies the creation and package managing process for the most part.  

##### PIP Virtual Env ("Vanilla")
The below quote is from the [Conda cheatsheet](https://conda.io/docs/_downloads/conda-cheatsheet.pdf), being in an active venv and installing a package via `pip` does indeed install that package only into the active venv and not into your global Python installation.  (This is a good thing).

> Install a package directly from PyPI into the current active environment using pip.  
Ex: "pip install new_module" installs new_module into current active environment only.




For either venv, when done you must deactivate the env, which returns you to your default global Python installation.
+ `deactivate [venv name]` (vanilla)
+ `source deactivate [venv name]` (conda)

To delete a venv, you can remove its folder.  For a conda venv, you can use a conda command to cleanly remove it as well.
+ `rm -rf [venv name]` (or visually in the Finder)
+ `conda remove -n [venv name] --all`


See more about using [PIP venv](http://docs.python-guide.org/en/latest/dev/virtualenvs/) and [Anaconda venv](https://conda.io/docs/using/envs.html)



### Conda commands
Command | Action
--------|--------
`conda list`

### pip commands
Command | Action
--------|--------
`python --version` | gives current version of python
`pip install [package]==X.x.x` | force install a specific version of a package, downgrading if necessary.
`pip install [package] --upgrade` | upgrades a package, overwriting as needed


Common packages used (beware of cross-dependencies!):  
numpy **|** pandas **|** matplotlib **|** flask **|** scipy **|** scikit-learn **|** scikit-image **|** seaborn **|** selenium **|** six **|** SQLAlchemy **|**  
Werkzeug **|** numexpr **|** pymc **|** psql **|** psutil **|** psycopg2 **|** pymongo **|** bokeh **|** beautifulsoup4 **|** Flask **|**  
ghp-import (*github pages* for *Pelican* static site publishing) **|** ipython **|** Jinja2 **|** jupyter **|** mrjob **|** nbformat

### Current list of virtual environments I have set up:
`conda info --envs`
Environment | Python Version | Purpose
------------|---------|---------




### Switch between two Python environments (2.7 and 3.x) in Anaconda:
My default installation is (currently) Python 2.7.
I installed Python 3 and named it `python3`, which is the name we will use to utilize that environment.  Type the following commands into the terminal to start or stop a Python 3 environment.  During DSI, we installed a GraphLab environment called `gl-env`.
(Some of these commands may take `--` instead of `-` -- I can't tell yet)
(All commands are one-line commands, regardless of how they are line-wrapped below).
Command | Effect
--------|--------
`conda env -help` | List of all Conda commands
`conda create -name [your_env_name] python=X.x anaconda` | Creates the virtual env. <br> For Python version, can use general version [`2` or `3`] or specify point version [e.g. `2.7` or `3.5`, etc.]
`source activate python3` | Activates virtual env
`source deactivate python3` | Deactivates virtual env
`conda info -envs` | Verify current environment
`conda env list` | Lists all virtual env
`conda create -name [new_name] -clone [env_to_clone]` | clone an existing env with a new name.
`conda remove -name [env_name] -all` | removes an env. Verify its removal with `conda info -envs`

TIP: Many frequently used options after two dashes (--) can be abbreviated with just a dash and the first letter. So --name and -n options are the same and --envs and -e are the same. See conda --help or conda -h for a list of abbreviations.

**Share an environment**
You may want to share your environment with another person, for example, so they can re-create a test that you have done. To allow them to quickly reproduce your environment, with all of its packages and versions, you can give them a copy of your environment.yml file.

[This guide](https://uoa-eresearch.github.io/eresearch-cookbook/recipe/2014/11/20/conda/) is the best nano guide on creating a virtual env in Conda.

See [this page](https://conda.io/docs/using/envs.html) for more info on advanced environment handling, such as exporting, creating env by hand, variable scripts, and building identical conda envs.

Also see Anaconda's good [write up](http://conda.pydata.org/docs/py2or3.html) for a short guide on using environments to switch between different versions of Python if needed:  


##### Installing Packages with Anaconda
Anaconda Python and Packages

We use the Anaconda scientific python stack which is just a vanilla version of Python 2.7 along with all the packages that a data scientist would need, including NumPy, SciPy, SciKit-Learn, Pandas, and matplotlib. Anaconda manages the Python environment for us. If you need to install other Python packages (unlikely), do so with the conda command-line utility (i.e. `conda install some-cool-package`). Use conda list to see what's installed.

to update itself `conda update conda`
<BR><BR><BR>





## Homebrew Package Manager
***
<BR>

We will commonly use **brew** to install packages which are not available through **pip**.  **Homebrew** is built with Ruby and Git, so it supports versioning in a sense and supports installing packages that are *not* Python-specific, unlike **pip** or **conda** (though technically I believe **conda** does support some non-Python packages, it is certainly Python-centric).  Homebrew installs packages to their own directory and then symlinks their files into `/usr/local`.  Full documentation links are below.  Homebrew is a package manager, and Homebrew **Cask** is a full-application manager (Google Chrome, for example)

### Homebrew Installation
Install **homebrew** and brew **cask**:
  + Install homebrew. Instructions [here](http://brew.sh/)
    + ...Or paste the following into your terminal to install:  
    `/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"`
  + Install brew cask with: `brew install caskroom/cask/brew-cask`
  + If you already have them, update: `brew update && brew upgrade brew-cask`
<BR>


### Homebrew Commands
##### Common Commands
Command	| Description
--------|------------
`brew install [package]`	| Install a package
`brew upgrade [package]`	| Upgrade a package; leave blank to upgrade all (caution)
`brew unlink [package]`	| Unlink
`brew link [package]`	| Link
`brew switch [package] 2.5.0`	| Change versions
`brew list --versions [package]`	| See what versions you have
`brew info [package]`	| List versions, caveats, etc
`brew cleanup [package]`	| Remove old versions, include `-n` to see which packages would be removed
`brew edit [package]`	| Edit this formula
`brew home [package]`	| Open homepage
`brew pin [package]`  | Stop from upgrading/updating
`brew unpin [package]`  | Allow upgrading/updating
<BR>

##### Global Commands
Command	| Description
--------|------------
`brew update`	| Update brew
`brew list`	| List installed
`brew outdated`	| What’s due for upgrades?
<BR>

##### The Rest
Command	| Description
--------|------------
`man brew` | Display Homebrew User Manual
`brew --cache`	| Print path to Homebrew’s download cache (usually `~/Library/Caches/Homebrew`)
`brew --cellar`	| Print path to Homebrew’s Cellar (usually `/usr/local/Cellar`)
`brew --config`	| Print system configuration info
`brew --env`	| Print Homebrew’s environment
`brew --prefix`	| Print path to Homebrew’s prefix (usually `/usr/local`)
`brew --prefix [formula]`	| Print where formula is installed
`brew audit`	| Audit all formulae for common code and style issues
`brew cleanup [formula]`	| Remove older versions from the Cellar for all (or specific) formulae1
`brew create [url]`	| Generate formula for downloadable file at url and open it in `$HOMEBREW_EDITOR` or `$EDITOR2`
`brew create [tarball-url] --cache`	| Generate formula (including MD5), then download the tarball
`brew create --fink [formula]`	| Open Fink’s search page in your browser, so you can see how they do formula
`brew create --macports [formula]`	| Open MacPorts’ search page in your browser, so you can see how they do formula
`brew deps [formula]`	| List dependencies for formula
`brew doctor`	| Check your Homebrew installation for common issues
`brew edit`	| Open all of Homebrew for editing in TextMate
`brew edit [formula]`	| Open [formula] in `$HOMEBREW_EDITOR` or `$EDITOR`
`brew fetch --force -v --HEAD [formula]`	| Download source package for formula; for tarballs, also prints MD5, SHA1, and SHA256 checksums
`brew home`	| Open Homebrew’s homepage in your browser
`brew home [formula]`	| Opens formula’s homepage in your browser
`brew info`	| Print summary of installed packages
`brew info [formula]`	| Print info for formula (regardless of whether formula is installed)
`brew info --github [formula]`	| Open Github’s History page for formula in your browser
`brew install [formula]`	| Install formula
`brew install --HEAD [formula]`	| Install the HEAD version of formula (if its formula defines HEAD)
`brew install --force --HEAD [formula]`	| Install a newer HEAD version of formula (if its formula defines HEAD)
`brew link [formula]`	| Symlink all installed files for formula into the Homebrew prefix3
`brew list [formula]`	| List all installed files for formula (or all installed formulae with no arguments )
`brew options [formula]`	| Display install options specific to formula
`brew outdated`	| List formulae that have an updated version available (`brew install formula` will install the newer version)
`brew prune`	| Remove dead symlinks from Homebrew’s prefix4
`brew remove [formula]`	| Uninstall formula
`brew search`	| List **all** available formula
`brew search [formula]`	| Search for formula in all available formulae
`brew search /[formula]/`	| Search for `/formula/` (as regex) in all available formulae
`brew test [formula]`	| If formula defines a test, run it
`brew unlink [formula]`	| Unsymlink formula from Homebrew’s prefix
`brew update`	| Update formulae and Homebrew itself
`brew upgrade`	|Install newer versions of outdated packages
`brew upgrade [formula]`	| Install newer version of formula
`brew versions [formula]`	| List previous versions of formulae, along with a command to checkout each version  

You can update outdated packages with any of the following:
+ `brew upgrade`
+ `brew install 'brew outdated'`
+ `brew outdated | xargs brew install`  

You can uninstall Homebrew entirely with the following:  
`ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/uninstall)"`  
Download the [uninstall script](https://raw.githubusercontent.com/Homebrew/install/master/uninstall) and run `./uninstall --help` to view more uninstall options.  

If you do not uninstall all of the versions that Homebrew has installed, Homebrew will continue to attempt to install the newest version it knows about when you do (`brew upgrade --all`). This can be surprising.

To remove a formula entirely, you may do:  
`brew uninstall formula_name --force`  
Be careful as this is a destructive operation.




*1.* To delete a specific version, just go to the folder in the Cellar and `rm -rf `it; alternatively, drag it to the trash in Finder.

*2.* Homebrew tries to guess the formula name and version. If it fails, you’ll have to make your own template. I suggest copying `wget`’s.

*3.* Symlinking is automatically performed when installing formulae. It’s useful for DIY installation, or swapping out versions of a package that has multiple installs.

*4.* This is generally not needed. However, it can be useful if you are doing DIY installations.
<BR><BR>



### Cask Commands
Command | Action
--------|-------
`brew cask search` | List all available casks
`brew cask list` | List installed casks
`brew cask search <cask-name>` | Search a given cask (e.g. `chrome`)
`brew cask install <cask-name>` | Install a given cask (e.g. `google-chrome`)
`brew cask uninstall <cask-name>` | Uninstall a given cask
`brew cask info <cask-name>` | Display information of a given cask
`brew cask home <cask-name>` | Open homepage of a given cask (this will open the application's homepage)
`brew update` | Update (get the latest casks but does not upgrade Homebrew Cask itself, nor your applications)
`brew upgrade brew-cask` | Upgrade Homebrew Cask itself
`brew cask checklinks` | Check for bad cask links:
`brew cask alfred` | Modify Alfred's scope to include the Homebrew Cask apps:
`brew cask create <cask-name>` | Create a cask of the given name and open it in an editor:
`brew cask edit <cask-name>` | Edit a given cask
`brew cask audit` | Verify install-ability of all available casks
`brew cask audit <cask-name>` | Verify install-ability of a given cask:
<BR>


### Homebrew & Cask Docs
+ [Homebrew home page](brew.sh)
+ [Cask home page](http://gillesfabio.github.io/homebrew-cask-homepage/)
+ All [Official Homebrew docs](http://docs.brew.sh/)
+ [FAQ](http://docs.brew.sh/FAQ.html)
+ [Tips and Tricks](http://docs.brew.sh/Tips-N'-Tricks.html)
+ [Homebrew Github Repo](https://github.com/Homebrew/brew)
+ See [Common Issues](http://docs.brew.sh/Common-Issues.html) if you are having problems, including after upgrading Mac OSX:
  + Upgrading macOS can cause errors like the following:
    + `dyld: Library not loaded: /usr/local/opt/icu4c/lib/libicui18n.54.dylib`
    + `configure: error: Cannot find libz`  

    Following an macOS upgrade it may be necessary to reinstall the Xcode Command Line Tools and `brew upgrade` all installed formula:  
    ```bash
    xcode-select --install
    brew upgrade
    ```

+ Full [installation instructions](https://github.com/Homebrew/brew/blob/master/docs/Installation.md#installation), including a note about installing in a unrecommended directory.  If you follow the installation instructions above, you should be fine and have no need for this.
> However do yourself a favor and install to /usr/local. Some things may not build when installed elsewhere. One of the reasons Homebrew just works relative to the competition is because we recommend installing to /usr/local. Pick another prefix at your peril!

+



<BR><BR><BR>

## PIP Package Manager
***
<BR>

### PyPI - the Python Package Index
Using **PIP**, packages are usually installed from the Python Package Index (PyPI -- said "Pie-Pee-Eye" or "Pie-Pie").  PyPI is a repository of software for the Python programming language.  To install a package from the Python Package Index, just follow the instructions below.  
<BR>

### PIP Installation
Installing PIP is easy and if you're running Linux, its usually already installed.

If it's not installed or if the current version is outdated, you can use the package manager to install or update it.  
On Debian and Ubuntu: `$ sudo apt-get install python-pip`  
On Fedora:  `$ sudo yum install python-pip`  
If you are using Mac, you can simply install it through **easy_install**:  `sudo easy_install pip`
<BR>

### PIP Commands
Just typing `pip` in your terminal brings up the help menu. The most common usage for **pip** is to install, upgrade or uninstall a package.

Command | Action
--------|-------
`install [package]`	| Install packages.
`uninstall [package]` | Uninstall packages.
`freeze [package]` (optional) | Output installed packages in requirements format.
`list` | List installed packages.
`show [package]` | Show information about installed packages (can list multiple packages, separated by a space).
`search [package]` | Search PyPI for packages.
`zip [packages]` | Zip individual packages.
`unzip [packages]` | Unzip individual packages.
`bundle` | Create pybundles.
`help` | Show help for commands.

Below is a simple example of how to use **pip** to search for a package named **Flask**, install it, inspect it, and uninstall it.
<BR>

1. Search - `pip search Flask`
2. Install - `pip install Flask`
3. Inspect - `pip show Flask`  
4. Uninstall - `pip uninstall Flask` (if needed)

**JP Note:**  
**Flask** and many, many other packages are included in the Anaconda distribution of Python.  So there is no need to manage them from **pip** in most cases.  If you use virtual environments, it is recommended to use **conda** virtual environments and to manage packages within those venvs via **conda** as much as possible.  However, you can use **pip** to install a given package into a specific Anaconda venv just as you normally would any venv.
<BR>
