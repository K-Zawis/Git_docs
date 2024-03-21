There are three main kinds of branches when working as a team in `git`:
- Master branch
- Staging branch
- Feature branch(es)

There is generally only one master and staging branch, while multiple feature branches may exist. They have different naming convetions but they should describe what they are for. For example:
- feat/user-authentication
- fix/landing-page-transition

Sometimes they use ticket IDs to easily link a feature to a ticket.
## Where am I?

To check where you currently are, you can use the following commands:
```git
git branch
git status
git log
```
`git status` shows your changed files in staged and unstaged mode $\textemdash$ get familiar with these terms $\textemdash$ while `git log` shows you the git history.