---
layout: page
title: Version Control with Git
subtitle: Conflicts
minutes: 30
---
> ## Learning Objectives {.objectives}
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
$ cat bmi.py
~~~
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

> ## Solving Conflicts that You Create {.challenge}
>
> Clone the repository created by your instructor.
> Add a new file to it,
> and modify an existing file (your instructor will tell you which one).
> When asked by your instructor,
> pull her changes from the repository to create a conflict,
> then resolve it.

> ## Conflicts on Non-textual files {.challenge}
>
> What does Git do
> when there is a conflict in an image or some other non-textual file
> that is stored in version control?

[Home](index.html)
[Previous](06-collab.html)

