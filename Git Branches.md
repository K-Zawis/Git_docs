There are three main kinds of branches when working as a team in `git`:
- Master branch
- Staging branch
- Feature branch(es)

There is generally only one master and staging branch, while multiple feature branches may exist. They have different naming conventions but they should describe what they are for. For example:
- feat/user-authentication
- fix/landing-page-transition

Sometimes ticket IDs are used to easily link a feature to a ticket.
## Where am I?
To check where you currently are, you can use the following commands:
```git
git branch
git status
git log
```
`git status` shows your changed files in staged and unstaged mode $\textemdash$ get familiar with these terms $\textemdash$ while `git log` shows you the git history. 

`git reflog` may be used to see where you were before a certain git command that visually "removes" commits, like accidentally resetting to an older commit or rebase-ing wrongly. Then you can `git reset --hard` back to that ref to restore your previous state. Note, refs imply not just the commit but the entire history behind it.

## Branch Life-cycles
The Master and the Staging branches are only created once and stay as long as the project exists. Unlike feature branches which are only created for the period of time the feature is being developed. They get merged into the staging branch and finally the staging branch gets merged into the master branch for a new release of your application. 

The two essential commands to create a new branch or to check out an available branch:
```git
git checkout -b <branch_name>
git checkout <branch_name>
```
At the beginning of your project's life-cycle, someone has to set up master and staging branch with the necessary configuration (e.g. no force push on master/staging branches, PR templates, ...).

To retrieve a branch only available in the remote repository, for example when a team mate has created and pushed it but you do not have a local copy of it:
```git
git fetch
```
Essentially this keeps all the available branches in sync with your local copy. However this does not include commits which must be pulled.

To delete a branch you can either delete it locally or remotely:
```git
git branch -d <branch_name>
git push origin -d <branch_name>
```
Usually you would want to merge the branch into staging before deletion so make sure this is done before you delete locally and remotely.
## Feature Branch
First, you should check out your new feature branch with `git checkout -b <branch_name>`. Then to make commits follow the following pattern to make your changes available to everyone in the remote repository:
```git
git add .
git commit -m "<commit_message>"
git push origin <branch_name>
```
Note `git add .` moves *all* changed, added or deleted files to staging for the next commit. You can specify which files you may want to include in the commit by specifying flags and specific files:
```git
git add [--verbose | -v] [--dry-run | -n] [--force | -f] [--interactive | -i] [--patch | -p] 
		[--edit | -e] [--[no-]all | -A | --[no-]ignore-removal | [--update | -u]] [--sparse] 
		[--intent-to-add | -N] [--refresh] [--ignore-errors] [--ignore-missing] [--renormalize] [--chmod=(+|-)x] [--pathspec-from-file=<file> [--pathspec-file-nul]] 
		[--] [<pathspec>…​]
```

If unwanted files were added to the staging area, they can be unstaged (removed) by using:
```git
git reset HEAD <file_path>
```
This will keep the local changes in the file but remove them from the staging area, effectively preventing them from being included in the next commit.

The opposite can be done using:
```git
git checkout -- <file_path>
```
This will add an unstaged file to the staging area.

It is good practice to agree on a naming convention for your commit messages. There are different types, for example:
```
<type>(<which_file_or_domain>) <detailed_comment>
```
which can have the following types:
- feat - actual feature implementation
- style - code style and code clean up
- tests - actual test implementation 
- fix - bug fix
- refactor - refactor that doesn't effect the behaviour of the code
- chore - no production code changes, but more like configuration and setup

Using this naming convention would look something like this:
- feat(users) add authentication
- fix(logout) clean up cookie
- test(login) cookie set with access token
- style(\*) fix indentation
- chore(.gitignore) add .env file
## How to keep a branch up-to-date?
Before you start to update the branch, follow these optional steps:
- If the branch isn't available locally for you:
  1. `git fetch`
  2. `git checkout <branch_name>`
