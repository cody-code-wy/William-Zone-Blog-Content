# Git Best Practices
-----

## Git commit messages

[Tpope Guide to git commit messages](http://tbaggery.com/2008/04/19/a-note-about-git-commit-messages.html)

### Commit Header

##### Informative summary

> First letter capitalized
>
> 50 chars max
>
> Imperative Tense
>
> No punctuation
 
### Commit Body

##### First line blank (After the header)

> 74 Chars per line
>
> Any Tense
 
 Answers
 
- What this changes
- Why this change is needed
- _Optional:_ Explain implementation (for odd code)
- _Optional:_ What did it do before (Breaking changes/fixes)
 
## Git Commits

- Does not break things
- Does not add incomplete features
- Does not use code that has not been commited
- Does not add code that is not being used

##### Better to have multiple commits, than a massive one

## Pushes

- Don't push until everything has been tested
- Don't push after every commit
- Only push when a logical feature is finished

Fixup commits should not be pushed (squash them)

##### Pushing makes changes harder

Force pushing is only acceptable on branches that you _own_ after all pull requests **with comments** have been **closed**

## Merges

- Only merge using Pull Requests
- All merges must be conflict free!
- Use rebase to remove conflicts

If a confict happens run `git merge --abort` to stop, and undo the merge


## Rebase
##### Changing History

**Any rebase on pushed changes will require a force push!**

### Interactive Rebase

`git rebase --interactive` (`grbi` for oh-my-zsh)
 
 To include the first commit `--root`
 
 Git rebase interactive allows you to manipulate commits on the current branch.
 
- `p` `pick` Use the commit normally (no changes)
- `s` `squash` Combine with the previous commit (one line above)
- `f` `fixup` Same as squash, but disregards commit message.
- `r` `reword` Change commit message
- `e` `edit` Ammend changes, and change commit message
- `d` `drop` Delete commit entierly

Use `git commit --fixup=<commit>` to tag commit as a fixup

### 'Merge' style rebase

#### Fixing merge conficts

1. Checkout branch that is failing to merge
2. run `git rebase <branch to merge to>`
 - This will add the history from branch you want to merge to into your current branch.
3. Fix conflict
4. Change files to correct version
5. Stage (`git add`) files
6. run `git rebase --continue` (`grbc` for oh-my-zsh)

The merge sholud now be possible

## Adding files

### Adding line by line (interactive add)

`git add -i` `git add --interactive`
 
Normally you want to use `patch` to selectively add parts from a file

- `y` Add hunk
- `n` Don't add hunk
- `q` Stop patching
- `s` Split hunk (not always available)
- `e` Edit hunk manually

#### Editing hunks manually

###### Removing added lines

To remove an add, just delete the line

###### Removing deleted lines

To remove a delete, change the `-` to ` ` (a **space**!)

----

# Rails Specific

## Generating Models

- Run migrations before commiting

## Fixing merge issues

### Schema.rb

The schema number will always cause a merge issue, the larger number is almost **always** the one that you want to keep
 