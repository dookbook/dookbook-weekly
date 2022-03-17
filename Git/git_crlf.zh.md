# Git 处理行尾 CRLF

## 行尾问题

不同系统间编程协作，经常会出现 **行尾问题**。因为 Windows 环境下文本文件的行末尾是*回车符* (*carriage-return character*) 和*换行符* (*linefeed character*) ，即 *CRLF* (*`/r/n`*)；而 Mac，Linux，Unix 环境下文本文件的行末尾是*换行符* (*linefeed character*)，即 *LF* (*`\n`*)。详情可参见 [CRLF](https://dookbook.info/content/6231fc5f4d9f1a1af78d90ff/)。
同时，Windows 的许多编辑器还悄悄地将 LF 修改成了 CRLF 格式的行结束符，或者在你敲回车的时候，CRLF 格式的行结束符就自动生成了。当然，这一切都发生在同时存在 Windows 和非 Windows 的跨平台工作中，如果大家都是同一种操作系统，就没有这个问题了。

^M 出现的原因：在 Linux 或 Mac OSX 下打开 Windows 编辑过的文件，就会在行末尾显示 **`^M`**。

Windows 环境：

```bash
$ git add somefile
warning: LF will be replaced by CRLF in somefile.
The file will have its original line endings in your working directory.
```

## 解决方案

当添加到暂存区 (`git add`) 时，Git 会自动将 *CRLF* 转换成 *LF*；反之，当签出 (checkout) 时，自动将 *LF* 转换成 *CRLF*。

如果是在 **Windows** 系统上，把 *`core.autocrlf`* 设置成 **`true`**，这样在签出 (checkout) 代码时，换行 (*LF*) 会被转换成回车和换行 (*CRLF*)：

```bash
# Checkout Windows风格，commit Unix风格
git config --global core.autocrlf true
```

如果是在 **Linux**，**UNIX** 或 **Mac** 系统上，你不需要 Git 在签出 (checkout) 文件时进行自动转换；
然而当一个以*回车加换行* (*CRLF*) 作为行结束符的文件不小心被引入时，你肯定想让 Git 修正。
你可以把 *`core.autocrlf`* 设置成 **`input`** 来告诉 Git 在提交时把回车和换行 (*CRLF*) 转换成换行符 (*LF*)，签出 (checkout) 时不转换：

```bash
# Checkout 不变，commit Unix风格
git config --global core.autocrlf input
```

如果你是 Windows 程序员，且遇到仅在 Windows 上开发的项目，可以设置成 **`false`** 取消此功能，把回车 (*CR*) 保留在版本库中：

```bash
# Checkout 不变，commit 不变
git config core.autocrlf false
```

注意：`core.autocrlf` 不会影响存储库中已有的内容。如果你之前提交过 CRLF 结尾的文件，它们将继续保留。这是避免依赖 `core.autocrlf` 的一个很好的理由；如果一个用户没有设置它，他们可以将带有 CRLF 结尾的内容带入到存储库中，且会一直存在。

## CRLF 安全检测

CRLF 转换有轻微损坏数据的可能性。当 *`core.autocrlf`* 启用后，Git 将在提交期间将 CRLF 转换为 LF，在签出期间将 LF 转换为 CRLF。
Git 无法重新创建在提交之前混合包含 LF 和 CRLF 的文件。对于*文本文件*来说，这是正确的做法：它会更正行尾，这样存储库中就只有LF行尾了。但对于意外归类为文本的*二进制文件*，转换可能会损坏数据。

不幸的是，无法区分清理具有混合行结尾的文本文件的预期效果和破坏二进制文件的预期效果。在这两种情况下，CRLF 都以**不可逆**的方式被去除。
对于文本文件来说，这样做是正确的，因为 CRLF 是行尾；而对于二进制文件来说，转换 CRLF 会**破坏数据**。

**`core.safecrlf`** 只是一种基本确保不会对二进制文件不可逆转地执行 CRLF 转换的方法。

当 *`core.autocrlf`* 是开启 (*`true`* 或 *`input`*) 状态时：

```bash
git config core.safecrlf true
```

Git 会检测 CRLF 转换是否可逆。Git 会检测工作区的文件是否被修改，无论是直接还是间接的。
比如，先提交一个文件，然后再签出同样的文件，必须能够在工作区还原原先被提交的那个文件。如果不符合 *`core.autocrlf`* 设置的策略，Git 会拒绝该文件，并报错：

```bash
$ git add somefile
fatal: CRLF would be replaced by LF in somefile.
```

当设置为 *`warn`* 时，Git只会对不可逆转换发出警告，但会继续操作：

```bash
$ git config core.safecrlf warn

$ git add somefile
warn: CRLF would be replaced by LF in somefile.
```

## 批量转换 LF -> CRLF

### 安装工具

```bash
# MacOS
brew install dos2unix

# Linux (Ubuntu)
apt install dos2unix

# Linux (RHEL)
yum install dos2unix
```

Windows 的 MINGW64 Git Bash 客户端自带 dos2unix。

### 开始转换

以下命令可以将当前文件夹内的文件批量的转换

```bash
find . -type f -print0 | xargs -0 dos2unix
```

### 刷新存储库

```bash
git add --renormalize .
git commit -m "Normalize all the line endings"
```

## 按存储库配置：Git 属性 (Attributes)

您可以配置 **`.gitattributes`** 文件来管理 Git 如何读取特定存储库中的行结束符。将此文件提交到仓库时，它将覆盖所有仓库贡献者的 *`core.autocrlf`* 设置。
这可确保所有用户的行为一致，而不管其 Git 设置和环境如何。

`.gitattributes` 文件必须在存储库的*根目录*下创建，且像任何其他文件一样提交。

`.gitattributes` 文件看上去像一个两列表格。

- 左侧是 Git 要匹配的文件名。
- 右侧是 Git 应对这些文件使用的行结束符配置。

### `.gitattributes` 示例

以下是 .gitattributes 文件示例。 您可以将其用作仓库的模板：

```ini
# Set the default behavior, in case people don't have core.autocrlf set.
* text=auto

# Explicitly declare text files you want to always be normalized and converted
# to native line endings on checkout.
*.c text
*.h text

# Declare files that will always have CRLF line endings on checkout.
*.sln text eol=crlf

# Denote all files that are truly binary and should not be modified.
*.png binary
*.jpg binary
```

我们将在下面介绍一些可能的设置。

- `text=auto` Git 将以其认为最佳的方式处理文件。 这是一个合适的默认选项。

- `text eol=crlf` 在检出时 Git 将始终把行结束符转换为 CRLF。 您应将其用于必须保持 CRLF 结束符的文件，即使在 Mac OSX 或 Linux 上。

- `text eol=lf` 在检出时 Git 将始终把行结束符转换为 LF。 您应将其用于必须保持 LF 结束符的文件，即使在 Windows 上。

- `binary` Git 会理解指定文件不是文本，并且不应尝试更改这些文件。 `binary` 设置也是 `-text -diff` 的一个别名。

## 指定行尾 `core.eol`

```bash
git config core.eol lf      # 指定行尾 LF
git config core.eol crlf    # 指定行尾 CRLF
git config core.eol native  # 行尾随本地系统（默认）
```

*[CR]: Carriage Return
*[LF]: Line Feed
