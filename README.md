LearninGit
==========

An overview of my git workflow. Feel free to suggest changes or fork your own version.


Part 1 - Why git?
-----------------

It is important to remember why we use git in the first place. Many times, git users shy away from
performing even simple operations such as reverting back to a previous state, or branching from an
old commit, usually because the operation is perceived as "difficult" or "risky" either. The fear
of the unknown, is actually unwarranted. Git was designed to make such things easy and safe, however,
this assumes some knowledge on the part of the user.

In particular, git excels at the following:

+ Git exhibits *high durability* - Git records the history of each file **independently** and allows the user to revert back to state of a given file or compare any two states of a given file. Usually, these operations are performed on the whole repository.
+ Git exhibits *high performance* - At the core of git's codebase lies an **intelligent** differencing algorithm. It does not save numerous copies of the file so the user should not be afraid to record many states or branch without inhibition.
+ Git exhibits *high availability* - Git makes it easy to clone or fork repositories, so that project complexity is manageable even as the numbers of collaborators approaches the thousands (e.g. the linux kernel).
+ Git offers *freedom* - That is, the freedom to try something daring without any real consequences (usually). The freedom to go back in time, all the way to the nascent of the project if you so choose. The freedom to work on 2834 independent features at once without a single one of them affecting the others.

If at any point, you shy away from taking advantage any of these points because what you want to do
seems too hard, remind yourself that in fact, what you want to do is **EASY** if only you knew how
to do it. All you need is more knowledge, and knowledge is power.

Part 2 - Basic terminology
--------------------------

Let's start with a simple repository with a single file (call it file.txt). The file's contents are as follows

> Now is the time for all good men to come to
> the aid of their country. This is just a
> regular paragraph.
> 
> The quick brown fox jumped over the lazy
> dog's back.

and we can denote the state of this file as *A*. Suppose we made a change to the file:

> Now is the time for all good men to come to
> the aid of their country. *This is just an
> awesome paragraph!*
> 
> The quick brown fox jumped over the lazy
> dog's back.

After saving the file locally in your favorite editor, git updates the **index** of your project.
Your **working tree** at this point is just *A*. Right now the state of the repository looks
like:

    master: A------A'
           (WT)   (index)

If we now perform a `git add file.txt` followed with a subsequent `git commit`, the changes to file.txt
are now recorded and the state of the repository looks like:

    master: A------B
           (WT)


Note that both `A` and `B` are now part of the current working tree (abbr. WT). What was called the *index*
before has been recorded by git as state `B`.

The `HEAD` in git nomenclature is the *location of the current reference*. After commiting our changes
to the file, the `HEAD` currently points to state `B`. If we were to do, say:

`git checkout (commit code for A)`

the file would look like what it was before our edit and the repository would look like

    master: A------B
            H

Note that you can use `git log` to see the commit hash which is pretty much guaranteed to be unique. We could have also arrived at this state by typing `git checkout HEAD^` (the caret means back one commit
from the reference name just to the left of it). Alternatively we could have done, `git checkout HEAD~1`,
where `ref~n` denotes the nth commit before ref.

The whole progression from `A` to `B` including any additional changes we might make (the new index built
on top of B) constitutes a git **branch**. Git branches include the working tree and the index. We can
make a new branch by doing something like

`git checkout -b cool-branch`

    cool-branch:    ?
                   /
    master:      A------B

Note that the working tree of `cool-branch` contains `A` because we had moved `HEAD` to point to `A`.
Running `git checkout -b branch-name` creates a new branch off of the current location of `HEAD`. If we now
edit the file, it will look like:

> Now is the time for all good men to come to
> the aid of their country. This is just a
> regular paragraph.
> 
> The quick brown fox jumped over the lazy
> dog's back.

because the working tree of cool-branch contains state `A` and nothing else.
To conclude this terminology section, let's edit the second paragraph and make a commit:

> Now is the time for all good men to come to
> the aid of their country. This is just a
> regular paragraph.
> 
> The quick brown *velociraptor* jumped over the lazy
> dog's back.

    git add file.txt
    git commit -m 'Velociraptors are awesome!'

    cool-branch:    C
                   /
    master:       A------B

Note that `HEAD` now points to state `C` on `cool-branch`. Note also that the new commit *is not named
`B` despite its previous commit being `A`*. Commit names are unique in the entire repository regardless
of what branch it was committed to unless they are merged from one branch to another, rebased, or cherry-
picked (more on these later).

Part 3 - Setup
--------------

I like to have my remote aliases as follows:

    origin -> my fork
    upstream -> main repository

To create the fork, one could simply hit the "fork" button in github. The purpose of the setup is to
abstract one's on development efforts from the main repository. That way, the balance of power is such
that nobody directly writes to the main repository. If *everyone* were to read and write directly to the
main repository, chaos would certainly ensue, depending on the number of collaboraters.

You can name these remotes with:

    git remote add origin url-to-your-fork
    git remote add upstream url-to-main-repo

If either `origin` or `upstream` already exist, you may remove them with `git remote rm remote-alias-to-remove`. Incidentally, if there are other collaborators, you can add remote aliases to their forks as well and fetch their changes for local viewing (and editing).

Part 4 - Basic Workflow
-----------------------

1. Usually, I start by making sure my local environment has an up-to-date copy of my remote environment.
I accomplish this with a `git fetch upstream`. This will sync all the branches existing on upstream and
you should receive a status message notifying you of which branches received updates and which branches
were created.

