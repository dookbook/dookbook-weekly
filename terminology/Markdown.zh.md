# Markdown

**Markdown** 是一种轻量级**标记语言**，用于使用*纯文本*编辑器创建格式化文本。
*John Gruber* 和 *Aaron Swartz* 于2004年创建了 Markdown，作为一种源代码
形式吸引人类读者的标记语言。Markdown 广泛用于博客、即时消息、在线论坛、协作软件、文档页面和自述文件。

由于 Markdown 的最初描述包含歧义和未回答的问题，因此多年来出现的实现有细微的差异，
许多实现都带有语法扩展。

Markdown 编写的文档的文件拓展名为`.md`或`.markdown`。

## 标准化

标记的特点是非正式规范[^1]和转换为 *HTML* 的参考实现。随着时间的推移，出现了许多实现。
人们开发这些功能的主要原因是需要在基本语法的基础上增加额外的功能，如表、脚注、
定义列表（技术上是 HTML 描述列表）和 HTML 块内的标记。其中一些的行为与参考实现不同。同时，
非正式规范中的一些含糊不清之处引起了人们的注意。这些问题促使创建了 *Babelmark* 等工具，
以比较各种实现的输出，以及一些开发人员为标准化而进行的开发解析器的努力。然而，
Gruber 认为完全标准化是错误的：“不同的网站（和人）有不同的需求。没有一种语法能让所有人都满意。”

