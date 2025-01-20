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

## Basic Concepts of Git - 1

**Remote Git Repository**: Where the code is hosted (e.g., GitLab or GitHub)  
**Local Git Repository**: Local copy of the code on your machine  
**Git Client**: To connect and execute git commands (can be UI or Command Line Tool)

-   Code is fetched ("pulled") from remote repo and "pushed" to it

## Basic Concepts of Git - 2

-   Most of the time, Git knows how to merge changes automatically
-   "Merge Conflict" occurs when same line was changed (requires manual resolution)
-   Best Practice: Push and Pull often from remote repository to stay in sync
-   Note: Breaking changes don't affect you until you pull the new code

## Working with Git - 1

`git add <file>`:

-   To include the changes of a file into the next commit
-   Moves changes from "working directory" to "staging area"

`git commit -m "commit message"`:

-   Saves your changes in your local repository
-   Creates a new commit you can return to later

## Working with Git - 2

`git push <remote> <branch-name>`:

-   After committing, sends changes to remote Git server
-   Uploads your commits to the remote repo

## Setup Git Repository - 1

### 1) Remote Repository

Different Git Repositories to register:

-   Platforms that host your repository
-   Companies may have their own Git servers
-   Can be private (companies) or public (open source)
-   Many operations possible via platform UI

## Setup Git Repository - 2

### 2) Local Repository

Requirements:

-   Git client installed (UI or Command Line Tool)
-   [Installation Guide](https://git-scm.com/downloads)
-   [GUI Clients](https://git-scm.com/downloads/guis)

## Setup Git Repository - 3

### 2) Local Repository (continued)

Authentication:

-   Your public SSH Key must be added to remote platforms
-   Getting started guide available

## Setup Git Repository - 4

### 2) Local Repository (alternative)

For existing local projects:

-   Initialize git repository with `git init`
-   Then push to GitLab/GitHub/etc.

## Concept of Branches - 1

Purpose:

-   Better collaboration
-   "main" branch (formerly "master") created by default
-   Developers create temporary branches for features/bugfixes

Common branch names:

-   main, dev, feature, bugfix
-   Example: `bugfix/ticket-2134`

**Best Practice**: 1 branch per bugfix or feature

## Concept of Branches - 2

Branch workflow:

-   Branch starts from main branch codebase
-   When finished, branch is merged back to main
-   Goal: Maintain stable main branch ready for production

## Merge Requests or Pull Requests

**Best Practice**: Code reviews before merging

-   Request to merge one branch into another (usually main)
-   Reviewer can see changes and approve/decline

## Why to know Git as DevOps Engineer? - 1

### Use Case 1) Infrastructure as Code

-   Track configuration files and scripts
-   Securely store in remote repository
-   Enable team collaboration

### Use Case 2) Automation Scripts

-   Version control for Python/other automation scripts
-   Store in Git repository

## Why to know Git as DevOps Engineer? - 2

### Use Case 3) CI/CD Pipeline and Build Automation

-   CI requires checking out code from repository
-   Need to setup integration between build tool and git repo
-   Important git commands for:
    -   Getting commit hashes
    -   Checking change locations (frontend/backend)

## Best Practices - 1

### Commit-related:

-   Use descriptive commit messages
-   Commit in small chunks
-   Only commit related work
-   Configure proper authorship (name and email)

### Synchronization:

-   Keep branches up-to-date with remote
-   Pull often from remote repository
-   Merge main into feature branches regularly

## Best Practices - 2

### General:

-   Don't push directly to main branch
-   Use `--force` push carefully (avoid on shared branches)
-   Name branches with prefixes: `feature/xx`, `bugfix/xxx`
-   Conduct code reviews via Merge Requests
-   Use `.gitignore` for editor files/build folders
