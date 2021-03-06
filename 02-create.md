---
layout: page
title: Version Control with Git
subtitle: Creating a Repository
minutes: 10
---
> ## Learning Objectives {.objectives}
> 
> *   Explain how to create a Git repository locally.

Once Git is configured,
we can start using it.
Let's create a directory for our work. In this lesson we'll write a Python program to calculate BMI (Body Mass Index), so let's call it "bmi":

~~~ {.bash}
$ mkdir bmi
$ cd bmi
~~~

and tell Git to make it a [repository](reference.html#repository)&mdash;a place where
Git can store old versions of our files:

~~~ {.bash}
$ git init
~~~

If we use `ls` to show the directory's contents,
it appears that nothing has changed:

~~~ {.bash}
$ ls
~~~

But if we add the `-a` flag to show everything,
we can see that Git has created a hidden directory called `.git`:

~~~ {.bash}
$ ls -a
~~~
~~~ {.output}
.	..	.git
~~~

Git stores information about the project in this special sub-directory.
If we ever delete it,
we will lose the project's history.

We can check that everything is set up correctly
by asking Git to tell us the status of our project:

~~~ {.bash}
$ git status
~~~
~~~ {.output}
# On branch master
#
# Initial commit
#
nothing to commit (create/copy files and use "git add" to track)
~~~

It should be noted that you can create a git repository in an existing directory. Simply `cd` into it and issue the `git init` command.

[Home](index.html)
[Previous](01-setup.html)
[Next](03-changes.html)
