---
layout: post
title:  "Git Hooks and Teaching"
date:   2016-09-11 13:05:14 +0100
categories: git git-hooks teaching automation
author: encima
cover: ""
location: Cardiff, UK
description: 
---


# TL;DR Git hooks + tedious tasks = time saved

## What?

I have spent much of this summer break trying to automate as much of my teaching as possible. Most of that involves using Jupyter notebookes and the many amazing plugins (nbgrader, extensions-manager etc) and designing homework so that they can be autograded.

This does not work so well when you are using Python and it especially does not work when you are using something as cumbersome as Android Studio as a teaching tool. This is primarily due to the fact that Gradle changes on a weekly basis and updates come in thick and fast. For development, I am a huge fan of this. However, when I write a set of apps to teach with one year, I would like them to work pretty much the same the following year; this is rarely the case.

Giving students huge code samples is no fun so I have now half-written a set of apps to teach the key concepts of the Android Operating System and how to develop apps within it.

Previously, I had followed this process:
1. Create app and repo
2. Create a `step_one` branch and create a README
3. Put TODO comments in the code and compile them into the README
4. Commit and create a `step_two` branch 
5. Complete the TODOs and add more in
6. Rinse and repeat for all necessary steps

Context switching is a huge issue for me, so I find it easy to get distracted when I am switching between writing Android code and then jumping to the README. Plus, the TODOs are in the code, right? Why should I have to copy them into a README? 

ANSWER: I DON'T!

## Enter Git Hooks

Git hooks are in the `.git/hooks/` folder of your repo and they are executed when certain actions are performed. These can be:

* Commit
* Push 
* Update

And each action has a file (essentially a shell script).

Git is kind enough to create a few sample files for you and you can just rename the necessary file from `pre-commit.sample` to `pre-commit` and edit it. Some trivial actions are added in to the file which you can keep if you like. For the purpose of this blog post, I will be using a script, called `todo.md` and is here https://github.com/charlesthomas/todo.md

### Git Hooks and TODO.MD

If you clone that repo and symlink it to your `/usr/local/bin` like so:
----
ln -s $PWD/todo.md.sh /usr/local/bin/todogen
----

Now, open your `pre-commit` file and add the following line:
----
todogen -e .idea -f README.md -l
----

`-e` allows exclusion patterns and it seems the guys at IntellJ have some todos in the project metadata code.
`-f` allows you to specify an output. I chose README instead of the default TODO because it is auto rendered by most repo hosts
`-l` is not necessary but it ensures that the output file is added to the commit.

==== Running it

Now, just add some TODOs in to your source code files, commit your code and the README will be auto generated with each of the TODOs, much like this:

![image]({{site.url}}/assets/img/todo.png)

TA-DA, the magic of automation that enables you to be as lazy as possible whilst still delivering a reasonable education to students.


