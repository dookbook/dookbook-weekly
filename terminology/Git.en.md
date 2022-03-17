# Git

**Git** is a free and open source **distributed** **version control system** (**VCS**) designed to handle everything from small to very large projects with *speed* and *efficiency*.
Actually, it is used for *tracking changes* in any set of files, usually used for coordinating work among programmers collaboritively developing source code during software development (*source code management system*, *SCM*).

![Git Logo](https://dookbook.info/media/git.png)

Git was created by *Linus Torvalds* in *2005* for development of the Linux kernel, with other kernel developers contributing to its initial development.
Since 2005, *Junio Hamano* has been the core maintainer.

## Features

### Branching and Merging

The Git feature that really makes it stand apart from nearly every other SCM out there is its **branching model**.

Git allows and encourages you to have multiple local branches that can be entirely independent of each other.
The creation, merging, and deletion of those lines of development takes seconds.

This means that you can do things like:

- **Frictionless Context Switching**. Create a branch to try out an idea, commit a few times, switch back to where you branched from, apply a patch, switch back to where you are experimenting, and merge it in.

- **Role-Based Codelines**. Have a branch that always contains only what goes to production, another that you merge work into for testing, and several smaller ones for day to day work.

- **Feature Based Workflow**. Create new branches for each new feature you're working on so you can seamlessly switch back and forth between them, then delete each branch when that feature gets merged into your main line.

- **Disposable Experimentation**. Create a branch to experiment in, realize it's not going to work, and just delete it - abandoning the work—with nobody else ever seeing it (even if you've pushed other branches in the meantime).

