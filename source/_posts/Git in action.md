---
title: Git in aciton
tags: [think,dev]
---
This paper is about how to set up git repository and how to mange porject with git


## Set up git project

Let say, I am making a **"helloworld"** project and set git to manage it.

Below show how to use git to manage this **"helloworld"** project

- Make a dir and get into it 

```shell
mkdir helloworld
cd helloworld
```

-  Initialize git and project

```shell
# Set a README.md
echo "# helloworld" >> README.md

# Initial git
git init

# Add file to git
git add README.md

# Commit the change
git commit README.md -m "first commit"

# Add remote server (the git server need to set first) 
git remote add origin https://github.com/bill/helloworld.git

# Push the commit to server
git push origin master
```



## Daily update

In daily workflow, we must update the project first then modify the content, after day of work, we need to push all the commit to server

- Update the project

```shell
git pull
```

Then we can modify the file and happy coding, let say, if we modify a.py, we need to commit and push the result

```shell
# -a mean all, commit all the change
git commit -a -m "modify a.py"
git push origin master
```



## Branch management

- For coorperated project, it's recommended to make the project into many branches based on different functions, then merge the branches after all the function finished


```shell
# Make a new branch based on current code
git checkout -b feature_x

# If you want to switch to master branch
git checkout master

# Push the feature_x branch to server
git push origin feature_x
```

- After finishing all the coding, we need to merge feature_x to master


```shell
# Get into master
git checkout master

# Merge feature_x to master
git merge feature_x

# If all is done, remove feature_x
git branch -d feature_x

# Be careful when mergeing two branches, code may get conflicted, need to modify the conflicted files and add the file one more time,E.g. the a.py get conflcted, after modifying , need to add it again
git add a.py

```

- After finish merging all the functions,  it is time to  set a deliver tag for celebration , we can do with by tagging current version

```shell
# First, get the verion num for current version 
git log

# Then, we will see current version's num is '1b2cc54'

# Tag current verion with 1.0.0
git tag 1.0.0 1b2cc54
```



## Time machine

- Some time, we may make some mistakes in coding, if you want to roll back to version before last push, you can run command

```shell
git checkout --<filename>
```

- Or if you want current branch reset and set as another branch, you can run this command

```shell
git fetch orign
git reset --head origin/(new branch name)
```



## Git tools

There are many  tricks  for making git experience better

- Use git with interation mode

```shell
git add -i
```

- Use git in color mode

```shell
git config color.ui true
```

- Make git log into one line

```shell
git config fomat.pretty oneline
```

- Use GUI of git

```shell
gitk
```

- Analyse the log from git

```shell
# Get log info
git log

# See commite of a person name 'bob
git log --author=bob

# One info one line
git log --pretty=oneline

# See the branch with a tree
git log --graph --oneline --decorate --all

# See what files chagne
git log --name-status

# Get help 
git log --help
```



## Reference

[git - the simple guide](<http://rogerdudler.github.io/git-guide/>)

