# 装饰器 (Decorator)

返回值为另一个函数的函数，通常使用 *`@wrapper`* 语法形式来进行函数变换，称为**函数装饰器**或**方法装饰器**。装饰器的常见例子包括 `classmethod()` 和 `staticmethod()`。

同样的概念也适用于类，叫**类装饰器**，但通常较少这样使用。

## 语法糖

```python
def func(arg1, arg2, ...):
    pass

f = decorator(func)(arg1, arg2, ...)
```

语义上完全等价于:

```python
@decorator
def func(arg1, arg2, ...):
    pass
```

## 多重装饰器

```python
@dec2
@dec1
def func(arg1, arg2, ...):
    pass
```

等同于：

```python
def func(arg1, arg2, ...):
    pass

func = dec2(dec1(func))(arg1, arg2, ...)
```

## 用法：包装器 (Wrapper)

### 无参数

```python
from functools import wraps

def decorator(_func):

    @wraps(_func)
    def wrapper(*args, **kwargs):
        """wrapper function."""
        print(f'run wrapper: {args}, {kwargs}')
        return _func(*args, **kwargs)

    return wrapper

@decorator
def func(*args, **kwargs):
    """original function."""
    print(f'run func: {args}, {kwargs}')


# Without Arguments
>>> func()
run wrapper: (), {}
run func: (), {}

# With Arguments
>>> func('arg1', 'arg2')
run wrapper: ('arg1', 'arg2'), {}
run func: ('arg1', 'arg2'), {}
```

```python
>>> func.__module__
'**main**'
>>> func.__name__
'func'
>>> func.__doc__
'original function.'
>>> func.__annotations__
{}
>>> func.__qualname__
'func'
```

没有 **`@functools.wraps`** 装饰器，原始函数的属性无法传递，而是包装器 (wrapper) 的：

```python
>>> func.__module__
'__main__'
>>> func.__name__
'wrapper'
>>> func.__doc__
'wrapper function.'
>>> func.__annotations__
{}
>>> func.__qualname__
'decorator.<locals>.wrapper'
```

### 必需参数

```python
from functools import wraps

def decorator(arg1=None, arg2=None, *_args, **_kwargs):

    def _decorator(_func):

        @wraps(_func)
        def wrapper(*args, **kwargs):
            """wrapper function."""
            print(f'run wrapper: {arg1}, {arg2}, {_args}, {_kwargs}')
            return _func(*args, **kwargs)

        return wrapper

    return _decorator

@decorator(1, 2)
def func(*args, **kwargs):
    """original function."""
    print(f'run func: {args}, {kwargs}')
```

### 可选参数

```python
from functools import wraps, partial

def decorator(func=None, *, arg1=None, arg2=None):
    if func is None:
        return partial(decorator, arg1=arg1, arg2=arg2)

    @wraps(func)
    def wrapper(*args, **kwargs):
        """wrapper function."""
        print(f'run wrapper: {args}, {kwargs}')
        return func(*args, **kwargs)

    return wrapper

@decorator(1, 2)
def func(*args, **kwargs):
    """original function."""
    print('run func')
```

## 用法: 作用于函数

```python
def decorator(*_args, **_kwargs):

    def wrapper(_func):
        """wrapper function."""
        print(f'run wrapper: {_args}, {_kwargs}')
        return _func

    print(f'run decorator: {_args}, {_kwargs}')
    return wrapper

@decorator(1)
def func(*args, **kwargs):
    """original function."""
    print(f'run func: {args}, {kwargs}')


run decorator: (1,), {}
run wrapper: (1,), {}
>>> func()
run func: (), {}
>>> func(2)
run func: (2,), {}
```

## `@functools.wraps` 实现细节

```python
from functools import partial, update_wrapper

WRAPPER_ASSIGNMENTS = ('__module__', '__name__', '__qualname__', '__doc__', '__annotations__')
WRAPPER_UPDATES = ('__dict__',)

def wraps(wrapped,
          assigned = WRAPPER_ASSIGNMENTS,
          updated = WRAPPER_UPDATES):
    return partial(update_wrapper, wrapped=wrapped, assigned=assigned, updated=updated)
```

## 类装饰器

```python
class A:
  pass
A = decorator_a(decorator_b(A))


@decorator_a
@decorator_b
class A:
  pass
```

可以定义类型为类的装饰器

```python
from functools import wraps

class Decorator:
    def __init__(self, func):
        print('create instance')
        self.func = func

    @wraps(func)
    def __call__(self, *args, **kwargs):
        print('working here')
        return self.func(*args, **kwargs)

@Decorator
def func(arg1, arg2):
    return f'{arg1=}, {arg2=}'


>>> func('A', 'B')
create instance
working here
"arg1='A', arg2='B'"
```

也可以给类定义装饰器：

```python
def decorator(cls):
    cls.num_of_animals = 10
    return cls

@decorater
class A:
    pass


>>> a_obj = A()
>>> A.num_of_animals
10
```

## 类装饰器示例

1. 定义一个单实例类

    ```python
    def singleton(cls):
        instances = {}
        def getinstance():
            if cls not in instances:
                instances[cls] = cls()
            return instances[cls]
        return getinstance

    @singleton
    class MyClass:
        pass
    ```

2. 声明一个实现特殊接口集的类

    ```python
    def provides(*interfaces):
        """
        An actual, working, implementation of provides for
        the current implementation of PyProtocols.
        Not particularly important for the PEP text.
        """
        def provides(typ):
            declareImplementation(typ, instancesProvide=interfaces)
            return typ
        return provides

    class IBar(Interface):
        """Declare something about IBar here"""
        pass

    @provides(IBar)
    class Foo(object):
        """Implement something here..."""
        pass
    ```

## 参考资料

- [PEP 318 - Decorators for Functions and Methods (函数和方法装饰器)](https://peps.python.org/pep-0318/)
- [PEP 3129 – Class Decorators (类装饰器)](https://peps.python.org/pep-3129/)
- [PEP 614 – Relaxing Grammar Restrictions On Decorators (解除装饰器的语法限制)](https://peps.python.org/pep-0614/)
