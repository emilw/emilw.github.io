---
author: emilw
comments: true
date: 2014-04-23 19:23:24+00:00
#layout: post
slug: c-style-guide-lines
title: C++ style guide lines
wordpress_id: 137
commentIssueId: 7
categories:
- C++
---

Google have nice C++ style guide lines:

[http://google-styleguide.googlecode.com/svn/trunk/cppguide.xml#TODO_Comments](http://google-styleguide.googlecode.com/svn/trunk/cppguide.xml)

and a coresponding cpplint that checks the style on the fly. The cpplint is a python script.

I have tested the cpplint incorporated in a build script in xcode(with xcodebuild) and it works great, all issues are shown directly in the IDE.
More on the xcode build [here](http://lime.postback.se/2014/04/09/shared-staticuniversal-library-management-in-xcode/).



cpplint can be downloaded from: [http://google-styleguide.googlecode.com/svn/trunk/cpplint/cpplint.py](http://google-styleguide.googlecode.com/svn/trunk/cpplint/cpplint.py)
