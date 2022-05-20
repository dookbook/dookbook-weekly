# Git 空格错误 (Whitespace Errors)

默认情况下，*结尾的空格*（包括*仅由空格*组成的行）和在行的初始缩进中紧跟制表符的空格字符被视为**空格错误**，也称为**空格冲突**。

Git 通过设置 **`core.whitespace`** 预先定义了一些选项来探测和修正多余空白字符问题。它提供了六种处理多余空白字符的主要选项 —— 其中三个默认开启，另外三个默认关闭，不过你可以自由地设置它们。

默认被*打开*的三个选项是：

- **`blank-at-eol`**，*行尾* (*EOF*) 的空格；
- **`blank-at-eof`**，*文件末尾* (*EOL*) 的空行；
- **`space-before-tab`**，行开头制表符 tab 前面的空格;

**`trailing-space`**，涵盖了 *`blank-at-eol`* 和 *`blank-at-eof`*。

默认被*关闭*的三个选项是：

- **`indent-with-non-tab`**，以空格而非等效制表符 tab 缩进的行（你可以用 *`tabwidth`* 选项控制它）；
- **`tab-in-indent`**，在行开头表示缩进的制表符 tab；
- **`cr-at-eol`**，行尾的回车 (CR：*`\r`*)。

你可以让 Git 按照你的意图来打开或关闭以逗号分割的选项。
要想关闭某个选项，你可以在输入设置选项时不指定它或在它前面加个 **`-`**。

例如，如果你想要打开除 `space-before-tab` 之外的所有选项，那么可以这样：

```bash
git config --global core.whitespace \
    trailing-space,-space-before-tab,indent-with-non-tab,tab-in-indent,cr-at-eol
```

当你运行 *`git diff`* 命令并尝试给输出着色时，Git 将探测到这些问题，因此你在提交前就能修复它们。

## `git apply --whitespace` / `git config apply.whitespace`

用 *`git apply`* 打补丁时你也会从中受益。如果正准备应用的补丁存在空格错误，你可以拒绝应用补丁并且发出警告或错误：

```bash
git apply --whitespace=warn <patch>
或
git apply --whitespace=error <patch>      # 部分错误
或
git apply --whitespace=error-all <patch>  # 全部错误
```

或

```bash
git config apply.whitespace warn
或
git config apply.whitespace error
或
git config apply.whitespace error-all
```

或者在打上补丁前自动修正此问题：

```bash
git apply --whitespace=fix <patch>
```

或

```bash
git config apply.whitespace fix
```

或者干脆禁用或关闭错误信息：

```bash
git apply --whitespace=nowarn <patch>
```

或

```bash
git config apply.whitespace nowarn
```

## `git rebase`

这些选项也能运用于 *`git rebase`*。如果提交了有空白问题的文件，但还没推送到上游，你可以让 Git 在重写补丁时自动修正它们：

```bash
git rebase --whitespace=fix
```

更多参见 <https://dookbook.info/>。

*[EOL]: End of Line
*[EOF]: End of File
