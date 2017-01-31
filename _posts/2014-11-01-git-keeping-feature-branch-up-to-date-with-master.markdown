---
author: emilw
comments: true
date: 2014-11-01 16:10:06+00:00
#layout: post
slug: git-keeping-feature-branch-up-to-date-with-master
title: GIT keeping feature branch up to date with master
wordpress_id: 189
categories:
- Tips
- Version control
---

When working on a feature i started checking whats the best workflow to keep the feature branch updated against the master branch.
After som trial and error, this is my workflow:

    //Create the branch with a central branch(for keeping the code safe)
    git branch Featurebranch1
    git checkout Featurebranch1
    git push -u origin Featurebranch1
    //Do operations on feature branch and merge with master
    .
    .
    git merge master //Continue until the the feature is ready

    //Get it into main branch, we do not want all small commits from the feature branch, so we put it in one big commit
    git checkout master
    git merge --squash Featurebranch1

    //All files are now ready to be committed to the master branch
    git commit -m "The great feature"

    //Push all of it to central master, and the feature is done