![Git Branches](https://dookbook.info/media/git-branches@2x-without-alpha.png)

Notably, when you push to a remote repository, you do not have to push all of your branches.
You can choose to share just one of your branches, a few of them, or all of them.
This tends to free people to try new ideas without worrying about having to plan how and when they are going to merge it in or share it with others.

There are ways to accomplish some of this with other systems, but the work involved is much more difficult and error-prone.
Git makes this process incredibly easy and it changes the way most developers work when they learn it.

### Small and Fast

Git was built to work on the Linux kernel, meaning that it has had to effectively handle large repositories from day one.
Git is written in *C*, reducing the overhead of runtimes associated with higher-level languages.
**Speed** and **performance** has been a primary design goal of Git from the start.

**Git is fast**. With Git, nearly all operations are performed locally, giving it a huge speed advantage on centralized systems that constantly have to communicate with a server somewhere.

The major difference between Git and any other VCS is the way Git thinks about its data. Conceptually, most other systems store information as a list of file-based changes.
These other systems think of the information they store as a set of files and the changes made to each file over time (this is commonly described as **delta-based** version control).
Instead, Git thinks of its data more like a series of **snapshots** of a miniature filesystem. Git thinks about its data more like a **stream of snapshots**.

### Distributed

One of the nicest features of any Distributed SCM, Git included, is that it's distributed.
This means that instead of doing a "checkout" of the current tip of the source code, you do a "clone" of the entire repository.

#### Multiple Backups

This means that even if you're using a centralized workflow, every user essentially has a full backup of the main server.
Each of these copies could be pushed up to replace the main server in the event of a crash or corruption.
In effect, there is no single point of failure with Git unless there is only a single copy of the repository.

#### Any Workflow

Because of Git's distributed nature and superb branching system, an almost endless number of workflows can be implemented with relative ease.

##### Subversion-Style Workflow

A centralized workflow is very common, especially from people transitioning from a centralized system.
Git will not allow you to push if someone has pushed since the last time you fetched, so a centralized model where all developers push to the same server works just fine.

![Subversion-Style Workflow](https://dookbook.info/media/git-workflow-svn-style@2x-without-alpha.png)

##### Integration Manager Workflow

Another common Git workflow involves an integration manager — a single person who commits to the 'blessed' repository.
A number of developers then clone from that repository, push to their own independent repositories, and ask the integrator to pull in their changes.
This is the type of development model often seen with open source or GitHub repositories.

![Integration Manager Workflow](https://dookbook.info/media/git-workflow-b@2x-without-alpha.png)

##### Dictator and Lieutenants Workflow

For more massive projects, a development workflow like that of the Linux kernel is often effective.
In this model, some people ('lieutenants') are in charge of a specific subsystem of the project and they merge in all changes related to that subsystem.
Another integrator (the 'dictator') can pull changes from only his/her lieutenants and then push to the 'blessed' repository that everyone then clones from again.

![Dictator and Lieutenants Workflow](https://dookbook.info/media/git-workflow-c@2x-without-alpha.png)

### Data Assurance

The data model that Git uses ensures the **cryptographic integrity** of every bit of your project.
Every file and commit is checksummed and retrieved by its *checksum* when checked back out.
It's impossible to get anything out of Git other than the exact bits you put in.

It is also impossible to change any file, date, commit message, or any other data in a Git repository without changing the IDs of everything after it.
This means that if you have a commit ID, you can be assured not only that your project is exactly the same as when it was committed, but that nothing in its history was changed.

The mechanism that Git uses for this checksumming is called a **SHA-1 hash**.
This is a 40-character string composed of hexadecimal characters (0–9 and a–f) and calculated based on the contents of a file or directory structure in Git.

### Staging Area

Unlike the other systems, Git has something called the "**staging area**" or "**index**".
This is an intermediate area where commits can be formatted and reviewed before completing the commit.

One thing that sets Git apart from other tools is that it's possible to quickly stage some of your files and commit them without committing all of the other modified files in your working directory or having to list them on the command line during the commit.

![Git Staging Area](https://dookbook.info/media/git-index@2x-without-alpha.png)

This allows you to stage only portions of a modified file.
Gone are the days of making two logically unrelated modifications to a file before you realized that you forgot to commit one of them.
Now you can just stage the change you need for the current commit and stage the other change for the next commit.
This feature scales up to as many different changes to your file as needed.

Of course, Git also makes it easy to ignore this feature if you don't want that kind of control — just add a **`-a`** to your **`commit`** command in order to add all changes to all files to the staging area.

![Git Skip Staging Area](https://dookbook.info/media/git-index-skip-without-alpha.png)

### Free and Open Source

Git is released under the **GNU General Public License version 2.0** (**GPLv2**), which is an open source license.
The Git project chose to use GPLv2 to guarantee your freedom to share and change free software --- to make sure the software is free for all its users.

## Git Lifecyle

Git has three main states that your files can reside in: **modified**, **staged**, and **committed**:

- Modified means that you have changed the file but have not committed it to your database yet.

- Staged means that you have marked a modified file in its current version to go into your next commit snapshot.

- Committed means that the data is safely stored in your local database.

![Git Lifecyle: The Three States](https://dookbook.info/media/git-lifecycle-without-alpha.png)

This leads us to the three main sections of a Git project: the **working tree**, the **staging area**, and the **Git directory**.

![Git Three Sections](https://dookbook.info/media/git-three-sections-without-alpha.png)

Git has two other states: **untracked** and **unmodified**:

![Git Lifecyle: The Five States](https://dookbook.info/media/git-lifecycle-all.jpg)

## Basic Git Workflow

The basic Git workflow goes something like this:

- You modify files in your working tree, or discard changes (**`git restore`**).

- You selectively stage (**`git add`**, **`git rm`**, **`git mv`**, **`git restore --staged`**) just those changes you want to be part of your next commit, which adds only those changes to the staging area.

- You do a commit (**`git commit`**), which takes the files as they are in the staging area and stores that snapshot permanently to your Git directory (.git directory).

```bash
# Show the working tree status
$ git status

# short status
$ git status --short
Or
$ git status -s
```

```bash
# stage modified files or directories from the working tree
git add <file ... or directory>

# remove files or directories from the working tree and from the index
git rm <file ... or directory>

# remove files or directories only from the index
git rm --cached <file ... or directory>

# rename or move a file or directory, or a symlink
git mv <source> <destination>

# commit staged files or directories to .git repository
git commit -m '<commit message>'

# commit all tracked and modified files or directories
git commit -a -m '<commit message>'
```

For more details to see <https://dookbook.info/>.
