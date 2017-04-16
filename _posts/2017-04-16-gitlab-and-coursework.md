---
layout: post
title:  "Gitlab and Coursework"
date:   2017-04-16 13:05:14 +0100
categories: git gitlab teaching coursework
author: encima
cover: ""
location: Lancaster, UK
description: 
---


# Using Gitlab (and some Ruby) to automate handing out and submission of software based coursework

Sadly, I am leaving Cardiff University for a more full-time development role, but it does not mean that I am not going to leave a legacy of hacky scripts in a repo behind. This [repo](https://github.com/encima/gitlab_group_work) has been held on the University Gitlab server for a while but I thought it could do with cleaning up.

It is still not close to being finished but, if you are using gitlab as a git server, then this can help your teaching massively. Primarily, the script can create projects for students and groups of students. This is kinda useful and has saved issues with students not giving staff access. For each submission, a group is made to hold the repos and the user of the script is the owner. 

## Setting up:

Once cloned, you need to grab Gitlab token (gained from the `Access Tokens` section of your account) and run

`export GITLAB_TOKEN=<TOKEN>`

There is a `gitlab.yaml` file that contains paths to the students on your module, the endpoint of your gitlab server and an array of staff member names.

Make sure you grab the gitlab and arg-parser gem, using `bundle install`

The student csv file should be of the form:

`id, surname, forename, team_number, project_name`

## Running:

The tool does not run without specifying the action to perform and this can be either `CREATE` or `REMOVE`. Running the tool with no arguments or the `h` flag shows you that you can change things like the group name, module code, repo name and (COMING SOON) a dry run tool to test assignment.

## Removing:

I used this in a timed assessment, creating and assignign the repos as the test started. As I did this, I thought it would be useful to remove access to the repos to stop students cheating after the submission. When I mentioned this as a feature idea, someone pointed out that this would remove access for students that are allowed extra time for a number of reasons.

The tool then became my own sort of timed assessment as I rushed to add features (that I could not test properly without ruining the current test) that revoked access to the tests.

It worked and running a command like:

`ruby cc.rb REMOVE -t INDIVIDUALS -e false`

This removes access forindividuals that do not have extra time. Flip the e flag after an hour (or however long the extra time is) and then they are all removed.

Great, now you just need to grab 120 repos and their urls and clone them down to your machine. NOPE. As access is revoked, each repo is cloned down to a `repos` folder and voila.

## Group work:

Creating and removing repos is all well and good but, at least on our Software Engineering course, group work runs for multiple weeks and needs to be checked up on each week. Cloning and/or looking at each project is a huge time sink and teaching is a time-limited exercise if you want to get any research done.

Well, you can thank [gitinspector](https://github.com/ejwa/gitinspector) and [git-standup](https://github.com/kamranahmedse/git-standup) for making this easier. Sadly, gitinspector cannot auto summarise all branches from the master and git standup does not tell you which branches each commit belongs to. Alone, they have proven to be somewhat useful. Together, they have helped to identify students that are not contributing or lying in sprint reviews, as well as students that may be focussing on one area of development and not spreading their wings.

I have not had time to turn the script into a module (hint hint), but the `group_inspect.rb` tool uses the YAML file for config as well. It clones all projects (runs a fetch on those cloned) and then runs `gitinspector` and `git-standup` on each repository, saving the output into a reports directory at the root of this project. When we met with our groups, this tool could easily show who had done what on each branch, as well as if they were following the right commit patterns.

For me, it has been a life-changer, removing almost all prep time from group worka nd relying on cold, hard facts for claims of no work, too much or something in between. Give it a go and feel free to submit a pull request!

I have thoroughly enjoyed my time at Cardiff University and I hope these hacky tools can help others that want to maximise their teaching time.