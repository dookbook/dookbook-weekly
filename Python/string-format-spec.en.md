# String Format Specification

**Format specifications** are used within replacement fields contained within a format string to define how individual values are presented.

## Alignment

| Option | Meaning |
| :---: | :--- |
| `<` | left aligned (default for most objects) |
| `>` | right aligned (default for numbers) |
| `>` | centered |
| `=` | Forces the padding to be placed after the sign (if any) but before the digits. This is used for printing fields in the form `+000000120`. This alignment option is only valid for **numeric types**. It becomes the default when `0` immediately precedes the field width. |

```python
>>> text = 'AAA'

# <: lefi aligned
>>> f'{text:f<5}'
'AAAff'

# >: right aligned
>>> f'{text:f>5}'
'ffAAA'

# ^: centered
>>> f'{text:f^5}'
'fAAAf'
>>> f'{text:f^6}'
'fAAAff'

# =: only for numeric types, otherwise raise ValueError exception
>>> f'{text:f=6}'
ValueError: '=' alignment not allowed in string format specifier
>>> i = 6
>>> f'{i:0=5}'
'00006'
```

## Sign

!!! warn ""
    Only for numeric types.

| Option | Meaning |
| :---: | --- |
| `+` | indicates that a sign should be used for both positive as well as negative numbers. |
| `-` | indicates that a sign should be used only for negative numbers (this is the default behavior). |
| (Space) | indicates that a leading space should be used on positive numbers, and a minus sign on negative numbers. |

```python
>>> i = 6
>>> j = -2

>>> f'{i:0=5}, {j:0=5}'
'00006, -0002'

>>> f'{i:0=+5}, {j:0=+5}'
'+0006, -0002'

>>> f'{i:0=-5}, {j:0=-5}'
'00006, -0002'

>>> f'{i:0= 5}, {j:0= 5}'
' 0006, -0002'
```

## Integer Format

**`d`** (decimal format) / **`n`** (decimal formar with current locale setting), **`b`** (binary format), **`o`** (octal format), **`x`** (hex format using lower-case letter) / **`X`** (hex format using upper-case letter):

```python
>>> i = 666666
>>> f'{i:d}, {i:n}, {i:b}, {i:o}, {i:x}, {i:X}'
'666666, 666666, 10100010110000101010, 2426052, a2c2a, A2C2A'
```

### Prefix: `#`

```python
>>> i = 666666
>>> f'{i:#d}, {i:#n}, {i:#b}, {i:#o}, {i:#x}, {i:#X}'
'666666, 666666, 0b10100010110000101010, 0o2426052, 0xa2c2a, 0XA2C2A'
```

### Thousand Seperator: `,` / `_`

Use of a **comma** (**`,`**) for a **thousands** (decimal format):

```python
>>> i = 666666
>>> f'{i:#,d}'
'666,666'
```

Use of a **underscore** (**`_`**) for a **thousands** (binary, octal, hex format):

```python
>>> i = 666666
>>> f'{i:#_b}, {i:#_o}, {i:#_x}, {i:#_X}'
'0b1010_0010_1100_0010_1010, 0o242_6052, 0xa_2c2a, 0XA_2C2A'
```

## Float Format

```python
>>> i = 666666.666
>>> f'{i:f}'
'666666.666000'
```

### Thousand Seperator: `,`

A **thousand separator** (**`,`**) for floating point presentation types:

```python
>>> i = 666666.666
>>> f'{i:,f}, {i:_f}'
'666,666.666000, 666_666.666000'
```

### Field Width and Precision

Total **field width** and **precision** (the minimum total field width, including any prefixes, separators, and other formatting characters. If not specified, then the field width will be determined by the content.):

```python
>>> i = 666666.666

>>> f'{i:f}'  # precision is 6 by default
'666666.666000'

>>> f'{i:.2f}'  # precision is 2
'666666.67'

>>> f'{i:10.2f}'  # width is 10 (space by default), precision is 2
' 666666.67'

>>> f'{i:010.2f}'  # width is 10 (padding with 0), precision is 2
'0666666.67'
```

Combination examples:

```python
>>> i = 1234.5678
>>> f'{i = :<+10.2f}'  # left-align, width 10, show +, precision 2
'i = +1234.57  '
```

## Date and Time Format

```python
>>> from datetime import datetime
>>> today = datetime(year=2019, month=10, day=24)

>>> f'{today:%B %d, %Y}'  # using date format specifier
'October 24, 2019'
>>> f'{today:%Y-%m-%d %H:%M:%S}'  # using datetime format specifier
'2019-10-24 00:00:00'
```

## Recommended Readings

- [PEP 3101 – Advanced String Formatting](https://peps.python.org/pep-3101/)
- [PEP 498 – Literal String Interpolation](https://peps.python.org/pep-0498/)

## References

- [PEP 3101 – Advanced String Formatting](https://peps.python.org/pep-3101/)
