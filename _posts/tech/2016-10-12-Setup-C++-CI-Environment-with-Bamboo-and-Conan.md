---
layout: post
title: Setup C++ CI Environment with Bamboo and Conan
category: Tech
tags: Tech CI Framework
keywords: Framework of C++ CI
---

# Summary
There is a CI environment in our company, which is setuped with Bamboo, and Stash as version control. It supports Java and C++ development CI. It works fine for Java since Maven can provide very good support on package management and can be integrated with Bamboo conveniently. But for C++, I think that the CI environment can be impoved still.

It does work for C++ with current CI environment. But there is no package management and version control in the environment. So, we have to deploy dependent libraries and different version of packages mannually. Aka, CI support for C++ is only entry level. I wonder if we can also find a tool like Maven for C++.

Fortunately, I found a package management tool for C++ at last, named [Conan][1]. It supports source code and binary management, provides also same main features of Maven. Even more, it is able to manage version of packages. It also supports a lot of compilers, linkers and platforms. That's AWESOME! Because in our development, we are using g++, CMake and QMake to compile and build our projects. 

Now we have a package mangement tool. But for Conan, there is no plug-in in Bamboo. A good news is that Bamboo provides custom scripts support. So, we can use shell script to execute tasks and output results to Bamboo. 

That's all ideas about new CI environment.

# CI Workflows
There are 2 kinds of CI in our design: Commit CI & Daily CI. There may be high level and further phase CI, like component CI, but at least, so far, we only need these two kinds.

## Commit CI
Every time, when a commit is pushed, this CI is triggered. It pulls code from Stash, download packages needed from Conan, and then build and run unit tests.

Here is the sequence of Commit CI.
![Sequence of Commit CI][2]

## Daily CI
Daily CI is almost same as Commit CI, except two points:

 - Compared to being triggered by commit, Daily CI is triggered fixed time every day.
 - If everything is sucessful in CI, the generated binary will be uploaded to Conan as a new version of develop branch.
 
Here is the sequence of Daily CI.
![Sequence of Daily CI][3]

  [1]: https://conan.io/
  [2]: https://www.dropbox.com/s/n4xudi74x5othwp/commit_ci.png?raw=1
  [3]: https://www.dropbox.com/s/97avl7ernkc31ri/daily_ci.png?raw=1