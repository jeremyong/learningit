learningit
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
> the aid of their country. This is just an
> awesome paragraph!
> 
> The quick brown fox jumped over the lazy
> dog's back.

After saving the file locally in your favorite editor, git updates the **index** of your project.
Your **working tree** at this point is just *A*. Right now the state of the repository looks
like:

`   master: A------A'`
`         (WT)   (index)`

If we now perform a `git add file.txt` followed with a subsequent `git commit`, the changes to file.txt
are now recorded and the state of the repository looks like:

`   master: A------B`
`           (WT)`


Note that both `A` and `B` are now part of the current working tree (abbr. WT). What was called the *index*
before has been recorded by git as state `B`.

The `HEAD` in git nomenclature is the *location of the current reference*. After commiting our changes
to the file, the `HEAD` currently points to state `B`. If we were to do, say:

`git checkout (commit code for A)`

the file would look like what it was before our edit and the repository would look like

`   master: A------B`
`           H`

We could have also arrived at this state by typing `git checkout HEAD^` (the caret means back one commit
from the reference name just to the left of it). Alternatively we could have done, `git checkout HEAD~1`,
where `ref~n` denotes the nth commit before ref.

The whole progression from `A` to `B` including any additional changes we might make (the new index built
on top of B) constitutes a git **branch**. Git branches include the working tree and the index. We can
make a new branch by doing something like

`git checkout -b cool-branch`

`   cool-branch:    ?`
`                  /`
`   master:      A------B`

Note that the working tree of `cool-branch` contains `A` because we had moved `HEAD` to point to `A`.
Running `git checkout -b branch-name` creates a new branch off of the current location of `HEAD`. If we now
edit the file, it will look initially like:

> Now is the time for all good men to come to
> the aid of their country. This is just a
> regular paragraph.
> 
> The quick brown fox jumped over the lazy
> dog's back.

because the working tree of cool-branch contains state `A` and no commits have been made since then.
To conclude this terminology second, let's edit the second paragraph and make a commit:

> Now is the time for all good men to come to
> the aid of their country. This is just a
> regular paragraph.
> 
> The quick brown *velociraptor* jumped over the lazy
> dog's back.

`git add file.txt`
`git commit -m 'Velociraptors are awesome!'`

`   cool-branch:    C`
`                  /`
`   master:       A------B`

Note that `HEAD` now points to state `C` on `cool-branch`. Note also that the new commit *is not named
`B` despite its previous commit being `A`*. Commit names are unique in the entire repository regardless
of what branch it was committed to.
