# Git

**Git** 是一个免费的开源**分布式****版本控制系统**（**VCS**），旨在以*快速*、*高效*的方式处理从小型到大型项目的一切事务。
实际中，它用于*跟踪文件的更改*，通常用于在软件开发期间协调程序员之间协作开发源代码的工作（*SCM*）。

![Git Logo](https://dookbook.info/media/git.png)

Git 由 *Linus Torvalds* 于 *2005年* 创建，用于开发 Linux 内核，其他内核开发人员也参与了它的初始开发。
自2005年以来，*Junio Hamano* 一直是核心维护者。

## 特性 (Features)

### 分支和合并 (Branching and Merging)

Git 的**分支模型**让它真正区别于几乎所有其他 SCM。

Git 允许并鼓励您拥有多个彼此完全独立的本地分支。创建、合并和删除这些开发线仅需要几秒钟。

这意味着你可以做以下事情：

- **无障碍上下文切换**。创建一个分支来尝试一个想法，提交几次，切换回您的分支来源，应用补丁，切换回您正在试验的地方，并将其合并。

- **基于角色的代码线**。有一个分支总是只包含用于生产的内容，另一个分支是用于测试的合并工作，还有几个较小的分支用于日常工作。

- **基于特征的工作流**。为正在处理的每个新功能创建新分支，以便可以在它们之间无缝切换，然后在该功能合并到主线中时删除每个分支。

- **一次性实验**。创建一个分支进行实验，意识到它不起作用，然后删除它 —— 在没有人看到它的情况下放弃这项工作（即使你同时推动了其他分支）。

![Git 分支图例](https://dookbook.info/media/git-branches@2x-without-alpha.png)

值得注意的是，当您推送到远程存储库时，不必推送所有分支。您可以选择只共享一个分支、几个分支或全部分支。
这使得人们可以自由地尝试新想法，而不必担心如何以及何时将其融入或与他人分享。

### 小而快 (Small and Fast)

Git 是为了在 Linux 内核上工作而构建的，这意味着它从一开始就必须有效地处理大型存储库。
Git 是用 *C* 编写的，减少了与高级语言相关的运行时开销。**速度**和**性能**从一开始就是 Git 的主要设计目标。

**Git很快**。有了Git，几乎所有的操作都在本地执行，这使得它在需要经常与某个地方的服务器通信的集中式系统上具有巨大的速度优势。

Git 和其它版本控制系统的主要差别在于 Git 对待数据的方式。从概念上来说，其它大部分系统以文件变更列表的方式存储信息，这类系统通常称作 **基于差异**（**delta-based**）的版本控制。
而 Git 是直接记录**快照** (**snapshots**)，而非 *差异比较* (*differences*)。Git 对待数据更像是一个 **快照流** (**stream of snapshots**)。

### 分布式 (Distributed)

包括 Git 在内的任何分布式 SCM 最优秀的特性之一就是它是分布式的。这意味着，您不必“签出 (checkout)”当前的源代码提示，而是“克隆 (clone)”整个存储库。

#### 多重备份 (Multiple Backups)

这意味着，即使您使用的是集中式工作流，每个用户基本上都有主服务器的完整备份。在崩溃或损坏的情况下，这些副本中的每一个都可以向上推，以替换主服务器。
实际上，Git 没有单点故障，除非只有存储库的一个副本。

#### 任意工作流 (Any Workflow)

由于 Git 的分布式特性和出色的分支系统，可以相对轻松地实现几乎无穷无尽的工作流。

##### Subversion 风格的工作流

集中式工作流非常常见，尤其是从集中式系统过渡的人。如果自上次获取后有人推送，Git 将不允许您推送，因此所有开发人员推送到同一服务器的集中式模型可以正常工作。

![Subversion 风格的工作流](https://dookbook.info/media/git-workflow-svn-style@2x-without-alpha.png)

##### 集成管理者工作流 (Integration Manager Workflow)

另一个常见的 Git 工作流涉及集成管理者 -- 许多开发人员从该存储库进行克隆，推送到他们自己的独立存储库，并要求集成人员合并他们的更改。这是开源或 GitHub 存储库中常见的开发模型类型。

![集成管理者工作流 (Integration Manager Workflow)](https://dookbook.info/media/git-workflow-b@2x-without-alpha.png)

##### 独裁者和副手工作流 (Dictator and Lieutenants Workflow)

对于更大规模的项目，像 Linux 内核那样的开发工作流通常是有效的。在这个模型中，一些人（“副手”）负责项目的一个特定子系统，他们合并了与该子系统相关的所有变更。
另一个整合者（“独裁者”）可以只从他/她的副手那里获取更改，然后推送到主存储库，然后每个人都可以再次从中克隆。

![独裁者和副手工作流 (Dictator and Lieutenants Workflow)](https://dookbook.info/media/git-workflow-c@2x-without-alpha.png)

### 数据保障 (Data Assurance)

Git 使用的数据模型确保了项目每一部分的**加密完整性**。每个文件和提交都会进行*校验和*，并在签出 (checkout) 时通过其校验和进行检索。除了你输入的确切数据，你不可能从 Git 中得到任何东西。

在 Git 存储库中更改任何文件、日期、提交消息或任何其他数据时，如果不更改之后所有内容的ID，也是不可能的。
这意味着，如果您有一个提交ID，您不仅可以确保您的项目与提交时完全相同，而且它的历史记录中没有任何更改。

Git 用以计算校验和的机制叫做 **SHA-1 哈希 (hash)**。这是一个由 40 个十六进制字符（0-9 和 a-f）组成的字符串，基于 Git 中文件的内容或目录结构计算出来。

### 暂存区 (Staging Area)

与其他系统不同，Git 有一个叫做“**暂存区**”或“**索引**”的东西。这是一个中间区域，可以在完成提交之前对提交进行格式化和审查。

Git 与其他工具不同的一点是，它可以快速暂存一些文件并提交它们，而无需提交工作目录中所有其他修改过的文件，也无需在提交过程中在命令行上列出它们。

![Git 暂存区 (Staging Area)图示](https://dookbook.info/media/git-index@2x-without-alpha.png)

这允许您仅暂存修改文件的一部分。现在，您可以为当前提交准备所需的更改，并为下一次提交准备其他更改。此功能可根据需要对文件进行多次不同的更改。

当然，如果你不想要那种控制，Git 也很容易忽略这个特性 -- 只需在 **`commit`** 命令中添加一个 **`-a`**，就可以将所有文件的更改添加到暂存区 (staging area)。

![Git 跳过暂存区 (Staging Area)图示](https://dookbook.info/media/git-index-skip-without-alpha.png)

### 免费开源

Git 是在 **GNU通用公共许可证第2版** (**GPLv2**)下发布的，这是一个开源许可证。Git 项目选择使用 GPLv2 来保证您共享和更改自由软件的自由，以确保该软件对所有用户都是免费的。

## Git 生命周期

Git 有三种状态，你的文件可能处于其中之一：**已修改** (**modified**)、**已暂存** (**staged**) 和 **已提交** (**committed**)。

- 已修改表示修改了文件，但还没保存到数据库中。

- 已暂存表示对一个已修改文件的当前版本做了标记，使之包含在下次提交的快照中。

- 已提交表示数据已经安全地保存在本地数据库中。

![Git 生命周期的三种状态](https://dookbook.info/media/git-lifecycle-without-alpha.png)

这会让我们的 Git 项目拥有三个阶段：**工作区** (**working tree**)、**暂存区** (**staging area**) 和 **Git 目录** (**Git directory**)。

![Git 三个阶段](https://dookbook.info/media/git-three-sections-without-alpha.png)

Git 完整生命周期还有两种状态 **未跟踪** (**untracked**) 和 **未修改** (**unmodified**):

![Git 完整生命周期的五种状态](https://dookbook.info/media/git-lifecycle-all.jpg)

## Git 基本工作流程

基本的 Git 工作流程如下：

- 在工作区(working tree)中修改文件，放弃修改 (**`git restore`**)。

- 将你想要下次提交的更改选择性地暂存 (**`git add`**, **`git rm`**, **`git mv`**, **`git restore --staged`**)，这样只会将更改的部分添加到暂存区(staging area) 。

- 提交更新 (**`git commit`**)，找到暂存区的文件，将快照永久性存储到 Git 目录(Git directory, 或 .git directory)。

```bash
# 查看当前状态
git status
```

```bash
# 暂存工作区的修改
git add <file ... or directory>

# 删除工作区和暂存区的文件或目录
git rm <file ... or directory>

# 仅删除暂存区的文件或目录
git rm --cached <file ... or directory>

# 移动或重命名的文件、目录或符号链接
git mv <source> <destination>

# 提交已暂存的
git commit -m '<commit message>'

# 提交所有当前修改的
git commit -a -m '<commit message>'

# 修改已提交信息
git commit --amend
```

更多参见 <https://dookbook.info/>。
