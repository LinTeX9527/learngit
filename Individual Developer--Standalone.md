# giteveryday #

A standalone individual developer does not exchange pathces with other people, and works alone in a single repository, using the following commands.

 - `git-init` to create a new repository.
 - `git-log` to see what happened.
 - `git-checkout` and `git-branch` to switch branches.
 - `git-add` to manage the index file.
 - `git-diff` and `git-status` to see what you are in the middle of doing.
 - `git-commit` to advance the current branch.
 - `git-reset` and `git-checkout` (with pathname parameters) to undo changes.
 - `git-merge` to merge between local branches.
 - `git-rebase` to maintain topic branches.
 - `git-tag` to mark a known point.

# tag create #
After a commit, using following commands to create a lightweight, unannotated tag:

> git tag v2.43


# modify last commit message #

> git commit --amend


# limit output of `git log` #

> git log --since='3 days ago'

> git log --until=2005-12-10


