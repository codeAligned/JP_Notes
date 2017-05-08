# JP Github Notes

##### Github Cloning and Pushing Commands
1. Find master repo on Github
2. fork to my profile
3. clone to my local by copying the 'clone' link *on my Github profile's fork* then using `git clone <link>` in the desired directory in terminal.
4. make changes to files / do work
5. add changes to your staging area: `git add <filename>` in <filename> directory
6. commit the changed files with summary of changes (extremely bad practice to omit the summary -- always include a summary): `git commit -m, "<change summary>"`
7. push committed files back to your repo: `git push origin master` (Note: master refers to the repo from which you cloned originally; in this case, your personal repo)
8. if needed, such as for assessments, create a pull request from zipfian by going to zipfian's repo that you forked from in \#2, and choosing 'create new pull request' and following the steps there

* Note it is possible to simply clone a repo from a master, like zipfian, without forking to your own profile.  Do this if that repo is going to be updated regularly by the creator, such as a assignment solutions repo which will have new solutions added daily or weekly.  Simply clone to your desired directory and then update your local copy once changes are made in the zipfian master: `git pull` in the directory of the cloned repo.  This pull request will simply match your local files to the zipfian master, which is all we want since we won't be branching anything in a simple solutions repo.



`git branch <name>` - creates a new branch
`git checkout <branch name>` - switches to a specified branch


`git push origin <branch name>` - push your files to YOUR branch, NOT master

Then switch into MASTER branch and  `git pull` to make sure you've got all the updated files from the master branch.  Anytime you go into a new branch, just `pull` the branch.

THEN go back into YOUR branch and  `git merge` into the master, once you've got files that you want to merge.

It is possible to simply keep working in your branch for a while, and merge after much work has been done, but never ever `merge` into master branch without doing a `pull` first.  Always, always `pull` from MASTER before merging into it.  Otherwise you get big merge conflicts because files in different branches are not matching up.  Bad.



**git ignore**
in the main level folder of each repo is a `.gitignore` file.
In it, list the files or file types in that folder that you want github to ignore when syncing with your repo.  For example, if you have a big database or a file with your passwords in it, you would list them on a new line each, such as: `my_database.csv` or `my_passwords.json`  and they would be ignored, not uploaded to github.
If you wish to simply ignore all occurrences of a file type, you simply prefix the file type itself with an asterisk, such as: `*.csv` or `*.json` to ignore all csv or json files in that repo.
If you want to ignore a specific folder, simply enter the directory into the `.gitignore` file.  If your directory structure was *Top_Level* -> *Data_A*, *Data_B* -> *Script_A*, *Script_B* and your master `.git` folder is in *Top_Level*, you can ignore all occurrences of folder named *Data_B* and its own subfolders by saving `Data_B/` into the `.gitignore` file.  If you want to ignore just that folder but not its subfolders (I think...) you can just enter that folder into the `.gitignore` file, *Data_B*.

Please note that if you delete any file or folder on the Github web interface, it will be deleted from your local once you do a `git pull`.  Your local repo and the corresponding master branch on github are always going to mirror each other.  Hence, branching in git.




#### Merging my Fork with the Original Upstream Repo
Once in the local repo *of my fork* in terminal:
`git remote add upstream [url for the upstream original repo I am syncing from]`
`git pull upstream master`




#### Github Commands
Command | Action
--------|--------
