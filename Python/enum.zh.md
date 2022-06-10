# 枚举 (Enumeration)

## 基本用法

```python
from enum import Enum

class Color(Enum):
    RED = 2  # 成员值可以是 int、str
    YELLOW = 6
    BLUE = 9
    GREEN = 20

# 使用描述性字符串
class Color(Enum):
    RED = 'error'
    YELLOW = 'warning'
    BLUE = 'debug'
    GREEN = 'info'
```

也可以采用函数式 API (Functional API):

```python
from enum import Enum

Color = Enum('Color', 'RED YELLOW BLUE GREEN')
Color = Enum('Color', 'RED, YELLOW, BLUE, GREEN')
Color = Enum('Color', ['RED', 'YELLOW', 'BLUE', 'GREEN'])
Color = Enum('Color', [('RED', 2), ('YELLOW', 6), ('BLUE', 9), ('GREEN', 20)])
Color = Enum('Color', {'RED': 2, 'YELLOW': 6, 'BLUE': 9, 'GREEN': 20})
```

## 成员

```python
>>> print(Color.RED)
Color.RED

>>> print(repr(Color.RED))
<Color.RED: 1>
```

枚举成员包含 **`name`** 和 **`value`** 属性：

```python
>>> Color.RED.name
'RED'

>>> Color.RED.value
1
```

枚举成员类型:

```python
>>> type(Color.RED)
<enum 'Color'>

>>> isinstance(Color.GREEN, Color)
True
```

枚举成员按顺序迭代：

```python
>>> for color in Color:
...     print(color)
...
Color.RED
Color.YELLOW
Color.BLUE
Color.GREEN
```

枚举成员可哈希，可用于字典和集合：

```python
>>> apples = {}
>>> apples[Color.RED] = 'red delicious'
>>> apples[Color.GREEN] = 'granny smith'
>>> apples == {Color.RED: 'red delicious', Color.GREEN: 'granny smith'}
True
```

两个枚举成员的名称不能相同 (抛出 **`TypeError`**)：

```python
>>> class Color(Enum):
...     RED = 2
...     RED = 3
...

TypeError: Attempted to reuse key: 'RED'
```

## 确保唯一枚举值

使用 **`@enum.unique`**：

```python
>>> from enum import Enum, unique
>>> @unique
... class Color(Enum):
...     RED = 1
...     YELLOW = 2
...     BLUE = 3
...     GREEN = 3
...

ValueError: duplicate values found in <enum 'Color'>: GREEN -> BLUE
```

## 使用自动设定的值

如果确切的值不重要，你可以使用 **`@enum.auto`**:

```python
>>> from enum import Enum, auto
>>> class Color(Enum):
...     RED = auto()
...     BLUE = auto()
...     GREEN = auto()
...
>>> list(Color)
[<Color.RED: 1>, <Color.BLUE: 2>, <Color.GREEN: 3>]
```

值将由 **`_generate_next_value_()`** 来选择，该函数可以被重载:

```python
>>> class AutoName(Enum):
...     def _generate_next_value_(name, start, count, last_values):
...         """此方法定义必须在任何其他成员之前。"""
...         return name
...
>>> class Ordinal(AutoName):
...     NORTH = auto()
...     SOUTH = auto()
...     EAST = auto()
...     WEST = auto()
...
>>> list(Ordinal)
[<Ordinal.NORTH: 'NORTH'>, <Ordinal.SOUTH: 'SOUTH'>, <Ordinal.EAST: 'EAST'>, <Ordinal.WEST: 'WEST'>]
```

## 枚举成员比较

枚举成员是按标识号进行比较的:

```python
>>> Color.RED is Color.RED
True
>>> Color.RED == Color.RED
True

>>> Color.RED is Color.BLUE
False
>>> Color.RED == Color.BLUE
False

>>> Color.RED is not Color.BLUE
True
>>> Color.RED != Color.BLUE
True
```

枚举值之间的排序比较 **不被** 支持。 `Enum` 成员不属于整数：

```python
>>> Color.RED < Color.BLUE
TypeError: '<' not supported between instances of 'Color' and 'Color'
```

与非枚举值的比较将总是不相等:

```python
>>> Color.BLUE == 2
False
```

## `Flag`

`Flag` 成员可使用**按位**运算符 (**`&`**, **`|`**, **`^`**, **`~`**) 进行组合，
与 `IntFlag` 不同的是它们不可与任何其它 `Flag` 枚举或 `int` 进行组合或比较。

```python
from enum import Flag, auto

class Color(Flag):
    RED = auto()
    BLUE = auto()
    GREEN = auto()
```

```python
>>> Color.RED & Color.GREEN
<Color.0: 0>

>>> bool(Color.RED & Color.GREEN)
False
```

## `IntEnum`

`IntEnum` 成员可以与 `int` 类型数据比较：

```python
from enum import IntEnum

class Shape(IntEnum):
    CIRCLE = 1
    SQUARE = 2

class Request(IntEnum):
    POST = 1
    GET = 2
```

```python
>>> Shape == 1
False

>>> Shape.CIRCLE == 1
True

>>> Shape.CIRCLE == Request.POST
True
```

不过仍然不可与标准 `Enum` 枚举进行比较:

```python
from enum import Enum

class Color(Enum):
    RED = 1
    GREEN = 2
```

```python
>>> Shape.CIRCLE == Color.RED
False
```

## `IntFlag`

`IntFlag` 成员可使用**按位**运算符 (**`&`**, **`|`**, **`^`**, **`~`**) 进行组合且结果仍然为 `IntFlag` 成员：

```python
from enum import IntFlag

class Perm(IntFlag):
    R = 4
    W = 2
    X = 1
```

```python
>>> Perm.R | Perm.W
<Perm.R|W: 6>

>>> Perm.R + Perm.W
6

>>> RW = Perm.R | Perm.W
>>> Perm.R in RW
True
```

## 参考

- [Python 文档 - `enum`](https://dookbook.info/docs/python/lastest/zh-hans/library/enum.html#enum.IntEnum)
- [PEP 435 - Adding an Enum type to the Python standard library](https://peps.python.org/pep-0435/)
