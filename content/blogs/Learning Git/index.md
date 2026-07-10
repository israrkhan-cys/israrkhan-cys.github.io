---
title: "Learning Git and Github Guide"
date: 2026-06-13
draft: false
tags: ["Git", "GitHub", "Version Control", "Beginner"]
summary: "A practical guide to Git and GitHub covering everything from basic commands to branching, pull requests, and undoing mistakes."
coverImage: "cover.png"
---

## What is Git?
A version control system that helps track changes in code. Two main benefits:
1. Track full history of your project
2. Collaborate on large projects with others

---

## Initial Configuration
```bash
git config --global user.name "your-username"
git config --global user.email "your-email"
git config --list  # verify configuration
```
Two types of configuration: **local** (one repo) and **global** (all repos).
set global config so you dont have authenicate everytime.

---

## Core Commands

```bash
git clone "repo-link"        # copy a remote repo locally
git status                   # check current state of files
git add <filename>           # stage a specific file  
git add .                    # stage all files
git commit -m "message"      # save staged changes
git push -u origin main      # upload changes to remote repo
```

### Different File Status Types
| Status | Meaning |
|--------|---------|
| Untracked | New file Git doesn't know about |
| Modified | File has been changed |
| Staged | Ready to be committed |
| Unmodified | No changes |

---

## Starting From Scratch (No Remote Repo)
```bash
git init                          # make folder a git repo
git remote add origin "repo-link" # connect to remote repo
git remote -v                     # verify remote connection
git branch -M main                # rename branch to main
git push -u origin main           # first push
```

---

## Branching
```bash
git branch                    # check current branch
git branch -M main            # rename branch
git checkout <branch>         # switch to branch
git checkout -b <new-branch>  # create and switch to new branch
git branch -d <branch>        # delete a branch
git push origin <branch>      # push branch to remote
```

---

## Pull Requests
A PR lets you tell others about changes you've pushed to a branch. Then someone like senior manager or admin reviews your changes before merging into main. GitHub automatically checks for conflicts between branches and tell you if there is any conflicts.
Conflict: It occurs when there is different content on same file in different branches.

---

## Undoing Changes

```bash
# Case 1 — Unstage a file
git reset <filename>

# Case 2 — Undo last commit (keep changes)
git reset HEAD~1

# Case 3 — Undo multiple commits
git reset <commit-hash>         # keep changes
git reset --hard <commit-hash>  # discard changes permanently
```

---

## Forking
A fork is a copy of someone else's repo on your own GitHub account. You make changes, add features, fix bugs — then submit a Pull Request to contribute back to the original project.

---

## Quick Reference Card

| Command | What it does |
|---------|-------------|
| `git status` | Check file states |
| `git add .` | Stage everything |
| `git commit -m ""` | Save changes |
| `git push` | Upload to remote |
| `git pull` | Download from remote |
| `git log` | View commit history |
| `git diff` | See unstaged changes |
