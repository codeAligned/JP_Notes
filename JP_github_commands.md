# JP Github Notes

##### Github Forking, Cloning, and Pushing Commands
1. Find master repo on Github
2. fork to my profile
3. clone to my local by copying the 'clone' link *on my Github profile's fork* then using `git clone <link>` in the desired directory in terminal.
4. make changes to files / do work
5. add changes to your staging area: `git add <filename>` in <filename> directory (`git add .` adds _all_ files)
6. commit the changed files with summary of changes (this is required; commit will be rejected without): `git commit -m, "<change summary>"`
7. push committed files back to your repo: `git push origin master` (Note: master refers to the repo from which you cloned originally; in this case, your personal repo)
8. if needed, such as for assessments, create a pull request from zipfian by going to zipfian's repo that you forked from in \#2, and choosing 'create new pull request' and following the steps there

* Note it is possible to simply clone a repo from a master, like zipfian, without forking to your own profile.  Do this if that repo is going to be updated regularly by the creator, such as a assignment solutions repo which will have new solutions added daily or weekly.  Simply clone to your desired directory and then update your local copy once changes are made in the zipfian master: `git pull` in the directory of the cloned repo.  This pull request will simply match your local files to the zipfian master, which is all we want since we won't be branching anything in a simple solutions repo.  Also, if you have _added_ files to your local, this will either attempt to upload them to git or cause an error as the two are not synced.



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





### Create Repo From Existing Local Directory
1. Go to github, create a new repo, name it, copy URL
  + Note: create this repo _without_ a README, gitignore, or license to avoid errors.  Add these after first commit.
2. Navigate to local directory in terminal
3. `git init`
4. `git remote add origin <URL>` adds the origin (the github repo) as the destination for our local folder (paste URL)
5. `git remote -v` to verify the URLs for the repo are correct
6. `git add .` to add _all_ files in this folder
7. `git commit -m "first upload"` to commit those files to the git head
8. `git push origin master` to push all committed files up to the actual repo. yay!  

__Note 1:__ if you do create the repo on Github with a readme or license in it, or you are trying to sync a new local folder with an existing github repo, you will have to first download all the stuff (e.g. README, license, other files) from the repo to your local folder before you can push any new files from your local back up to your repo.  This merging keeps the two locations, local and repo, in sync.

__Note 2:__ `git remote set-url origin <URL>` is a similar command to \#4, `git remote add origin <URL>`, but it is used once a local folder has already had its origin added and you now need to change it.  You just set the URL to the new desired URL.








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
