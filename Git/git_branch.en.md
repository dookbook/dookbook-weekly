# Git Branch

## List Branches

Existing **local branches** are listed (option **`-l`**/**`--list`**, default option):

```bash
$ git branch
* main
  develop
```

The current branch will be highlighted in *green* and marked with an *asterisk* (*`*`*)

**remote-tracking branches** are listed (option **`-r`**/**`--remotes`**):

```bash
$ git branch -r
origin/HEAD -> origin/main
origin/main
upstream/main
```

Show **both local and remote branches** (option **`-a`**/**`--all`**):

```bash
$ git branch -a
* main
  develop
  remotes/origin/HEAD -> origin/main
  remotes/origin/main
  remotes/upstream/main
```

Show sha1 and commit subject line for each head, along with relationship to upstream branch (if any) (option **`-v`**/**`--verbose`**):

```bash
$ git branch -v
  iss53   93b412c Fix javascript issue
* master  7a98805 Merge branch 'iss53'
  testing 782fd34 Add scott to the author list in the readme
```

Only list branches whose tips are reachable from the specified `commit` (`HEAD` if not specified) (option **`--merged`**):

```bash
git branch --merged [<commit>]
```

Only list branches whose tips are not reachable from the specified `commit` (`HEAD` if not specified) (option **`--no-merged`**):

```bash
git branch --no-merged [<commit>]
```

## Create a New Branch

```bash
git branch <new-branch>
```

This creates a new *pointer* to the same commit you’re currently on. The command only created a new branch — it didn’t switch to that branch.

If new branches already exists, Git will raise an error message:

```bash
$ git branch a-branch
fatal: A branch named 'a-branch' already exists.
```

Create a new branch to the `start-point` commit:

```bash
git branch <new-branch> <start-point>
```

`start-point` can be the *branch name*, *commit id*, *tag name*.

Reset `branchname` to `startpoint`, even if `branchname` exists already. Without **`-f`**/**`--force`**, `git branch` refuses to change an existing branch.

```bash
git branch -f <existed-branch> <start-point>
```

When creating a new branch, set up `branch.<name>.remote` and `branch.<name>.merge` configuration entries to set "upstream" tracking configuration for the new branch (using option **`-t`**/**`--track`**):

```bash
git branch --track <branch> <remote-branch>
```

## Switch Branches

```bash
git switch <another-branch>
```

*`checkout`* also can switch branches:

```bash
git checkout <another-branch>
```

Git *2.23* added the `switch` command to replace the feature of switching branches from `checkout`.

If branches to be switched not exists, Git will raise an error message:

```bash
$ git switch a-branch
fatal: invalid reference: a-branch
```

Create a new branch named `new-branch` before switching to the branch (using option **`-c`**/**`--create`**):

```bash
git switch -c <new-branch>
```

This is a convenient shortcut for:

```bash
git branch <new-branch>
git switch <new-branch>
```

Before Git *2.23*, use *`checkout -b`*：

```bash
git checkout -b <new-branch>
```

If the branch already exists, Git will raise an error message:

```bash
$ git switch -c a-branch
fatal: A branch named 'a-branch' already exists.
```

Create a new branch named `new-branch` starting at `start-point` before switching to the branch:

```bash
git switch -c <new-branch> <start-point>
```

`start-point` can be the *branch name*, *commit id*, *tag name*.

This is a convenient shortcut for:

```bash
git branch <new-branch> <start-point>
git switch <new-branch>
```

Similar to `--create` except that if `new-branch` already exists, it will be reset to `start-point` (using option **`-C`**/**`--force-create`**):

```bash
git switch -C <new-branch> <start-point>
```

This is a convenient shortcut for:

```bash
git branch -f <new-branch> <start-point>
git switch <new-branch>
```

Before Git *2.23*, use *`checkout -B`*：

```bash
git checkout -B <new-branch>
```

When creating a new branch, set up "upstream" configuration: (**`-c`**/**`--create`** + **`-t`**/**`--track`**)：

```bash
git switch -c --track [direct|inherit] <new-branch>
```

Before Git *2.23*, use *`checkout -b -t`*：

```bash
git checkout -b --track=[direct|inherit] <new-branch>
```

To switch back to the previous branch:

```bash
git switch -
```

## Rename Branches

Use option **`-m`**/**`--move`**:

```bash
git branch -m <old-branch> <new-branch>
```

If the newly named branch already exists, Git will raise an error message:

```bash
$ git branch -m a-branch b-branch
fatal: A branch named 'b-branch' already exists.
```

Allow renaming the branch even if the new branch name already exists, using option **`-M`**:

```bash
git branch -M <old-branch> <new-branch>
```

Shortcut for:

```bash
git branch --move --force <old-branch> <new-branch>
```

## Copy Branches

Use option **`-c`**/**`--copy`**:

```bash
git branch -c <old-branch> <new-branch>
```

If the newly copied branch already exists, Git will raise an error message:

```bash
$ git branch -c a-branch b-branch
fatal: A branch named 'b-branch' already exists.
```

Allow copying the branch even if the new branch name already exists, using option **`-C`**:

```bash
git branch -C <old-branch> <new-branch>
```

Shortcut for:

```bash
git branch --copy --force <old-branch> <new-branch>
```

## Create New orphan Branches

Create a new orphan branch, and all tracked files are removed, using option **`--orphan`**:

```bash
git branch --orphan <branch>
```

Create a new orphan branch, and switch to it:

```bash
git switch --orphan <new-branch>
```

Before Git *2.23*, use *`checkout --orphan`*：

```bash
git checkout --orphan <new-branch>
```

## Delete Branches

Delete a branch. The branch must be fully merged in its upstream branch, or in `HEAD` if no upstream was set with `--track` or `--set-upstream-to` (using option **`-d`**/**`--delete`**):

```bash
git branch -d <branch>    # fully merged branch
```

Force to delete a branch which is not fully merged in its upstream branch, or not in `HEAD` if no upstream was set (using option **`-D`**):

```bash
git branch -D <branch>    # NOT merged branch
```

Shortcut for:

```bash
git branch --delete --force <branch>
```

## Set Up Upstream Tracking

Use option **`-u`**/**`--set-upstream-to`** (*`--set-upstream`* 废弃):

```bash
git branch --set-upstream-to=<remote-branch> <branch>
```

## Remove Upstream Tracking

Use option **`--unset-upstream`**:

```bash
git branch --unset-upstream <branch>
```

## Merge Branches

```bash
git merge <branch>
```

Merge a specific commit into `branch`:

```bash
git cherry-pick <commit>
```
