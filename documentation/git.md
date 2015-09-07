---
layout: page
title: "Git"
excerpt: "Git"
tags: ["documentation"]
context: assign
subcontext: doc
image:
  feature: "https://dgosxlrnzhofi.cloudfront.net/blogs/317/featured_images/what-is-a-bad-culture-fit.png?1438216788"
---

# Setup

We use the Git version control system with [github.com](https://github.com) to manage submissions and grade assignments. If you don't already have a GitHub account, [sign up](https://github.com/signup) first!

Once you have a GitHub account, set up Git on your computer by following [this guide](https://help.github.com/articles/set-up-git/). Be sure to select your operating system at the top of that guide. Also open the `Input` informational box for information on how to use git from the command-line, because we will be using that in this guide.

We will explain the steps needed to work on and submit assignments in this guide. To learn the basics of git, read [git - the simple guide](http://rogerdudler.github.io/git-guide/) and [try out the Git command-line](https://try.github.io/). If you'd like to learn more, [have a look at these resources](https://help.github.com/articles/good-resources-for-learning-git-and-github/).


# Structure

Let's look at the repository structure first.

On GitHub, we will create a private git repository for you, which is owned by us, and is only visible to you and the compiler construction team. This repository will host assignment templates and your submissions. Note that you do **not** have write access to this repository, you can only read from it and submit assignments to it. This is to ensure that you cannot mess with submitted assignments after the deadline.

You create your own private Git repository by making a *fork* of our private repository. This repository will be used by you to work on assignments. You have write access to this repository since you create it. Our repository on GitHub is called *upstream* in git terms, because your repository is a fork of it. Your repository on GitHub is called *origin*. If you'd like to learn more about forking, [read this GitHub guide](https://help.github.com/articles/fork-a-repo/).


# Getting started

First, find your private repository in the [TUDelft-IN4303-2015](https://github.com/orgs/TUDelft-IN4303-2015) organization on GitHub, it should be called `student-id` where `id` is your student id. Remember your id, as it is used later. Go to the repository and press the fork button to fork the repository into your account.

![Fork](https://camo.githubusercontent.com/a67a2699e627d522bfb0da1537a79a5546ded10c/68747470733a2f2f6769746875622d696d616765732e73332e616d617a6f6e6177732e636f6d2f68656c702f7265706f7369746f72792f666f726b5f627574746f6e2e6a7067)

To actually do work in the Git repository, you need to make a *local* clone of the repository on your computer. You can find the URL needed to clone on the bottom right of the page, be sure to use `HTTPS`.

![Clone URL](/images/clone_url.png)

Open up the command line and make a local clone with your URL:

```shell
git clone https://github.com/username/student-id.git
```

Now cd into the local clone and confirm that it works:

```shell
cd student-id
git status
```

which should output something like:

```shell
On branch master
Your branch is up-to-date with 'origin/master'.
nothing to commit, working directory clean
```

To make a connection with the upstream repository we forked off, we need to add another remote to the local repository. Use the following command with your id:

```shell
git remote add upstream https://github.com/tudelft-in4303-2015/student-id.git
```

Confirm that it works by fetching commits from upstream:

```shell
git fetch upstream
```

which should output something like:

```shell
From https://github.com/tudelft-in4303-2015/student-id.git
 * [new branch]      master     -> upstream/master
```

Your local repository is set up now! Follow the steps below to work on an assignment.


# Workflow

## Starting an assignment

Every assignment is put into its own branch, named `assignment1`, `assignment2`, etc.
The correct assignment branch must be checked out in your local Git repository to be able to work on it.

Some assignments have templates which we provide to you, these require special steps to check out.
If the assignments asks you to check out a template, which for example `assignment1` does, use the following steps:

```shell
git fetch upstream
git checkout -b assignment1 upstream/assignment1
git push -u origin assignment1
```

If the assignment asks you to continue from the previous assignment, which for example `assignment2` does, use the following steps instead:

```
git checkout -b assignment2
git push -u origin assignment2
```

Now you have the assignment branch checked out in your local repository and can start working.


## Saving work

Whenever you have changes that you'd like to save, such as after getting (a part of) the assignment working, you need to add, commit, and push your changes:

```
git add --all
git commit -m "Message describing your changes"
git push
```


## Submitting an assignment

[Pull requests](https://help.github.com/articles/using-pull-requests/) are used to submit an assignment in your *origin* repository to the *upstream* repository.

Make sure you've pushed all your changes first, then go to your *origin* repository (your fork) on GitHub.
If you've recently pushed changes, there should be a button with `Compare & pull request` for the right branch, click that.

![Compare & pull request](/images/create_pullrequest.png)

If there is no button there, go to the `Pull requests` tab, press `New pull request`, and set both branches to the correct branch for the assignment.

If all is well, it should display `Able to merge. These branches can be automatically merged`, and you can press the `Create pull request` button to submit your assignment. If not, check the troubleshooting section.

![Pull request](/images/pullrequest.png)

We will grade your assignment and post the results on the pull requests, so check back later.


## Switching to another assignment

If you'd like to work on an another assignment, for example to fix things for a new submission, you can just check out the branch for that assignment.
Be sure to push any changes to your current branch first, then check out the branch for the assignment you wish to switch to:

```shell
git checkout assignment1
```

## Pulling in changes from upstream

If there's something wrong in the template for an assignment, we fix it in the *upstream* repository, and you have to merge in those changes.
Use the following commands to merge in changes (use the correct branch!):

```shell
git fetch upstream
git merge upstream/assignment1
git push
```

In most cases, Git will automatically merge in any changes, but sometimes conflicts can occur. See [Resolving a merge conflict](https://help.github.com/articles/resolving-a-merge-conflict-from-the-command-line/) on how to resolve conflicts.


# Git GUI clients

This guide uses command-line Git commands, but if you'd rather use a GUI, use [SourceTree](https://www.sourcetreeapp.com/) or [GitHub desktop](https://desktop.github.com/).


# Troubleshooting

## Cannot push, no access/rights

This probably means that you're trying to push to the *upstream* repository rather than *origin*. Push to origin using:

```shell
git push -u origin
```

## Cannot automatically merge pull request

If a pull request cannot be automatically merged, your branch is out of date with the branch on *upstream*.
Merge in changes from upstream (use the correct branch!):

```shell
git fetch upstream
git merge upstream/assignment1
git push
```

## Resolving merge conflicts

See [Resolving a merge conflict](https://help.github.com/articles/resolving-a-merge-conflict-from-the-command-line/) on how to resolve merge conflicts.
You can also try a GUI merge tool such as [DiffMerge](https://sourcegear.com/diffmerge/) to resolve merge conflicts.
