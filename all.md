---
layout: page
title: Version Control with Git
subtitle: All sections
---
> ## Setting Up Git: Learning Objectives {.objectives}
>
> *   Explain which initialization and configuration steps are required once per machine,
>     and which are required once per repository.

We'll start by exploring how version control can be used
to keep track of what one person did and when.

The first time we use Git on a new machine,
we need to configure a few things.
Here's how Thomas Jefferson would set up his new laptop:

~~~ {.bash}
$ git config --global user.name "Tom Jefferson"
$ git config --global user.email "tj7z@virginia.edu"
$ git config --global color.ui "auto"
~~~

(Please use your own name and email address instead of Tom's.)

He also has to set his favorite text editor, following this table:

| Editor             | Configuration command                            |
|:-------------------|:-------------------------------------------------|
| nano               | `$ git config --global core.editor "nano -w"`    |
| Text Wrangler      | `$ git config --global core.editor "edit -w"`    |
| Sublime Text (Mac) | `$ git config --global core.editor "subl -n -w"` |
| Sublime Text (Win) | `$ git config --global core.editor "'c:/program files/sublime text 2/sublime_text.exe' -w"` |
| Notepad++ (Win)    | `$ git config --global core.editor "'c:/program files (x86)/Notepad++/notepad++.exe' -multiInst -notabbar -nosession -noPlugin"`|
| Kate (Linux)       | `$ git config --global core.editor "kate"`       |
| Gedit (Linux)      | `$ git config --global core.editor "gedit -s"`   |

Git commands are written `git verb`,
where `verb` is what we actually want it to do.
In this case,
we're telling Git:

*   our name and email address,
*   to colorize output,
*   what our favorite text editor is, and
*   that we want to use these settings globally (i.e., for every project),

The four commands above only need to be run once:
the flag `--global` tells Git to use the settings for every project on this machine.

You can check your settings at any time:

~~~ {.bash}
$ git config --list
~~~

> ## Proxy {.callout}
>
> In some networks you need to use a proxy. If this is the case you may also
> need to tell Git about the proxy:
>
> ~~~ {.bash}
> $ git config --global http.proxy proxy-url
> $ git config --global https.proxy proxy-url
> ~~~
>
> To disable the proxy, use
>
> ~~~ {.bash}
> $ git config --global --unset http.proxy
> $ git config --global --unset https.proxy
> ~~~


> ## Creating a Repository: Learning Objectives {.objectives}
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


> ## Tracking Changes: Learning Objectives {.objectives}
> 
> *   Go through the modify-add-commit cycle for single and multiple files.
> *   Explain where information is stored at each stage.

Let's create a Python script called `bmi.py` that calculates a persons BMI ([Body Mass Index](http://www.nhlbi.nih.gov/health/educational/lose_wt/BMI/bmicalc.htm)) given his or her weight in pounds and height in inches. BMI is a measure of body fat based on height and weight that applies to adult men and women. 
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


> ## Exploring History: Learning Objectives {.objectives}
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



> ## Ignoring Things: Learning Objectives {.objectives}
> 
> *   Configure Git to ignore specific files,
>     and explain why it is sometimes useful to do so.

What if we have files that we do not want Git to track for us,
like backup files created by our editor
or intermediate files created during data analysis.
Let's create a few dummy files:

~~~ {.bash}
$ mkdir results
$ touch a.dat b.dat c.dat results/a.out results/b.out
~~~

and see what Git says:

~~~ {.bash}
$ git status
~~~
~~~ {.output}
# On branch master
# Untracked files:
#   (use "git add <file>..." to include in what will be committed)
#
#	a.dat
#	b.dat
#	c.dat
#	results/
nothing added to commit but untracked files present (use "git add" to track)
~~~

Putting these files under version control would be a waste of disk space.
What's worse,
having them all listed could distract us from changes that actually matter,
so let's tell Git to ignore them.

We do this by creating a file in the root directory of our project called `.gitignore`.

~~~ {.bash}
$ nano .gitignore
$ cat .gitignore
~~~
~~~ {.output}
*.dat
results/
~~~

These patterns tell Git to ignore any file whose name ends in `.dat`
and everything in the `results` directory.
(If any of these files were already being tracked,
Git would continue to track them.)

Once we have created this file,
the output of `git status` is much cleaner:

~~~ {.bash}
$ git status
~~~
~~~ {.output}
# On branch master
# Untracked files:
#   (use "git add <file>..." to include in what will be committed)
#
#	.gitignore
nothing added to commit but untracked files present (use "git add" to track)
~~~

The only thing Git notices now is the newly-created `.gitignore` file.
You might think we wouldn't want to track it,
but everyone we're sharing our repository with will probably want to ignore
the same things that we're ignoring.
Let's add and commit `.gitignore`:

~~~ {.bash}
$ git add .gitignore
$ git commit -m "Add the ignore file"
$ git status
~~~
~~~ {.output}
# On branch master
nothing to commit, working directory clean
~~~

As a bonus,
using `.gitignore` helps us avoid accidentally adding files to the repository that we don't want.

~~~ {.bash}
$ git add a.dat
~~~
~~~ {.output}
The following paths are ignored by one of your .gitignore files:
a.dat
Use -f if you really want to add them.
fatal: no files added
~~~

If we really want to override our ignore settings,
we can use `git add -f` to force Git to add something.
We can also always see the status of ignored files if we want:

~~~ {.bash}
$ git status --ignored
~~~
~~~ {.output}
# On branch master
# Ignored files:
#  (use "git add -f <file>..." to include in what will be committed)
#
#        a.dat
#        b.dat
#        c.dat
#        results/

nothing to commit, working directory clean
~~~


> ## Collaborating: Learning Objectives {.objectives}
>
> *   Explain what remote repositories are and why they are useful.
> *   Explain what happens when a remote repository is cloned.
> *   Explain what happens when changes are pushed to or pulled from a remote repository.

Version control really comes into its own
when we begin to collaborate with other people.
We already have most of the machinery we need to do this;
the only thing missing is to copy changes from one repository to another.

Systems like Git allow us to move work between any two repositories.
In practice,
though,
it's easiest to use one copy as a central hub,
and to keep it on the web rather than on someone's laptop.
Most programmers use hosting services like [GitHub](http://github.com) or [BitBucket](http://bitbucket.org)
to hold those master copies.

Let's start by sharing the changes we've made to our current project with the world.
Log in to GitHub,
then click on the icon in the top right corner to create a new repository called `bmi`.


Name your repository "bmi" and then click "Create Repository".

As soon as the repository is created,
GitHub displays a page with a URL and some information on how to configure your local repository.


This effectively does the following on GitHub's servers:

~~~ {.bash}
$ mkdir bmi
$ cd bmi
$ git init
~~~

Our local repository still contains our earlier work on `bmi.py`,
but the remote repository on GitHub doesn't contain any files yet.

The next step is to connect the two repositories.
We do this by making the GitHub repository a [remote](reference.html#remote)
for the local repository.
The home page of the repository on GitHub includes
the string we need to identify it:

![Where to Find Repository URL on GitHub](fig/github-find-repo-string.png)

Click on the 'HTTPS' link to change the [protocol](reference.html#protocol) from SSH to HTTPS.

> ## HTTPS vs SSH {.callout}
>
> We use HTTPS here because it does not require additional configuration.
> After the workshop you may want to set up SSH access, which is a bit more
> secure, by following one of the great tutorials from
> [GitHub](https://help.github.com/articles/generating-ssh-keys),
> [Atlassian/BitBucket](https://confluence.atlassian.com/display/BITBUCKET/Set+up+SSH+for+Git)
> and [GitLab](https://about.gitlab.com/2014/03/04/add-ssh-key-screencast/)
> (this one has a screencast).

![Changing the Repository URL on GitHub](fig/github-change-repo-string.png)

Copy that URL from the browser,
go into the local `bmi` repository,
and run this command:

~~~ {.bash}
$ git remote add origin https://github.com/tomjeff/bmi.git
~~~

Make sure to use the URL for your repository rather than Tom's:
the only difference should be your username instead of `tomjeff`.

We can check that the command has worked by running `git remote -v`:

~~~ {.bash}
$ git remote -v
~~~
~~~ {.output}
origin   https://github.com/tomjeff/bmi.git (push)
origin   https://github.com/tomjeff/bmi.git (fetch)
~~~

The name `origin` is a local nickname for your remote repository:
we could use something else if we wanted to,
but `origin` is by far the most common choice.

Once the nickname `origin` is set up,
this command will push the changes from our local repository
to the repository on GitHub:

~~~ {.bash}
$ git push origin master
~~~
~~~ {.output}
Counting objects: 9, done.
Delta compression using up to 4 threads.
Compressing objects: 100% (6/6), done.
Writing objects: 100% (9/9), 821 bytes, done.
Total 9 (delta 2), reused 0 (delta 0)
To https://github.com/tomjeff/bmi
 * [new branch]      master -> master
Branch master set up to track remote branch master from origin.
~~~

> ## Proxy {.callout}
>
> If the network you are connected to uses a proxy there is an chance that your last
> command failed with "Could not resolve hostname" as the error message. To
> solve this issue you need to tell Git about the proxy:
>
> ~~~ {.bash}
> $ git config --global http.proxy http://user:password@proxy.url
> $ git config --global https.proxy http://user:password@proxy.url
> ~~~
>
> When you connect to another network that doesn't use a proxy you will need to
> tell Git to disable the proxy using
>
> ~~~ {.bash}
> $ git config --global --unset http.proxy
> $ git config --global --unset https.proxy
> ~~~

> ## Password Managers {.callout}
>
> If your operating system has a password manager configured, `git push` will
> try to use it when it needs your username and password. If you want to type
> your username and password at the terminal instead of using
> a password manager, type
>
> ~~~ {.bash}
> $ unset SSH_ASKPASS
> ~~~
>
> You may want to add this command at the end of your `~/.bashrc` to make it the
> default behavior.


> ## The '-u' Flag {.callout}
>
> You may see a `-u` option used with `git push` in some documentation.
> It is related to concepts we cover in our intermediate lesson,
> and can safely be ignored for now.

We can pull changes from the remote repository to the local one as well:

~~~ {.bash}
$ git pull origin master
~~~
~~~ {.output}
From https://github.com/tomjeff/bmi
 * branch            master     -> FETCH_HEAD
Already up-to-date.
~~~

Pulling has no effect in this case
because the two repositories are already synchronized.
If someone else had pushed some changes to the repository on GitHub,
though,
this command would download them to our local repository.

For the next step, get into pairs.
Pick one of your repositories on GitHub to use for collaboration.

> ## Practicing by yourself {.callout}
>
> If you're working through this lesson on your own, you can carry on by opening
> a second terminal window, and switching to another directory (e.g. `/tmp`).
> This window will represent your partner, working on another computer. You
> won't need to give anyone access on GitHub, because both 'partners' are you.

The partner whose repository is being used needs to give the other person access.
On GitHub, click the settings button on the right,
then select Collaborators, and enter your partner's username.

![Adding collaborators on GitHub](fig/github-add-collaborators.png)

The other partner should `cd` to another directory
(so `ls` doesn't show a `bmi` folder),
and then make a copy of this repository on their own computer:

~~~ {.bash}
$ git clone https://github.com/tomjeff/bmi.git
~~~

Replace 'tomjeff' with your partner's username (the one who owns the repository).

`git clone` creates a fresh local copy of a remote repository.

![After Creating Clone of Repository](fig/github-collaboration.svg)

The new collaborator can now make a change in their copy of the repository. For example, let's say she begins creating some documentation called help.txt:

~~~ {.bash}
$ cd bmi
$ nano help.txt
$ cat help.txt
~~~
~~~ {.output}
BMI is calculated as (weight / height^2) * 703. 
~~~
~~~ {.bash}
$ git add help.txt
$ git commit -m "first version of help file"
~~~
~~~ {.output}
 1 file changed, 1 insertion(+)
 create mode 100644 help.txt
~~~

then push the change to GitHub:

~~~ {.bash}
$ git push origin master
~~~
~~~ {.output}
Counting objects: 4, done.
Delta compression using up to 4 threads.
Compressing objects: 100% (2/2), done.
Writing objects: 100% (3/3), 306 bytes, done.
Total 3 (delta 0), reused 0 (delta 0)
To https://github.com/tomjeff/bmi.git
   9272da5..29aba7c  master -> master
~~~

Note that we didn't have to create a remote called `origin`:
Git does this automatically,
using that name,
when we clone a repository.
(This is why `origin` was a sensible choice earlier
when we were setting up remotes by hand.)

We can now download changes into the original repository on our machine:

~~~ {.bash}
$ git pull origin master
~~~
~~~ {.output}
remote: Counting objects: 4, done.
remote: Compressing objects: 100% (2/2), done.
remote: Total 3 (delta 0), reused 3 (delta 0)
Unpacking objects: 100% (3/3), done.
From https://github.com/tomjeff/bmi
 * branch            master     -> FETCH_HEAD
Updating 9272da5..29aba7c
Fast-forward
 help.txt | 1 +
 1 file changed, 1 insertion(+)
 create mode 100644 help.txt
~~~


> ## Conflicts: Learning Objectives {.objectives}
>
> *   Explain what conflicts are and when they can occur.
> *   Resolve conflicts resulting from a merge.

As soon as people can work in parallel,
someone's going to step on someone else's toes.
This will even happen with a single person:
if we are working on a piece of software on both our laptop and a server in the lab,
we could make different changes to each copy.
Version control helps us manage these [conflicts](reference.html#conflicts)
by giving us tools to [resolve](reference.html#resolve) overlapping changes.

To see how we can resolve conflicts,
we must first create one.
The file `bmi.py` currently looks like this
in both partners' copies of our `bmi` repository:

~~~ {.bash}
$ cat bmi.py
~~~
~~~ {.output}
w = raw_input("enter weight in lbs.: ")
h = raw_input("enter height in inches: ")
bmi =(float(w)/float(h)**2)*703
print round(bmi,1)
~~~

Let's change a line in one partner's copy only:

~~~ {.bash}
$ nano bmi.py
$ cat bmi.py
~~~
~~~ {.output}
w = raw_input("enter weight in lbs.: ")
h = raw_input("enter height in inches: ")
bmi =(float(w)/float(h)**2)*703
print 'Your BMI is %s' % (round(bmi,1))
~~~

and then push the change to GitHub:

~~~ {.bash}
$ git add bmi.py
$ git commit -m "update output language"
~~~
~~~ {.output}
[master 5ae9631] Adding a line in our home copy
 1 file changed, 1 insertion(+)
~~~
~~~ {.bash}
$ git push origin master
~~~
~~~ {.output}
Counting objects: 5, done.
Delta compression using up to 4 threads.
Compressing objects: 100% (3/3), done.
Writing objects: 100% (3/3), 352 bytes, done.
Total 3 (delta 1), reused 0 (delta 0)
To https://github.com/tomjeff/bmi
   29aba7c..dabb4c8  master -> master
~~~

Now let's have the other partner
make a slightly different change to their copy
*without* updating from GitHub:

~~~ {.bash}
$ nano bmi.py
$ cat bmi.py
~~~
~~~ {.output}
w = raw_input("enter weight in lbs.: ")
h = raw_input("enter height in inches: ")
bmi =(float(w)/float(h)**2)*703
print 'BMI: %s' % (round(bmi,1))
~~~

We can commit the change locally:

~~~ {.bash}
$ git add bmi.py
$ git commit -m "add BMI label"
~~~
~~~ {.output}
[master 07ebc69] Adding a line in my copy
 1 file changed, 1 insertion(+)
~~~

but Git won't let us push it to GitHub:

~~~ {.bash}
$ git push origin master
~~~
~~~ {.output}
To https://github.com/tomjeff/bmi.git
 ! [rejected]        master -> master (non-fast-forward)
error: failed to push some refs to 'https://github.com/tomjeff/bmi.git'
hint: Updates were rejected because the remote contains work that you do
hint: not have locally. This is usually caused by another repository pushing
hint: to the same ref. You may want to first integrate the remote changes
hint: (e.g., 'git pull ...') before pushing again.
hint: See the 'Note about fast-forwards' in 'git push --help' for details.
~~~

Git detects that the changes made in one copy overlap with those made in the other
and stops us from trampling on our previous work.
What we have to do is pull the changes from GitHub,
[merge](reference.html#merge) them into the copy we're currently working in,
and then push that.
Let's start by pulling:

~~~ {.bash}
$ git pull origin master
~~~
~~~ {.output}
remote: Counting objects: 3, done.
remote: Compressing objects: 100% (2/2), done.
remote: Total 3 (delta 1), reused 3 (delta 1), pack-reused 0
Unpacking objects: 100% (3/3), done.
From https://github.com/tomjeff/bmi
 * branch            master     -> FETCH_HEAD
   fd79f68..620cc4f  master     -> origin/master
Auto-merging bmi.py
CONFLICT (content): Merge conflict in bmi.py
Automatic merge failed; fix conflicts and then commit the result.
~~~

`git pull` tells us there's a conflict,
and marks that conflict in the affected file:

~~~ {.bash}
$ cat bmi.py
~~~
~~~ {.output}
w = raw_input("enter weight in lbs.: ")
h = raw_input("enter height in inches: ")
bmi =(float(w)/float(h)**2)*703
<<<<<<< HEAD
print 'BMI: %s' % (round(bmi,1))
=======
print 'Your BMI is %s' % (round(bmi,1))
>>>>>>> 620cc4fb444be59733bbea0a28451d5877ed053d
~~~

Our change---the one in `HEAD`---is preceded by `<<<<<<<`.
Git has then inserted `=======` as a separator between the conflicting changes
and marked the end of the content downloaded from GitHub with `>>>>>>>`.
(The string of letters and digits after that marker
identifies the revision we've just downloaded.)

It is now up to us to edit this file to remove these markers
and reconcile the changes.
We can do anything we want: keep the change made in the local repository, keep
the change made in the remote repository, write something new to replace both,
or get rid of the change entirely.
Let's replace both so that the file looks like this:

~~~ {.bash}
$ nano bmi.py
~~~
Note: In `nano`, Ctrl + K can be used to quickly `delete` a line in file, which can be useful for deleting lines such as `<<<<<<< HEAD`.

~~~ {.output}
w = raw_input("enter weight in lbs.: ")
h = raw_input("enter height in inches: ")
bmi =(float(w)/float(h)**2)*703
print 'Body Mass Index (BMI): %s' % (round(bmi,1))
~~~

To finish merging,
we add `bmi.py` to the changes being made by the merge
and then commit:

~~~ {.bash}
$ git add bmi.py
$ git status
~~~
~~~ {.output}
# On branch master
# All conflicts fixed but you are still merging.
#   (use "git commit" to conclude merge)
#
# Changes to be committed:
#
#	modified:   bmi.py
#
~~~
~~~ {.bash}
$ git commit -m "Merging changes from GitHub"
~~~
~~~ {.output}
[master 2abf2b1] Merging changes from GitHub
~~~

Now we can push our changes to GitHub:

~~~ {.bash}
$ git push origin master
~~~
~~~ {.output}
Counting objects: 10, done.
Delta compression using up to 4 threads.
Compressing objects: 100% (6/6), done.
Writing objects: 100% (6/6), 697 bytes, done.
Total 6 (delta 2), reused 0 (delta 0)
To https://github.com/tomjeff/bmi.git
   dabb4c8..2abf2b1  master -> master
~~~

Git keeps track of what we've merged with what,
so we don't have to fix things by hand again
when the collaborator who made the first change pulls again:

~~~ {.bash}
$ git pull origin master
~~~
~~~ {.output}
remote: Counting objects: 10, done.        
remote: Compressing objects: 100% (4/4), done.        
remote: Total 6 (delta 2), reused 6 (delta 2)        
Unpacking objects: 100% (6/6), done.
From https://github.com/tomjeff/bmi
 * branch            master     -> FETCH_HEAD
Updating dabb4c8..2abf2b1
Fast-forward
 bmi.py | 2 +-
 1 file changed, 1 insertion(+), 1 deletion(-)
~~~

we get the merged file:

~~~ {.bash}
$ cat bmi.py
~~~
~~~ {.output}
w = raw_input("enter weight in lbs.: ")
h = raw_input("enter height in inches: ")
bmi =(float(w)/float(h)**2)*703
print 'Body Mass Index (BMI): %s' % (round(bmi,1))
~~~

We don't need to merge again because Git knows someone has already done that.

Version control's ability to merge conflicting changes
is another reason users tend to divide their programs and papers into multiple files
instead of storing everything in one large file.
There's another benefit too:
whenever there are repeated conflicts in a particular file,
the version control system is essentially trying to tell its users
that they ought to clarify who's responsible for what,
or find a way to divide the work up differently.


[Home](index.html)