2. Then, to ensure that my current work on branch `dev` say is compatible with the updated changes on the
main branch, I will do

        git rebase upstream/master

    This will take all the commits I have on my current branch and replay them on the tip of the `upstream/master`
branch. This is preferable to having another merge commit on top of my existing changes. A rebase looks like this:

        dev:             C------E------F
                        /
        master:    A---B-----D-----G-------H 
    
        git rebase upstream/master
        
        dev:                                C------E------F
                                           /
        master:    A---B-----D-----G------H
    
    While a merge looks like this:
    
        dev:             C------E------F
                        /
        master:    A---B-----D-----G-------H 
    
        git merge upstream/master
        
        dev:             C------E------F----I
                        /                  /
        master:    A---B-----D-----G------H

    Note that the merge creates a *new commit* at the head of your dev branch. Your changes then, are no longer
nicely encapsulated as a linear progression of work from the tip of master, which is a moving target. The
rebase, however, maintains the dev branch as up-to-date without sullying its history. The real benefit is
seen when the dev branch gets merged back into the master branch.

    It is important to understand that state `F` after the rebase is *identical in content* to state `I` after
the merge. A rebase does not ever create a new commit, it *modifies existing ones*. This is why `git help rebase`
issues a cautionary warning, saying that rebase modifies history. This is OK for commits that have yet to be
merged onto the master branch, but I usually shy away from rebasing commits that already have.

3. After working some more, I commit my changes to a new commit and push my changes to origin (my fork).

        dev:                                C------E------F------I
                                           /
        master:    A---B-----D-----G------H
         
        git push origin dev

4. In preparation to submit a pull request, I usually run a `git rebase -i HEAD~n`, where `n` is the number
of commits I have on my current branch. The interactive rebase allows me to edit commit messages, reorder
commits, and squash small commits in ways that make sense (make sure that commits that are squashed together
are related!).

    Upon running `git rebase -i`, git will pull up the editor git is configured to use with a `n` lines. Each
line contains a command (defaults to pick), a commit number, and a commit message. The lines can be shuffled
within the file to change the commit application order. In addition, the 'pick' command can be changed to be
'r' for reword, or 's' for squash. Squashing a commit will squash it with the commit directly preceding it 
(above it in the file).

    **Warning**: Deleting a line in this file will remove a commit from your branch. If things go awry, simply
run `git rebase --abort` and git will rewind the back to its original state prior to the rebase.

5. After I am satisfied with my work after several iterations of steps (1) through (4), I submit a pull request
in github from my fork page. After the code is approved and *merged in* (here a merge is appropriate), the
git topology looks like this:

        dev:                                C------E------F------I
                                           /                      \
        master:    A---B-----D-----G------H------------------------J

Part 5 - Common Scenarios
-------------------------

There are a number of common scenarios that crop up during the course of a work session that disrupt the
above workflow. The following itemized list addresses the most frequent ones and covers the appropriate
response.

### I edit changes on the wrong branch and need to move them to a different branch.

The remedy is to use `git stash`. This will put your current index in the stash. Then you can simply
`git checkout (-b) branch-that-you-want` and subsequently run `git stash pop` to recover the index on 
top of the new working tree.

If you have already committed the changes, you can run `git reset HEAD^`. This will move the head back
one commit and turn the last commit into unstaged changes (the last commit is now the new index). Finally,
perform the steps in the above paragraph.

Alternatively, if the changes are already committed, you could checkout to a new branch, checkout back
to the old branch, and `git reset --hard HEAD^` to move the old branch back to its initial state.

In all of these changes, you could substitute `HEAD^` with `HEAD~1` or the commit number of your last 
commit.

### After a rebase, git complains that I need to pull first when I try to push

Don't pull first. Pulling will fetch your forks copy of your current dev branch and attempt to merge the
fork version in your local branch. The reason git is complaining is because the commit histories do not
line up after the rebase. This is expected because your rebase has *modified history*. Here, assuming 
you rebased correctly, you should do a `git push origin -f branch-name`. The `-f` flag overwrites the 
remote history with your local one. A safer way of doing this is to push your branch onto a new branch
with

    git push origin branch-name:new-branch-name

This avoids potentially losing information in the event that your rebase was incorrect. Just remove to
delete the branch with `git push origin :new-branch-name` afterwords or suffer branch pollution.

### I committed too early because I [forgot to ad a file]/[messed up the commit message]/[had a typo]/etc.

Simply do `git reset HEAD^`. This moves the `HEAD` pointer back one commit and moves the contents of 
that commit to the index. Then you are free to make additional changes and commit again.

### I completely messed up my local branch. I want it to look like upstream/branch or origin/branch.

    git checkout branch
    git reset --hard [upstream|origin]/branch

Part 6 - Where to go from here?
-------------------------------

The best thing you can do for yourself is to *learn to read the git help pages*. The built-in git
documentation is superb and highly readable. Good tools to read about first are

    git help commit
    git help rebase
    git help merge
    git help log
    git help show
    git help diff
    git help status

The last four commands are your 'eyes' when working on a git project. You should feel comfortable enough
to liberally use all of them constantly to ensure that git is doing what you think it should be doing.
Feel free to set up aliases and come up with good branch name and commit message conventions. Establishing
consistency in both will enhance your git vision when browsing the logs and lead to a faster solution.

Last but not least, continue to read and observe other git-masters at work. The workflow that I've 
described here is merely a starting point, but I'm certain many optimizations and changes could be made.
Most of all, have fun when you do it!

Cheers,
Jeremy
