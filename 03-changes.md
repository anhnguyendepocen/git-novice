---
layout: page
title: Version Control with Git
subtitle: Tracking Changes
minutes: 20
---
> ## Learning Objectives {.objectives}
> 
> *   Go through the modify-add-commit cycle for single and multiple files.
> *   Explain where information is stored at each stage.

Let's create a python script called `bmi.py` that calculates a persons BMI ([Body Mass Index](http://www.nhlbi.nih.gov/health/educational/lose_wt/BMI/bmicalc.htm)) given his or her weight in pounds and height in inches.
(We'll use `nano` to edit the file;
you can use whatever editor you like.
In particular, this does not have to be the core.editor you set globally earlier.)

~~~ {.bash}
$ nano bmi.py
~~~

Type the code below into the `bmi.py` file:

~~~ {.python}
w = raw_input("enter weight: ")
h = raw_input("enter height: ")
bmi = (float(w) / float(h)**2) * 703
print bmi 
~~~

`bmi.py` now contains four lines:

~~~ {.bash}
$ ls
~~~
~~~ {.output}
bmi.py
~~~
~~~ {.bash}
$ cat bmi.py
~~~
~~~ {.output}
w = raw_input("enter weight: ")
h = raw_input("enter height: ")
bmi = (float(w) / float(h)**2) * 703
print bmi 
~~~

Let's test our program:

~~~ {.bash}
$ python bmi.py
~~~
~~~ {.output}
enter weight in lbs.: 215
enter height in inches: 69
31.7464818315
~~~

It appears to be working (*as long as we enter numbers*).

If we check the status of our project again,
Git tells us that it's noticed the new file:

~~~ {.bash}
$ git status
~~~
~~~ {.output}
# On branch master
#
# Initial commit
#
# Untracked files:
#   (use "git add <file>..." to include in what will be committed)
#
#	bmi.py
nothing added to commit but untracked files present (use "git add" to track)
~~~

The "untracked files" message means that there's a file in the directory
that Git isn't keeping track of.
We can tell Git to track a file using `git add`:

~~~ {.bash}
$ git add bmi.py
~~~

and then check that the right thing happened:

~~~ {.bash}
$ git status
~~~
~~~ {.output}
# On branch master
#
# Initial commit
#
# Changes to be committed:
#   (use "git rm --cached <file>..." to unstage)
#
#	new file:   bmi.py
#
~~~

Git now knows that it's supposed to keep track of `bmi.py`,
but it hasn't recorded these changes as a commit yet.
To get it to do that,
we need to run one more command:

~~~ {.bash}
$ git commit -m "first version of bmi program"
~~~
~~~ {.output}
[master (root-commit) f22b25e] first version of bmi program
 1 file changed, 4 insertions(+)
 create mode 100644 bmi.py
~~~

