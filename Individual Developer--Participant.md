# Individual Developer -- Participant #

A developer working as a participant in a group project needs to learn how to communicate with others, and uses these commands in additioin to the ones needed by a standalone developer.

 - `git-clone` from the upstream to prime your local repository.
 - `git-pull` and `git-fetch` from "origin" to keep up-to-date with the upstream.
 - `git-push` to shared repository, if you adopt CVS style shared repository workflow.
 - `git-format-patch` to prepare e-mail submission, if you adopt Linux kernel-style public forum workflow.
 - `git-send-email` to send your e-mail submission without corruption by your MUA.
 - `git-request-pull` to create a summary of changes for your upstream to pull.



# Examples #

## checkout a new branch from a startpoint ##

> git checkout -b mine master


## create a patch ##

after some commits, you can creat a patch from this branch compare to another commit, like master:

> git format-patch master


## send email from git command ##
you can send email to someone and attach with a patch.

> git send-email --to="person <email@example.com>" 00*.patch


## see what happened after a pull ##
Immediately after pulling, look at the changes doen upstream since last time we checked, only in the area we are interested in, in this case, we are only interested in directories `arch/i386` and `include/asm-i386`.

> git pull

> git log -p AHEAD.. arch/i386 include/asm-i386


## revert the pull and garbage collect ##
Revert the pull and garbage collect leftover objects from reverted pull.

> git reset --hard ORIG_HEAD

> git gc