- If you have changed files:
  1. `git commit`
  2. or `git stash`, which is used to save un-committed changes and remove them from the working tree.


Now we can start pulling latest changes. Here we will be using a rebase which  will result in a linear and cleaner project history:
```git
git pull --rebase origin <branch_name>
```
In case of a merge conflict, we can manually edit the conflicted files to resolve the differences and continue on the command line in the following way:
```git
git add .
git rebase --continue
```
In case we encounter multiple conflicts by following the steps above until these are resolved.  However, if the rebase operation encounters significant issues or conflicts that cannot be resolved easily, we can abort the rebase using
```git
git rebase --abort
```

After the pull rebase finishes, all your commits should be listed on top of the remote branch's commits. If there were stashed changes before the rebase took place, they can be applied with `git stach apply`. Your branch should be up to date with the remote branch's changes and your own changes on top.
## How to keep a feature branch up-to-date with staging?
When other feature branches are merged into staging, we may want to keep your feature branch up-to-date. When should we do this?
- If we want to create a pull request of your feature branch to merge into staging, but all the recent changes from staging should also be included to reflect the latest changes but also to not run into merge conflicts.
- If we need to include an update from staging (e.g. hotfix, library update, dependent feature from someone else) to continue working on your feature branch without blocking issues.

We first must check out the feature branch
```git
git checkout <branch_name>
```
and follow the process in [[Git Branches#How to keep a branch up-to-date?|How to keep a branch up-to-date]]. 

Next we do the same for staging
```git
git checkout staging
```
and follow the process in [[Git Branches#How to keep a branch up-to-date?|How to keep a branch up-to-date]]. 

Followed by checking out your feature branch, rebase $\textemdash$ which will merge all your changes from the feature branch on top of the staging branch $\textemdash$ and push
```git
git checkout <branch_name>
git rebase staging
git push origin <branch_name>
```

Note, if you've previously pushed your branch to the remote repository and then rebased it with the staging branch locally, you'll need to force-push your changes. This is because when you rebase onto another branch, it alters the commit history of your branch, and thus when you try to push, git will reject it as it involves rewriting history, which in turn alters the commit structure. 
```git
git push -f origin <branch_name>
```
But be careful with a force push: If someone else made changes in between on this branch, a force push will forcefully overwrite all these changes.

## How to get a feature branch ready for merge?
A pull request will merge all your feature branch's changes into the staging branch. After which you may delete the feature branch both locally and remotely. It is not necessary to pull request to merge, but it is good practice as it enables other people to review your feature on platforms like Github or Gitlab. 

1. follow [[Git Branches#How to keep a feature branch up-to-date with staging?|How to keep a feature branch up-to-date with staging?]] 
2. Open a pull request for your feature branch
3. Wait for approval of team members
4. Optionally push more commits to your remote branch if changes are necessary after discussion

Then we may merge our pull request directly on the chosen platform (e.g. Github or Gitlab), or continue on the command line:
```git
git checkout staging
git merge --no-ff <branch_name>
git push origin staging
```
This process repeats for each feature branch. In case something goes wrong during the git workflow, it's worth checking out [this tutorial to revert almost anything with git.](https://github.blog/2015-06-08-how-to-undo-almost-anything-with-git/) 

## Keeping your git history tidy
By using rebasse, your changes will be applied on top of the changes of other team members changes. This means your history remains linear. However, to keep your *feature branches* tidy.

The following allow you to undo commits but want to keep this in history for documentation, reorder, rename, or squash commits into each other, if you want to append changes to your last commit and how to change the commit message of your last commit:
```git
git revert <commit_sha>
git rebase -i HEAD~<number_of_commits>
git commit --amend
git commit --amend -m "<commit_message>"
```
some of these commits will change your local commit history. If these have been pushed to the remote repository before, you will have to force push these changes. Just be mindful to not override a team members changes with force push. 