When we run `git commit`,
Git takes everything we have told it to save by using `git add`
and stores a copy permanently inside the special `.git` directory.
This permanent copy is called a [revision](reference.html#revision)
and its short identifier is `f22b25e`.
(Your revision may have another identifier.)

We use the `-m` flag (for "message")
to record a comment that will help us remember later on what we did and why.
If we just run `git commit` without the `-m` option,
Git will launch `nano` (or whatever other editor we configured at the start)
so that we can write a longer message.

If we run `git status` now:

~~~ {.bash}
$ git status
~~~
~~~ {.output}
# On branch master
nothing to commit, working directory clean
~~~

it tells us everything is up to date.
If we want to know what we've done recently,
we can ask Git to show us the project's history using `git log`:

~~~ {.bash}
$ git log
~~~
~~~ {.output}
commit f22b25e3233b4645dabd0d81e651fe074bd8e73b
Author: Tom Jefferson <tj7z@virginia.edu>
Date:   Thu Aug 22 09:51:46 2014 -0400

    first version of bmi program
~~~

`git log` lists all revisions  made to a repository in reverse chronological order.
The listing for each revision includes
the revision's full identifier
(which starts with the same characters as
the short identifier printed by the `git commit` command earlier),
the revision's author,
when it was created,
and the log message Git was given when the revision was created.

> ## Where Are My Changes? {.callout}
>
> If we run `ls` at this point, we will still see just one file called `bmi.py`.
> That's because Git saves information about files' history
> in the special `.git` directory mentioned earlier
> so that our filesystem doesn't become cluttered
> (and so that we can't accidentally edit or delete an old version).

Now suppose Tom updates the program to indicate that weight must be entered in pounds and height in inches.
(Again, we'll edit with `nano` and then `cat` the file to show its contents;
you may use a different editor, and don't need to `cat`.)

~~~ {.bash}
$ nano bmi.py
$ cat bmi.py
~~~
~~~ {.output}
w = raw_input("enter weight in lbs.: ")
h = raw_input("enter height in inches: ")
bmi = (float(w) / float(h)**2) * 703
print bmi
~~~

When we run `git status` now,
it tells us that a file it already knows about has been modified:

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

The last line is the key phrase:
"no changes added to commit".
We have changed this file,
but we haven't told Git we will want to save those changes
(which we do with `git add`)
much less actually saved them (which we do with `git commit`).
So let's do that now. It is good practice to always review
our changes before saving them. We do this using `git diff`.
This shows us the differences between the current state
of the file and the most recently saved version:

~~~ {.bash}
$ git diff
~~~
~~~ {.output}
diff --git a/bmi.py b/bmi.py
index 5009833..6fcaa25 100644
--- a/bmi.py
+++ b/bmi.py
@@ -1,4 +1,4 @@
-w = raw_input("enter weight: ")
-h = raw_input("enter height: ")
+w = raw_input("enter weight in lbs.: ")
+h = raw_input("enter height in inches: ")
 bmi =(float(w)/float(h)**2)*703
 print bmi
~~~

The output is cryptic because
it is actually a series of commands for tools like editors and `patch`
telling them how to reconstruct one file given the other.
If we break it down into pieces:

1.  The first line tells us that Git is producing output similar to the Unix `diff` command
    comparing the old and new versions of the file.
2.  The second line tells exactly which revisions of the file
    Git is comparing;
    `5009833` and `6fcaa25` are unique computer-generated labels for those revisions.
3.  The third and fourth lines once again show the name of the file being changed.
4.  The remaining lines are the most interesting, they show us the actual differences
    and the lines on which they occur.
    In particular,
    the `+` markers in the first column show where we are adding lines.

After reviewing our change, it's time to commit it:

~~~ {.bash}
$ git commit -m "Add weight and height units"
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

Whoops:
Git won't commit because we didn't use `git add` first.
Let's fix that:

~~~ {.bash}
$ git add bmi.py
$ git commit -m "Add weight and height units"
~~~
~~~ {.output}
[master 34961b1] Add weight and height units
 1 file changed, 2 insertions(+), 2 deletions(-)
~~~

Git insists that we add files to the set we want to commit
before actually committing anything
because we may not want to commit everything at once.
For example,
suppose we're adding a few citations to our supervisor's work
to our thesis.
We might want to commit those additions,
and the corresponding addition to the bibliography,
but *not* commit the work we're doing on the conclusion
(which we haven't finished yet).

To allow for this,
Git has a special staging area
where it keeps track of things that have been added to
the current [change set](reference.html#change-set)
but not yet committed.
`git add` puts things in this area,
and `git commit` then copies them to long-term storage (as a commit):

![The Git Staging Area](fig/git-staging-area.svg)

Let's watch as our changes to a file move from our editor
to the staging area
and into long-term storage.
First,
we'll update our program to round the BMI to one decimal place:

~~~ {.bash}
$ nano bmi.py
$ cat bmi.py
~~~
~~~ {.output}
w = raw_input("enter weight in lbs.: ")
h = raw_input("enter height in inches: ")
bmi = (float(w) / float(h)**2) * 703
print round(bmi,1)
~~~
~~~ {.bash}
$ git diff
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

So far, so good:
we've changed the last line the program.
Now let's put that change in the staging area
and see what `git diff` reports:

~~~ {.bash}
$ git add bmi.py
$ git diff
~~~

There is no output:
as far as Git can tell,
there's no difference between what it's been asked to save permanently
and what's currently in the directory.
However,
if we do this:

~~~ {.bash}
$ git diff --staged
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

it shows us the difference between
the last committed change
and what's in the staging area.
Let's save our changes:

~~~ {.bash}
$ git commit -m "round bmi to one decimal place"
~~~
~~~ {.output}
[master 005937f] round bmi to one decimal place
 1 file changed, 1 insertion(+), 1 deletion(-)
~~~

check our status:

~~~ {.bash}
$ git status
~~~
~~~ {.output}
# On branch master
nothing to commit, working directory clean
~~~

and look at the history of what we've done so far:

~~~ {.bash}
$ git log
~~~
~~~ {.output}
commit 005937fbe2a98fb83f0ade869025dc2636b4dad5
Author: Tom Jefferson <tj7x@virginia.edu>
Date:   Thu Aug 22 10:14:07 2014 -0400

    round bmi to one decimal place

commit 34961b159c27df3b475cfe4415d94a6d1fcd064d
Author: Tom Jefferson <tj7x@virginia.edu>
Date:   Thu Aug 22 10:07:21 2014 -0400

    add weight and height units

commit f22b25e3233b4645dabd0d81e651fe074bd8e73b
Author: Tom Jefferson <tj7x@virginia.edu>
Date:   Thu Aug 22 09:51:46 2014 -0400

    first version of bmi program
~~~

To recap, when we want to add changes to our repository,
we first need to add the changed files to the staging area
(`git add`) and then commit the staged changes to the
repository (`git commit`):

![The Git Commit Workflow](fig/git-committing.svg)


> ## Committing Changes to Git {.challenge}
>
> Which command(s) below would save changes of `myfile.txt` to my local Git repository?
>
> 1. 
>
>     ~~~
>     $ git commit -m "my recent changes"
>     ~~~
> 2. 
>
>     ~~~
>     $ git init myfile.txt
>     $ git commit -m "my recent changes"
>     ~~~
> 3. 
>
>     ~~~
>     $ git add myfile.txt
>     $ git commit -m "my recent changes"
>     ~~~
> 4. 
>
>     ~~~
>     $ git commit -m myfile.txt "my recent changes"
>     ~~~

> ## `bio` Repository {.challenge}
>
> Create a new Git repository on your computer called `bio`.
> Write a three-line biography for yourself in a file called `me.txt`,
> commit your changes,
> then modify one line and add a fourth and display the differences
> between its updated state and its original state.
