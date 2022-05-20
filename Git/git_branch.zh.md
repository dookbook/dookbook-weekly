# Git 分支 (Branch)

## 查看分支

查看**本地分支** (**`-l`**/**`--list`** 命令行参数，默认参数)

```bash
$ git branch
* main
develop
```

输出中 *`*`* 指当前分支，且用*绿色*标出。

查看**远程分支** (**`-r`**/**`--remotes`** 命令行参数)

```bash
$ git branch -r
origin/HEAD -> origin/main
origin/main
upstream/main
```

查看**本地和远程的所有分支** (**`-a`**/**`--all`** 命令行参数)

```bash
$ git branch -a
* main
  develop
  remotes/origin/HEAD -> origin/main
  remotes/origin/main
  remotes/upstream/main
```

查看详细信息 (**`-v`**/**`--verbose`** 命令行参数)：

```bash
$ git branch -v
  iss53   93b412c Fix javascript issue
* master  7a98805 Merge branch 'iss53'
  testing 782fd34 Add scott to the author list in the readme
```

仅查看已合并到 `commit` (默认为 `HEAD`) 的分支 (**`--merged`** 命令行参数)：

```bash
git branch --merged [<commit>]
```

仅查看没有合并到 `commit` (默认为 `HEAD`) 的分支 (**`--no-merged`** 命令行参数)：

```bash
git branch --no-merged [<commit>]
```

## 创建新分支

```bash
git branch <new-branch>
```

在当前所在的提交对象上创建一个*指针*，即新分支。它仅仅创建一个新分支，并不会自动切换到新分支中去。

如果分支已存在，则会报错：

```bash
$ git branch a-branch
fatal: A branch named 'a-branch' already exists.
```

可以指定起点 (start point) 创建新分支：

```bash
git branch <new-branch> <start-point>
```

`start-point` 可以是*分支名* (*branch name*)、*commit id*、*标签名* (*tag name*)。

重置分支的起点 (**`-f`**/**`--force`** 命令行参数)：

```bash
git branch -f <existed-branch> <start-point>
```

新建一个分支，与指定的远程分支建立追踪关系 (**`-t`**/**`--track`** 命令行参数)：

```bash
git branch --track <branch> <remote-branch>
```

## 切换分支

```bash
git switch <another-branch>
```

*`checkout`* 也可以切换分支：

```bash
git checkout <another-branch>
```

Git *2.23* 版本新增了 `switch` 命令，用来替代 `checkout` 命令中切换分支的功能。

如果分支不存在，则会报错：

```bash
$ git switch a-branch
fatal: invalid reference: a-branch
```

创建一个新分支并切换到该新分支 (**`-c`**/**`--create`** 命令行参数)：

```bash
git switch -c <new-branch>
```

等同于：

```bash
git branch <new-branch>
git switch <new-branch>
```

Git *2.23* 之前，使用 *`checkout -b`*：

```bash
git checkout -b <new-branch>
```

如果分支已存在，则会报错：

```bash
$ git switch -c a-branch
fatal: A branch named 'a-branch' already exists.
```

可以指定起点 (start point) 创建新分支：

```bash
git switch -c <new-branch> <start-point>
```

等同于：

```bash
git branch <new-branch> <start-point>
git switch <new-branch>
```

重置分支的起点 (**`-C`**/**`--force-create`** 命令行参数)：

```bash
git switch -C <new-branch> <start-point>
```

等同于：

```bash
git branch -f <new-branch> <start-point>
git switch <new-branch>
```

Git *2.23* 之前，使用 *`checkout -B`*：

```bash
git checkout -B <new-branch> <start-point>
```

创建新分支时，设置上游 (upstream) 的追踪模式 (**`-c`**/**`--create`** + **`-t`**/**`--track`**)：

```bash
git switch -c --track [direct|inherit] <new-branch>
```

Git *2.23* 之前，使用 *`checkout -b --track`*：

```bash
git checkout -b --track=[direct|inherit] <new-branch>
```

切换到上一个分支

```bash
git switch -
```

## 重命名分支

使用 **`-m`**/**`--move`** 命令行参数：

```bash
git branch -m <old-branch> <new-branch>
```

如果新命名的分支已存在，则会报错：

```bash
$ git branch -m a-branch b-branch
fatal: A branch named 'b-branch' already exists.
```

强制覆盖已存在的分支，使用 **`-M`** 命令行参数：

```bash
git branch -M <old-branch> <new-branch>
```

等同于：

```bash
git branch --move --force <old-branch> <new-branch>
```

## 复制分支

使用 **`-c`**/**`--copy`** 命令行参数：

```bash
git branch -c <old-branch> <new-branch>
```

如果新复制的分支名已存在，则会报错：

```bash
$ git branch -c a-branch b-branch
fatal: A branch named 'b-branch' already exists.
```

强制覆盖已存在的分支，使用 **`-C`** 命令行参数：

```bash
git branch -C <old-branch> <new-branch>
```

等同于：

```bash
git branch --copy --force <old-branch> <new-branch>
```

## 创建孤立 (orphan) 分支

**orphan branch** (**孤立分支**、**独立分支**、**空分支**)，该分支不继承任何提交，没有父节点，删除了所有跟踪的文件，是一个干净的分支 (**`-orphan`** 命令行参数)。

```bash
git branch --orphan <new-branch>
```

创建孤立分支，并切换到该分支：

```bash
git switch --orphan <new-branch>
```

Git *2.23* 之前，使用 *`checkout --orphan`*：

```bash
git checkout --orphan <new-branch>
```

## 删除分支

删除已经完全合并到上游分支，如果没有上游分支，则是合并到 `HEAD` 中 (**`-d`**/**`--delete`** 命令行参数)：

```bash
git branch -d <branch>    # fully merged branch
```

强制删除没有合并到上游或 `HEAD` 中的分支 (**`-D`** 命令行参数)：

```bash
git branch -D <branch>    # NOT merged branch
```

等同于：

```bash
git branch --delete --force <branch>
```

## 建立追踪关系

使用 **`-u`**/**`--set-upstream-to`** (*`--set-upstream`* 废弃) 命令行参数：

```bash
git branch --set-upstream-to=<remote-branch> <branch>
```

## 解除追踪关系

使用 **`--unset-upstream`** 命令行参数：

```bash
git branch --unset-upstream <branch>
```

## 合并分支

```bash
git merge <branch>
```

选择一个 commit，合并进当前分支：

```bash
git cherry-pick <commit>
```
