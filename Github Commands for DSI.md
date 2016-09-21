Github commands
1. Find master repo on zipfian
2. fork to my profile
3. clone to my hd by copying 'clone' link then using `git clone <link>` in the desired directory
4. make changes to files / do work
5. add changes to your staging area: `git add <filename>` in <filename> directory
6. commit the changed files with summary of changes: `git commit -, "<change summary>"`
7. push committed files back to your repo: `git push origin master` (Note: master refers to the repo from which you cloned originally; in this case, your personal repo)
8. if needed, such as for assessments, create a pull request from zipfian by going to zipfian's repo that you forked from in \#2, and choosing 'create new pull request' and following the steps there

* Note it is possible to simply clone a repo from a master, like zipfian, without forking to your own profile.  Do this if that repo is going to be updated regularly by the creator, such as a assignment solutions repo which will have new solutions added daily or weekly.  Simply clone to your desired directory and then update your local copy once changes are made in the zipfian master: `git pull` in the directory of the cloned repo.  This pull request will simply match your local files to the zipfian master, which is all we want since we won't be branching anything in a simple solutions repo.
