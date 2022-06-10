# Enumeration

## Basic Usage

```python
from enum import Enum

class Color(Enum):
    RED = 2  # Member values can be anything: int, str, etc..
    YELLOW = 6
    BLUE = 9
    GREEN = 20

# Using a descriptive string
class Color(Enum):
    RED = 'error'
    YELLOW = 'warning'
    BLUE = 'debug'
    GREEN = 'info'
```

An alternative creation method is described in Functional API:

```python
from enum import Enum

Color = Enum('Color', 'RED YELLOW BLUE GREEN')
Color = Enum('Color', 'RED, YELLOW, BLUE, GREEN')
Color = Enum('Color', ['RED', 'YELLOW', 'BLUE', 'GREEN'])
Color = Enum('Color', [('RED', 2), ('YELLOW', 6), ('BLUE', 9), ('GREEN', 20)])
Color = Enum('Color', {'RED': 2, 'YELLOW': 6, 'BLUE': 9, 'GREEN': 20})
```

## `Enum` Member

```python
>>> print(Color.RED)
Color.RED

>>> print(repr(Color.RED))
<Color.RED: 1>
```

`Enum` members have properties of **`name`** and **`value`**:

```python
>>> Color.RED.name
'RED'

>>> Color.RED.value
1
```

The *type* of an enumeration member is the enumeration it belongs to:

```python
>>> type(Color.RED)
<enum 'Color'>

>>> isinstance(Color.GREEN, Color)
True
```

Enumerations support iteration, in definition order:

```python
>>> for color in Color:
...     print(color)
...
Color.RED
Color.YELLOW
Color.BLUE
Color.GREEN
```

Enumeration members are hashable, so they can be used in dictionaries and sets:

```python
>>> apples = {}
>>> apples[Color.RED] = 'red delicious'
>>> apples[Color.GREEN] = 'granny smith'
>>> apples == {Color.RED: 'red delicious', Color.GREEN: 'granny smith'}
True
```

Having two enum members with the same name is invalid (**`TypeError`** raised):

```python
>>> class Color(Enum):
...     RED = 2
...     RED = 3
...

TypeError: Attempted to reuse key: 'RED'
```

## Ensuring unique enumeration values

Use **`@enum.unique`**：

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

## Using automatic values

If the exact value is unimportant you can use **`@enum.auto`**:

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

The values are chosen by **`_generate_next_value_()`**, which can be overridden:

```python
>>> class AutoName(Enum):
...     def _generate_next_value_(name, start, count, last_values):
...         """This method must be defined before any members."""
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

## Comparisons

Enumeration members are compared by identity:

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

Ordered comparisons between enumeration values are **not** supported.
`Enum` members are not integers:

```python
>>> Color.RED < Color.BLUE
TypeError: '<' not supported between instances of 'Color' and 'Color'
```

Comparisons against non-enumeration values will always compare not equal:

```python
>>> Color.BLUE == 2
False
```

## `Flag`

Like `IntFlag`, `Flag` members can be combined using the **bitwise** operators
(**`&`**, **`|`**, **`^`**, **`~`**).
Unlike `IntFlag`, they cannot be combined with,
nor compared against, any other `Flag` enumeration, nor `int`.

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

Members of an `IntEnum` can be compared to integers;
by extension, integer enumerations of different types can also be compared to each other:

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

However, they still can’t be compared to standard `Enum` enumerations:

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

The difference being `IntFlag` members can be combined using the **bitwise** operators
(**`&`**, **`|`**, **`^`**, **`~`**)
and the result is still an `IntFlag` member:

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

## References

- [Python Documentation - `enum`](https://dookbook.info/docs/python/lastest/en/library/enum.html#enum.IntEnum)
- [PEP 435 - Adding an Enum type to the Python standard library](https://peps.python.org/pep-0435/)
