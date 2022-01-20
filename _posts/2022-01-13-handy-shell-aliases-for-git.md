---
title: "Handy shell aliases and functions for Git"
categories:
- git
tags:
- shell-aliases
- git
---

Here are some shell aliases for Bash/Zsh and functions for Fish for automating some common Git operations.   

## Create New Branch

Performs the following steps:

1. Stash any existing changes, if any
2. Checkout the main or master branch, whichever a repo has
3. Pull the latest changes for main/master branch
4. Re-apply the stashed changes, if any
5. Add the changes, if any
6. Create a new branch, using the custom branch name supplied after the alias

Bash and Zsh alias:

```shell
alias gnb='(if [[ $(git status --porcelain --untracked-files=no | wc -l) -gt 0 ]]; then git stash && git checkout $(git remote show origin | awk '\''/HEAD branch/ {print $NF}'\'') && git pull && git stash apply && git add -u; else git checkout $(git remote show origin | awk '\''/HEAD branch/ {print $NF}'\'') && git pull; fi) && git checkout -B'
```

Fish function:

```shell
function gnb
    if test (git status --porcelain --untracked-files=no | wc -l) -gt 0
        git stash && git checkout (git remote show origin | awk '/HEAD branch/ {print $NF}') && git pull && git stash apply && git add -u && git checkout -B $argv
    else
        git checkout (git remote show origin | awk '/HEAD branch/ {print $NF}') && git pull && git checkout -B $argv
    end
end
```

Example usage:

```shell
# Create a new branch called New_branch_name, branched off the default branch (e.g. main or release), 
# with any uncommitted changes carried over
$ gnb New_branch_name
```

## Commit using Branch Name as Commit Message

Creates a commit, using the existing branch name as the commit message, with any spaces in the branch name
replaced with underscores.  Handy when the branch name is descriptive and can be used as the commit message.  

Bash and Zsh alias:

```shell
alias gcb='git commit -m "$(git symbolic-ref --short -q HEAD | sed -E '\''s:_: :g'\'')" && git log -1'
```

Fish function:

```shell
function gcb
    git commit -m (git symbolic-ref --short -q HEAD | sed -E 's:_: :g') $argv && git log -1
end
```

Example usage:

```shell
$ git checkout -B Some_great_new_feature

# Create a git commit with the commit message "Some great new feature"
$ gcb
```

## Git Push Branch

This alias will push the current local branch to the remote `origin` server.  It avoids having to run the 
`git push --set-upstream origin Some_new_Branch` command that Git asks you to run each time you push a new branch for 
the first time.  

Bash and Zsh alias:

```shell
alias gpb='git push --set-upstream origin $(git symbolic-ref --short -q HEAD)'
```

Fish function:

```shell
function gpb
    git push --set-upstream origin (git symbolic-ref --short -q HEAD)
end
```

Example usage:

```shell
# Perform a Git push to origin, using the local branch name as the remove branch name
$ gpb

# Force push
$ gpb -f
```

## Using these aliases/functions

To use these aliases/functions, copy them into one of the following files, depending on which shell you are using.  

| Shell | File | Notes |
|---|---|---|
| Bash | ~/.bash_profile | |
| Zsh (Z shell) | ~/.zshrc | |
| Fish | ~/.config/fish/functions/`{functionName}`.fish | You will need to create a separate file for each function. Replace `{functionName}` with the name of the function |

You can find out which shell you are using by running:

```shell
$ echo $SHELL
```
