+++
categories = ["git", "netops"]
date = "2019-07-12"
description = "What to do when you screw up in GIT"
title = "Git Panic Commands"
+++

# What to do when you screw up in GIT

Consult this link: [Oh shit, GIT](https://ohshitgit.com/)

Especially `git reflog` and `git reset HEAD@{index}`.

If needed to restore the entire working directory you can for example delete everything (but not the .git repo) and do `git reset --hard` to rebuild the working directory.