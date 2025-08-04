# Version Control with GIT

## What is Version Control?

Also known as "source control"

-   Practice of tracking and managing changes to software code
-   Enables multiple people to simultaneously work on a single project

**Code Repository**

-   Code is hosted centrally on the internet
-   Every developer has an entire copy of the code locally

## Basic Concepts of Version Control

-   Version Control keeps a history of changes
-   Every code change and file is tracked!
-   You can revert commits
-   Each change labelled with commit message
-   Git is the most used version control system

## Basic Concepts of Git

| Component         | Description                              |
| ----------------- | ---------------------------------------- |
| Remote Repository | Hosted on platforms like GitHub/GitLab   |
| Local Repository  | Developer's machine copy                 |
| Git Client        | CLI or GUI interface to execute commands |

-   Code is fetched ("pulled") from remote repo and "pushed" to it
-   Most of the time, Git knows how to merge changes automatically
-   "Merge Conflict" occurs when same line was changed (requires manual resolution)
-   Best Practice: Push and Pull often from remote repository to stay in sync
-   Note: Breaking changes don't affect you until you pull the new code

## Working with Git

`git add <file>`:

-   To include the changes of a file into the next commit
-   Moves changes from "working directory" to "staging area"

`git commit -m "commit message"`:

-   Saves your changes in your local repository
-   Creates a new commit you can return to later

`git push <remote> <branch-name>`:

-   After committing, sends changes to remote Git server
-   Uploads your commits to the remote repo

## Setup Git Repository

### 1) Remote Repository

Different Git Repositories to register:

-   Platforms that host your repository
-   Companies may have their own Git servers
-   Can be private (companies) or public (open source)
-   Many operations possible via platform UI

### 2) Local Repository

Requirements:

-   Git client installed (UI or Command Line Tool)
-   [Installation Guide](https://git-scm.com/downloads)
-   [GUI Clients](https://git-scm.com/downloads/guis)

Authentication:

-   Your public SSH Key must be added to remote platforms
-   Getting started guide available

For existing local projects:

-   Initialize git repository with `git init`
-   Then push to GitLab/GitHub/etc.

## Concept of Branches

Purpose:

-   Better collaboration
-   "main" branch (formerly "master") created by default
-   Developers create temporary branches for features/bugfixes

Common branch names:

-   main, dev, feature, bugfix
-   Example: `bugfix/ticket-2134`

**Best Practice**: 1 branch per bugfix or feature

Branch workflow:

-   Branch starts from main branch codebase
-   When finished, branch is merged back to main
-   Goal: Maintain stable main branch ready for production

## Merge Requests or Pull Requests

**Best Practice**: Code reviews before merging

-   Request to merge one branch into another (usually main)
-   Reviewer can see changes and approve/decline

## Why to know Git as DevOps Engineer?

### Use Case 1) Infrastructure as Code

-   Track configuration files and scripts
-   Securely store in remote repository
-   Enable team collaboration

### Use Case 2) Automation Scripts

-   Version control for Python/other automation scripts
-   Store in Git repository

### Use Case 3) CI/CD Pipeline and Build Automation

-   CI requires checking out code from repository
-   Need to setup integration between build tool and git repo
-   Important git commands for:
    -   Getting commit hashes
    -   Checking change locations (frontend/backend)

## Best Practices

### Commit-related:

-   Use descriptive commit messages
-   Commit in small chunks
-   Only commit related work
-   Configure proper authorship (name and email)

### Synchronization:

-   Keep branches up-to-date with remote
-   Pull often from remote repository
-   Merge main into feature branches regularly

### General:

-   Don't push directly to main branch
-   Use `--force` push carefully (avoid on shared branches)
-   Name branches with prefixes: `feature/xx`, `bugfix/xxx`
-   Conduct code reviews via Merge Requests
-   Use `.gitignore` for editor files/build folders

### Git Flow Branching Model

> ![Git flow](https://github.com/user-attachments/assets/dd016e01-2042-45cd-855b-a397acb151e8)

> Source: [Git Flow](https://nvie.com/posts/a-successful-git-branching-model/)

