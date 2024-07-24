# Git and GitHub #

Type in Git to the Windows start explorer to open up Git Bash, an application where you run all the commands

In the Git Bash command line you navigate to a folder that you want to initialise. To the intialise a git repository on your local machine`
```
git init
```

If this didn't work it is probably because your user email and settings are not configured. To configure email and name you type:
```
git congif --global user.email "jonah.trenouth@cognitive.business'
git congif --global user.name "jotren'
```
Which are my git credentials. Once this is done here are the main commands and what they do

__Add files to repository__
```
git add . = Add all files to repository

git add my_example.py = Add specific file to repository
```
_You have to add files before you commit_. Files are added you need to commit then to save

__Commit files to repository__
```
git commit = Commit the files in repository into master file

git commit -m 'Insert message here' = Commit the files in repository into master file with comments
```
__Git status checks__
```
git status = check status of files
```
__Move between branches__
```
git branch name = Create a new branch called 'name'

git checkout -b new = Create a new branch called new

git checkout master = move to master branch

git checkout new = move to new branch
```
__Merge branches together__
```
git merge master = when you are in the new branch this command will merge the changes from new into the master branch
```

Now in order to create a remote repository you need to go on GitHub and great one. You can do that at this [link](https://github.com/jotren?tab=repositories)

Once you've created the new remote repository then need to link it with your local one. This is done with

__Link local repository with remote__
```
git remote add origin https://github.coim/jotren/vessel-tracker.git
```

__Push a file to the remote repository__
```
git push -u origin master 
```
Master being the name of the branch and -u meaning save these settings.

## General Rules of Git ##

1) Never upload data, only programs
2) Save all programs in the 'projects' folder on the local machine
3) Save all the relevant input files the the same folder as the program but do not commit or push to repository
4) When we are sharing code we seperately send some 'test' data that the person can then use. Git is not a data sharing service

# The Process #

If you want to work on multiple branches this is the process:

1) Save the file, add and commit your files to the master branch
2) Create a new branch and save, add and commit there
3) When you are happy with changes in the branch you save, add and commit everytime
4) If your code is working you then commit to the master

# The New Process #

When you create a repository, you then type the commands from github into the VS code terminal in your directory. We then go on the source control tab in VS Code. 

We then save the files, create a message and then push the commit button. 