---
layout: post
title: Setup C++ CI Environment with Bamboo and Conan
category: Tech
tags: Tech CI Framework
keywords: Framework of C++ CI
---

# Summary
There is a CI environment in our company, which is setuped with Bamboo, and Stash as version control. It supports Java and C++ development CI. It works fine for Java since Maven can provide very good support on package management and can be integrated with Bamboo conveniently. But for C++, I think that the CI environment can be impoved still.

We develop under Ubuntu 14.04 LTS. It does work for C++ with current CI environment. But there is no package management and version control in the environment. So, we have to deploy dependent libraries and different version of packages mannually. Aka, CI support for C++ is only entry level. I wonder if we can also find a tool like Maven for C++.

Fortunately, I found a package management tool for C++ at last, named [Conan][1]. It supports source code and binary management, provides also same main features of Maven. Even more, it is able to manage version of packages. It also supports a lot of compilers, linkers and platforms. That's AWESOME! Because in our development, we are using g++, CMake and QMake to compile and build our projects. 

Now we have a package mangement tool. But for Conan, there is no plug-in in Bamboo. A good news is that Bamboo provides custom scripts support. So, we can use shell script to execute tasks and output results to Bamboo. 

That's all ideas about new CI environment.

# Tool List
We use a couple of tools to build the environment. Here is the list of them.

| Tool name| Comments|
|-|-|
| Bamboo| This is the CI platform we use|
| Conan| Package management tool. We use it to manage our dependencies and our generated execuables and libraries.|
| GTest| This library will be handled by Conan, obviously, it is used to do unit test.|
| Git(Stash)| We use git as our source version control tool, which is represented as Stash in our environment.|
| CppChecker| An open source static code analysis tool.|
| Shell| scripts we use in Ubuntu to execute CI tasks.|


# CI Workflows
There are 3 kinds of CI workflow in our design: Commit CI, Daily CI and Release CI. There may be high level and further phase CI, like component CI, but at least, so far, we only need these three kinds.

In our git repository, there are 2 default branch: devel & release.

Devel branch is our daily working branch, all changes will be committed into this branch everyday. In this branch, Commit CI & Daily CI will be applied both. Every commit to devel branch causes a Commit CI. And every day in same time, an automatical Daily CI is triggered.

And in release branch, Release CI is applied. Based on git privilege, we make a policy that only release manager can commit into release branch. 

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

## Release CI
Everytime when we need make a release of our product, we make a commit to release branch, then Bamboo executes build, unit tests and upload release files onto Conan.

Here is the sequence of Release CI.
![Sequence of Release CI][4]

# Artificals List
To implement the CI environment, shell scripts are used to execute tasks in CI workflow. Here is the list of artificals in the workflow.
| Name| Description|
|-|-|
| code-analysis.sh| Script to execute static code analysis.|
| build.sh| Script to build project.|
| run-unit-tests.sh| Script to run unit tests.|
| upload.sh| Script to upload generated files to Conan.|



  [1]: https://conan.io/
  [2]: https://www.dropbox.com/s/a2sn3ws945kr8ta/commit-CI.png?raw=1
  [3]: https://www.dropbox.com/s/ev2dsph3m5lp70q/daily-CI.png?raw=1
  [4]: https://www.dropbox.com/s/qzwwvkp0i6mrakt/release-CI.png?raw=1