---
author: lime.postback.se
comments: true
date: 2014-04-04 12:04:20+00:00
layout: page
published: false
slug: good-to-remember-about-git
title: Good to remember about Git
wordpress_id: 107
---

**Create a new repo**
Move to the directory where you have your existing project
`
$git init directory
$git add .
$git commit -m 'First commit'
$git rm --cached FilesToTakeAwayFromStagingButNotFromDisk//Typically generated files
$git remote add origin https://github.com/user/Repo.git
$git push
`

**General about the file tracking**
The file tracking works as the following. The sequence below will show how a file is treated all the way to the remote origin/master.
FileA.cpp is changed, one line is added with "//Hello"
`
$git status
`
Will show that one file is untracked
`
$git add FileA.cpp
$git status
`
The file FileA.cpp is added to the staging index.
The file FileA.cpp is changed again, with a new line "//Dude" added after "//Hello".
`
$git status
`
Will show that FileA.cpp is shown in the staged section and in the untracked section.
`
$git diff
`
Will show the difference between "//Dude" and "//Hello"
`
$git diff origin/master
`
Will show the difference between point zero(Before "//Dude" was added) and "//Dude n //Hello".

`
$git commit -m "A really important change"
$git status
`
Will show that there is one commit done.
Still the change where "//Hello" is untracked and will show up.

So where are we?
`
$git diff //Shows the diff between unstaged and staged
$git diff master //Shows the diff between unstaged and master
$git diff origin/master //Shows the diff between unstaged and the remote origin/master
`

**Branching/Merging**
Some information about how to branch and how to merge:

`
$git branch //Show all branches
$git branch add TestBranch //Adds the branch TestBranch
$git checkout TestBranch //Switch to TestBranch
//Changing a file, staging it and committing it to the TestBranch
$git checkout master //Switching to master
$git show-ref //Shows the pointers in the different branches. Note that it points to different for the different branches
$git merge TestBranch //Merge the commit from TestBranch
$git show-ref //Now you can see that the pointers for Master and TestBranch point to the same commit

$git push --all -u //Pushes all branches in the repository to the central repo. This will send the newly created branch from above

`

**Mixed stuff**
Some nice to have commands:
`
$git log --pretty=oneline //Shows the history in a nicer way
$git reset  //Will move the HEAD pointer to the commit specified. Same as undoing a commit.
$git log --stat //To see how much is changed in different files
`

References/Good resources:
- http://git-scm.com/book/en/Git-Branching-Basic-Branching-and-Merging
- https://github.com/Kunena/Kunena-Forum/wiki/Create-a-new-branch-with-git-and-manage-branches
