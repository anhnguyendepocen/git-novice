---
layout: page
title: Version Control with Git
subtitle: Exploring History
minutes: 15
---
> ## Learning Objectives {.objectives}
> 
> *   Identify and use Git revision numbers.
> *   Compare files with old versions of themselves.
> *   Restore old versions of files.


If we want to see what we changed when,
we use `git diff` again,
but refer to old versions
using the notation `HEAD~1`, `HEAD~2`, and so on:

~~~ {.bash}
$ git diff HEAD~1 bmi.py
~~~
~~~ {.output}
diff --git a/bmi.py b/bmi.py
index 6fcaa25..c480208 100644
--- a/bmi.py
+++ b/bmi.py
@@ -1,4 +1,4 @@
 w = raw_input("enter weight in lbs.: ")
 h = raw_input("enter height in inches: ")
 bmi =(float(w)/float(h)**2)*703
-print bmi
+print round(bmi,1)
~~~
~~~ {.bash}
$ git diff HEAD~2 bmi.py
~~~
~~~ {.output}
diff --git a/bmi.py b/bmi.py
index 5009833..c480208 100644
--- a/bmi.py
+++ b/bmi.py
@@ -1,4 +1,4 @@
-w = raw_input("enter weight: ")
-h = raw_input("enter height: ")
+w = raw_input("enter weight in lbs.: ")
+h = raw_input("enter height in inches: ")
 bmi =(float(w)/float(h)**2)*703
-print bmi
+print round(bmi,1)
~~~

In this way,
we build up a chain of revisions.
The most recent end of the chain is referred to as `HEAD`;
we can refer to previous revisions using the `~` notation,
so `HEAD~1` (pronounced "head minus one")
means "the previous revision",
while `HEAD~14` goes back 14 revisions from where we are now.

We can also refer to revisions using
those long strings of digits and letters
that `git log` displays.
These are unique IDs for the changes,
and "unique" really does mean unique:
every change to any set of files on any machine
has a unique 40-character identifier.
Our first commit was given the ID
d8f0892f62d80b2f2aa88dc0ad62e6989a60d282,
so let's try this:

~~~ {.bash}
$ git diff d8f0892f62d80b2f2aa88dc0ad62e6989a60d282 bmi.py
~~~
~~~ {.output}
diff --git a/bmi.py b/bmi.py
index 5009833..c480208 100644
--- a/bmi.py
+++ b/bmi.py
@@ -1,4 +1,4 @@
-w = raw_input("enter weight: ")
-h = raw_input("enter height: ")
+w = raw_input("enter weight in lbs.: ")
+h = raw_input("enter height in inches: ")
 bmi =(float(w)/float(h)**2)*703
-print bmi
+print round(bmi,1)
~~~

That's the right answer,
but typing random 40-character strings is annoying,
so Git lets us use just the first few:

~~~ {.bash}
$ git diff d8f089 bmi.py
~~~
~~~ {.output}
diff --git a/bmi.py b/bmi.py
index 5009833..c480208 100644
--- a/bmi.py
+++ b/bmi.py
@@ -1,4 +1,4 @@
-w = raw_input("enter weight: ")
-h = raw_input("enter height: ")
+w = raw_input("enter weight in lbs.: ")
+h = raw_input("enter height in inches: ")
 bmi =(float(w)/float(h)**2)*703
-print bmi
+print round(bmi,1)
~~~


All right:
we can save changes to files and see what we've changed---how
can we restore older versions of things?
Let's suppose we accidentally overwrite our file:

~~~ {.bash}
$ nano bmi.py
$ cat bmi.py
~~~
~~~ {.output}
print "hello world"
~~~

`git status` now tells us that the file has been changed,
but those changes haven't been staged:

~~~ {.bash}
$ git status
~~~
~~~ {.output}
# On branch master
# Changes not staged for commit:
#   (use "git add <file>..." to update what will be committed)
#   (use "git checkout -- <file>..." to discard changes in working directory)
#
#	modified:   bmi.py
#
no changes added to commit (use "git add" and/or "git commit -a")
~~~

We can put things back the way they were
by using `git checkout` (NOTE: do NOT forget the file name!):

~~~ {.bash}
$ git checkout HEAD bmi.py
$ cat bmi.py
~~~
~~~ {.output}
w = raw_input("enter weight in lbs.: ")
h = raw_input("enter height in inches: ")
bmi =(float(w)/float(h)**2)*703
print round(bmi,1)
~~~

As you might guess from its name,
`git checkout` checks out (i.e., restores) an old version of a file.
In this case,
we're telling Git that we want to recover the version of the file recorded in `HEAD`,
which is the last saved revision.
If we want to go back even further,
we can use a revision identifier instead:

~~~ {.bash}
$ git checkout d8f089 bmi.py
~~~

It's important to remember that
we must use the revision number that identifies the state of the repository
*before* the change we're trying to undo.
A common mistake is to use the revision number of
the commit in which we made the change we're trying to get rid of.
In the example below, we want retrieve the state from before the most
recent commit (`HEAD~1`), which is revision `f22b25e`:

![Git Checkout](fig/git-checkout.svg)

The following diagram illustrates what the history of a file might look
like (moving back from `HEAD`, the most recently committed version):

![When Git Updates Revision Numbers](fig/git-when-revisions-updated.svg)

> ## Simplifying the Common Case {.callout}
>
> If you read the output of `git status` carefully,
> you'll see that it includes this hint:
>
> ~~~ {.bash}
> (use "git checkout -- <file>..." to discard changes in working directory)
> ~~~
>
> As it says,
> `git checkout` without a version identifier restores files to the state saved in `HEAD`.
> The double dash `--` is needed to separate the names of the files being recovered
> from the command itself:
> without it,
> Git would try to use the name of the file as the revision identifier.

The fact that files can be reverted one by one
tends to change the way people organize their work.
If everything is in one large document,
it's hard (but not impossible) to undo changes to the introduction
without also undoing changes made later to the conclusion.
If the introduction and conclusion are stored in separate files,
on the other hand,
moving backward and forward in time becomes much easier.


> ## Recovering Older Versions of a File {.challenge}
>
> Jennifer has made changes to the Python script that she has been working on for weeks, and the 
> modifications she made this morning "broke" the script and it no longer runs. She has spent
> ~ 1hr trying to fix it, with no luck...
>
> Luckily, she has been keeping track of her revisions using Git! Which commands below will 
> let her recover the last committed version of her Python script called
> `data_cruncher.py`?
>
> 1. 
>
>     ~~~
>     $ git checkout HEAD
>     ~~~
> 2. 
>
>     ~~~
>     $ git checkout HEAD data_cruncher.py
>     ~~~
> 3. 
>
>     ~~~
>     $ git checkout HEAD~1 data_cruncher.py
>     ~~~
> 4. 
>
>     ~~~
>     $ git checkout <unique ID of last revision> data_cruncher.py
>     ~~~
> 5. Both 2 & 4


[Home](index.html)
[Previous](03-changes.html)
[Next](05-ignore.html)