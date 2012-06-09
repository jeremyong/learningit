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
+ Git offers *freedom* - That is, the freedom to try something daring without any real consequences (usually). The freedom to go back in time, all the way to the nascent of the project if you so choose. The freedom to work on 2834 independent features at once without a single one of them affecting the others.

If at any point, you shy away from taking advantage any of these points because what you want to do
seems too hard, remind yourself that in fact, what you want to do is **EASY** if only you knew how
to do it. All you need is more knowledge, and knowledge is power.

Part 2 - Basic terminology
--------------------------

Let's start with a simple repository with a single file. The file's contents are as follows

> Now is the time for all good men to come to
> the aid of their country. This is just a
> regular paragraph.
> 
> The quick brown fox jumped over the lazy
> dog's back.