[^1]: ["Markdown Syntax Documentation". Daring Fireball.](https://daringfireball.net/projects/markdown/syntax): "1"

## 标题语法

Markdown输入:

```markdown
# 一级标题

## 二级标题

### 三级标题
```

HTML输出:

```html
<h1>一级标题</h1>

<h2>二级标题</h2>

<h3>三级标题</h3>
```

## 段落语法

Markdown输入:

```markdown
段落1

段落2
```

HTML输出:

```html
<p>段落1</p>
<p>段落2</p>
```

## 列表语法

### 无序列表

演示示例：

- 列表项目 1
  - 列表项目 1.1
  - 列表项目 1.2
- 列表项目 2

Markdown输入:

```markdown
- 列表项目 1
  - 列表项目 1.1
  - 列表项目 1.2
- 列表项目 2
```

HTML输出:

```html
<ul>
  <li>列表项目 1</li>
  <ul>
    <li>列表项目 1.1</li>
    <li>列表项目 1.2</li>
  </ul>
  <li>列表项目 2</li>
</ul>
```

### 有序列表

演示示例：

1. 列表项目 1
2. 列表项目 2

Markdown输入：

```markdown
1. 列表项目 1
2. 列表项目 2
```

HTML输出：

```html
<ol>
  <li>列表项目1</li>
  <li>列表项目2</li>
</ol>
```

### 定义列表

演示示例：

Apple
:   Pomaceous fruit of plants of the genus Malus in
    the family Rosaceae.

Orange
:   The fruit of an evergreen tree of the genus Citrus.

Markdown输入：

```markdown
Apple
:   Pomaceous fruit of plants of the genus Malus in
    the family Rosaceae.

Orange
:   The fruit of an evergreen tree of the genus Citrus.
```

HTML输出：

```html
<dl>
  <dt>Apple</dt>
  <dd>Pomaceous fruit of plants of the genus Malus in the family Rosaceae.</dd>

  <dt>Orange</dt>
  <dd>The fruit of an evergreen tree of the genus Citrus.</dd>
</dl>
```

## 文本语法

### 强调文本

双下划线`__`不支持中文!

演示示例：

这是**强调文字**

Markdown输入：

```markdown
这是**强调文字**

这不支持中文__强调文字__
```

HTML输出：

```html
这是<strong>强调文字</strong>

这不支持中文<strong>强调文字</strong>
```

### 斜体文本

演示示例：

<!-- markdownlint-disable MD036 -->
*次强调文字， 斜体*

_这也是次强调文字，斜体_
<!-- markdownlint-enable MD036 -->

Markdown输入：

```markdown
*次强调文字， 斜体*

_这也是次强调文字，斜体_
```

HTML输出：

```html
<em>次强调文字， 斜体</em>

<em>这也是次强调文字，斜体</em>
```

### 删除文本

演示示例：

~~删除的文字~~

Markdown输入：

```markdown
~~删除的文字~~
```

HTML输出：

```html
<del>删除的文字</del>
```

### 插入文本

此为 *Dookbook Markdown* 语法。

演示示例：

++插入的文字++

Markdown输入：

```markdown
++插入的文字++
```

HTML输出：

```html
<ins>插入的文字</ins>
```

### 混合文本

此为 *Dookbook Markdown* 语法。

演示示例：

混合的文本**强调和 _斜体_**.

Markdown输入：

```markdown
混合的文本**强调和 _斜体_**.
```

HTML输出：

```html
混合的文本<strong>强调和 <em>斜体</em></strong>.
```

## 水平分割线语法

Markdown输入：

```markdown
***
```

HTML输出：

```html
<hr/>
```

## 链接语法

演示示例：

此处有[链接](https://dookbook.info/)。

此处有[链接](https://dookbook.info/ "Dookbook主页")。

Markdown输入：

```markdown
此处有[链接](https://dookbook.info/)。

此处有[链接](https://dookbook.info/ "Dookbook主页")。
```

HTML输出：

```html
此处有<a href="https://dookbook.info/">链接</a>。

此处有<a href="https://dookbook.info/" title="Dookbook主页">链接</a>。
```

## 图片语法

Markdown输入：

```markdown
![替换文本](URL)
```

HTML输出：

```html
<img src="URL" alt="替换文本"/>
```

## 行内代码语法

此为*标准 Markdown*语法。使用反引号(`` ` ``)。

*Markdown Lint Error*: 不允许代码块的前后包含空格，像：`` ` some text ` ``, `` `some text ` ``, `` ` some text` ``。正确的写法是，移除前后空格：`` `some text` ``。

演示示例：

这是一段文本`code here`这是另外一段文本

Markdown输入：

```markdown
这是一段文本`code here`这是另外一段文本
```

HTML输出：

```html
<p>这是一段文本<code>code here</code>这是另外一段文本</p>
```

### 嵌套反引号 (`` ` ``)

*反斜线(`\`)转义失效*: 反斜线(`\`)转义在嵌套的反引号(`` ` ``)这里失效。

Demo:

`` `code here``, ``code here` ``, `` ` ``

Markdown输入:

```markdown
`` `code here``, ``code here` ``, `` ` ``
```

HTML输出:

```html
<code>`code here</code>, <code>code here`</code>, <code>`</code>
```

## 引用块语法

演示示例：

As Kanye West said:

> We're living the future so
> the present is our past.

Markdown输入：

```markdown
As Kanye West said:

> We're living the future so
> the present is our past.
```

HTML输出：

```html
<p>As Kanye West said:</p>

<blockquote>We're living the future so
  the present is our past.</blockquote>
```

## 表格语法

### 原始表格

演示示例：

| 名称 | 描述 | 热度 |
| ------------- | --- | --- |
| 帮助 | 显示帮助窗口。| 5 |
| **关闭** | *关闭*一个`窗口` | 1000 |

Markdown输入：

```markdown
| 名称 | 描述 | 热度 |
| ------------- | --- | --- |
| 帮助 | 显示帮助窗口。| 5 |
| **关闭** | *关闭*一个`窗口` | 1000 |

<!-- 1. 至少需要3个短线或虚线分隔表格标题跟表格内容单元。-->

<!-- 2. 最外层的"|"是可选的。你也可以使用 Markdown 的行内元素。-->
```

HTML输出：

```html
<table>
  <thead>
    <tr>
      <th>名称</th>
      <th>描述</th>
      <th>热度</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>帮助</td>
      <td>显示帮助窗口。</td>
      <td>5</td>
    </tr>
    <tr>
      <td><strong>关闭</strong></td>
      <td><em>关闭</em>一个<code>窗口</code></td>
      <td>1000</td>
    </tr>
  </tbody>
</table>
```

### 列对齐

此为 *Dookbook Markdown* 语法。

演示示例：

| 名称 | 描述 (居中) | 热度 (右对齐) |
| ------------- | :---: | ---: |
| 帮助 | 显示帮助窗口。| 5 |
| **关闭** | *关闭*一个`窗口` | 1000 |

Markdown输入：

```markdown
<!-- 冒号可以用来对齐列：右对齐，居中 -->

| 名称 | 描述 (居中) | 热度 (右对齐) |
| ------------- | :---: | ---: |
| 帮助 | 显示帮助窗口。| 5 |
| **关闭** | *关闭*一个`窗口` | 1000 |
```

HTML输出：

```html
<table>
  <thead>
    <tr>
      <th>名称</th>
      <th align="center">描述 (居中)</th>
      <th align="right">热度 (右对齐)</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>帮助</td>
      <td align="center">显示帮助窗口。</td>
      <td align="right">5</td>
    </tr>
    <tr>
      <td><strong>关闭</strong></td>
      <td align="center"><em>关闭</em>一个<code>窗口</code></td>
      <td align="right">1000</td>
    </tr>
  </tbody>
</table>
```

## 缩写语法

此为 *Dookbook Markdown* 语法。

演示示例：

The HTML specification
is maintained by the W3C.

*[HTML]: Hyper Text Markup Language
*[W3C]: World Wide Web Consortium

Markdown输入：

```markdown
The HTML specification
is maintained by the W3C.

*[HTML]: Hyper Text Markup Language
*[W3C]: World Wide Web Consortium
```

HTML输出：

```html
<p>The <abbr title="Hyper Text Markup Language">HTML</abbr> specification
is maintained by the <abbr title="World Wide Web Consortium">W3C</abbr>.</p>
```

## 代码段语法

此为*Dookbook Markdown*语法。使用三个反引号(`` ``` ``)

演示示例：

```python
print ('Hello world')
```

Markdown输入：

<!-- markdownlint-disable MD031 -->
```markdown
```python
print ('Hello world')
``` // end
```
<!-- markdownlint-enable MD031 -->

HTML输出：

```html
<pre><code class="python">
print ('Hello world')
</code></pre>
```

*Dookbook Markdown*支持9种语言代码语法高亮：`HTML`/`XML`, `CSS`, `JavaScript`, `Python`, `Markdown`, `Bash`, `INI`/`TOML`。

### HTML

```html
<!DOCTYPE html>
<html>
<head>
  <title>Title</title>
  <style>body {width: 500px;}</style>
</head>
<body>
  <p checked class="title" id='title'>Title</p>
  <!-- here goes the rest of the page -->

  <script>
    function $init() {return true;}
  </script>
</body>
</html>
```

### XML

```xml
<xml>
  <tag1 attr="value">tag-1 text</tag>
</xml>
```

### CSS

```css
@font-face {
  font-family: Chunkfive; src: url('Chunkfive.otf');
}

body, .usertext {
  color: #F0F0F0; background: #600;
  font-family: Chunkfive, sans;
}

@import url(print.css);
@media print {
  a[href^=http]::after {
    content: attr(href)
  }
}
```

### JavaScript

*Dookbook Markdown*支持**ES6+**.

```javascript
function $initHighlight(block, cls) {
  try {
    if (cls.search(/\bno\-highlight\b/) != -1)
      return process(block, true, 0x0F) +
             ` class="${cls}"`;
  } catch (e) {
    /* handle exception */
  }
  for (var i = 0 / 2; i < classes.length; i++) {
    if (checkCondition(classes[i]) === undefined)
      console.log('undefined');
  }
}

export  $initHighlight;
```

### Python

```python
import sys

print('hello')
sys.exit()

@requires_authorization
def somefunc(param1='', param2=0):
    r'''A docstring'''
    if param1 > param2: # interesting
        print 'Gre\'ater'
    return (param2 - param1 + 1 + 0b10l) or None

class SomeClass:
    pass

>>> message = '''interpreter
... prompt'''
```

### Markdown <!-- markdownlint-disable MD024 -->

```markdown
## Heading 2

### Heading 3

A paragraph with a **strong** text and *italic* text.
```

### Bash

```bash
#!/bin/bash

###### CONFIG
ACCEPTED_HOSTS="/root/.hag_accepted.conf"
BE_VERBOSE=false

if [ "$UID" -ne 0 ]
then
 echo "Superuser rights required"
 exit 2
fi

genApacheConf(){
 echo -e "# Host ${HOME_DIR}$1/$2 :"
}
```

### INI/TOML

```toml
; boilerplate
[package]
name = "some_name"
authors = ["Author"]
description = "This is \
a description"

[[lib]]
name = ${NAME}
default = True
auto = no
counter = 1_000
```

### JSON

```json
{
  "a": 1,
  "b": null,
  "c": 1.2,
  "d": "string",
  "e": [1, 2],
  "f": {
    "g": 0,
    "h": "h"
  }
}
```

### HTTP

```http
GET https://dookbook.info/

POST /api/hello/?q=2
{
  "a": 1,
  "b": "string"
}
```

## 属性列表语法

此为 *Dookbook Markdown* 语法。

Markdown输入：

```markdown
## Attribute List {: #head-id}

Windows lets you perform most tasks directly from the keyboard. This page lists out the default bindings (keyboard shortcuts). Windows lets you perform most tasks directly from the keyboard. This page lists out the **default**{: attrname="value" } bindings (keyboard shortcuts).
{: #someid .someclass attrname='value' }
```

HTML输出：

```html
<h2 id="head-id">Attribute List</h2>
<p attrname="value" class="someclass" id="someid">Windows lets you perform most tasks directly from the keyboard. This page lists out the default bindings (keyboard shortcuts). Windows lets you perform most tasks directly from the keyboard. This page lists out the <strong attrname="value">default</strong> bindings (keyboard shortcuts).</p>
```

## 脚注语法

此为 *Dookbook Markdown* 语法。

演示示例：

Footnotes have a label[^label1] and the footnote's content[^label2].

[^label1]: A footnote on the label: "label1".
[^label2]: A footnote on the label: "label2".

Markdown输入：

```markdown
Footnotes have a label[^label1] and the footnote's content[^label2].

[^label1]: A footnote on the label: "label1".
[^label2]: A footnote on the label: "label2".

///Footnotes Go Here///
```

HTML输出：

```html
<p>Footnotes have a label<sup id="fnref:label1"><a class="footnote-ref" href="#fn:label1" rel="footnote">1</a></sup> and the footnote's content<sup id="fnref:label2"><a class="footnote-ref" href="#fn:label2" rel="footnote">2</a></sup>.</p>

<div class="footnote">
  <hr>
  <ol>
    <li id="fn:label1">
      <p>A footnote on the label: "label1".&#160;<a class="footnote-backref" href="#fnref:label1" rev="footnote" title="Jump back to footnote 1 in the text">&#8617;</a></p>
    </li>
    <li id="fn:label2">
      <p>A footnote on the label: "label2".&#160;<a class="footnote-backref" href="#fnref:label2" rev="footnote" title="Jump back to footnote 2 in the text">&#8617;</a></p>
    </li>
  </ol>
</div>
```

## 警示语法

此为*Dookbook Markdown*语法。

Markdown输入：

```markdown
!!! info "标题"
    **一般**级别的警示框。

!!! warn "标题"
    **警告**级别的警示框。

!!! error "标题"
    **错误**级别的警示框。

!!! error ""
    不带标题的警示框。
```

HTML输出：

```html
<div class="admonition info">
  <p class="admonition-title">标题</p>
  <p><strong>一般</strong>级别的警示框。</p>
</div>

<div class="admonition warn">
  <p class="admonition-title">标题</p>
  <p><strong>警告</strong>级别的警示框。</p>
</div>

<div class="admonition error">
  <p class="admonition-title">标题</p>
  <p><strong>错误</strong>级别的警示框。</p>
</div>

<div class="admonition error">
  <p>不带标题的警示框。</p>
</div>
```

## 上标语法

此为 *Dookbook Markdown* 语法。

演示示例：

2^10^=1024

Markdown输入：

```markdown
2^10^=1024
```

HTML输出：

```html
<p>2<sup>10</sup>=1024</p>
```

## 下标语法

此为 *Dookbook Markdown* 语法。

演示示例：

H~2~O = water

Markdown输入：

```markdown
H~2~O = water
```

HTML输出：

```html
<p>H<sub>2</sub>O = water</p>
```

## 元数据语法

此为 *Dookbook Markdown* 语法。

```markdown
LANGUAGE: zh-Hans
CATEGORY: terminology

# 标题1

body text here
```

## 任务清单语法

此为 *GitHub Flavored Markdown* 语法。

*Dookbook Markdown* 暂时不支持该语法。

演示示例：

- [x] @mentions, #refs, [links](URL), **formatting**, and ~~tags~~ are supported
- [x] 已完成
- [x] 已完成
- [ ] 未完成

Markdown输入：

```markdown
- [x] @mentions, #refs, [links](URL), **formatting**, and ~~tags~~ are supported
- [x] 已完成
- [x] 已完成
- [ ] 未完成
```

## 键盘输入语法

演示示例：

!!!Ctrl!!! + !!!C!!!

Markdown输入：

```markdown
!!!Ctrl!!! + !!!C!!!
```

HTML输出：

```html
<kbd>Ctrl</kbd> + <kbd>C</kbd>
```

///Footnotes Go Here///
