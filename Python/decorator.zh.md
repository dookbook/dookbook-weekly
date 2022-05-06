# 装饰器 (Decorator)

!!! info "PEP 614 - 解除语法限制"
    Python *3.9* 以后，支持任意合法语句的装饰器。
    之前只支持 `xx.xx.xx()` 的语法。

返回值为另一个函数的函数，通常使用 *`@wrapper`* 语法形式来进行函数变换，称为**函数装饰器**或**方法装饰器**。装饰器的常见例子包括 `classmethod()` 和 `staticmethod()`。

装饰器语法只是一种**语法糖**，以下两个函数定义在语义上完全等价:

```python
def func(arg1, arg2, ...):
    pass

f = decorator(func)(arg1, arg2, ...)
```

```python
@decorator
def func(arg1, arg2, ...):
    pass
```

同样的概念也适用于类，叫**类装饰器**，但通常较少这样使用。

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

## 装饰器实现：包装器 (Wrapper)

### 简单实现

```python
def decorator(func):
    def wrapper(*args, **kwargs):
        """包装器 (修饰器)"""
        print(f'run wrapper: {args}, {kwargs}')
        return func(*args, **kwargs)

    print('run decorator')
    return wrapper

@decorator
def func(*args, **kwargs):
    """原函数"""
    print('run func')


# 不带参数
>>> func()
run decorator
run wrapper: (), {}
run func

# 带参数
>>> func('arg1', 'arg2')
run decorator
run wrapper: ('arg1', 'arg2'), {}
run func
```

上述实现，没有传递 `__name__`, `__doc__` 等属性：

```python
>>> func.__module__
'__main__'

>>> func.__name__
'wrapper'

>>> func.__doc__
'包装器 (修饰器)'

>>> func.__annotations__
{}

>>> func.__qualname__
'decorator.<locals>.wrapper'
```

### 完整实现

通过 **`functools.wraps`** 装饰器，则可以返回原始函数的属性而非包装器 (wrapper)的：

```python
from functools import wraps

def decorator(func):
    @wraps(func)
    def wrapper(*args, **kwargs):
        """包装器 (修饰器)"""
        print(f'run wrapper: {args}, {kwargs}')
        return func(*args, **kwargs)

    print('run decorator')
    return wrapper

@decorator
def func(*args, **kwargs):
    """原函数"""
    print('run func')


>>> func.__module__
'__main__'
>>> func.__name__
'func'
>>> func.__doc__
'原函数'
>>> func.__annotations__
{}
>>> func.__qualname__
'func'
```

### 装饰器参数

```python
from functools import wraps, partial

def decorator(func=None, arg1=None, arg2=None):
    if func is None:
        return partial(decorator, arg1=arg1, arg2=arg2)

    @wraps(func)
    def wrapper(*args, **kwargs):
        """包装器 (修饰器)"""
        print(f'run wrapper: {args}, {kwargs}')
        return func(*args, **kwargs)

    print('run decorator')
    return wrapper

@decorator(1, 2)
def func(*args, **kwargs):
    """原函数"""
    print('run func')
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

## 函数装饰器示例

1. 向函数或方法增加属性

    ```python
    def attrs(**kwds):
        def wrapper(func):
            for k in kwds:
                setattr(func, k, kwds[k])
            return func
        return wrapper

    @attrs(versionadded="2.2",
           author="Guido van Rossum")
    def method(f):
        pass
    ```

2. 强制函数参数和返回类型

    ```python
    def accepts(*types):
        def wrapper(func):
            assert len(types) == func.func_code.co_argcount
            def new_func(*args, **kwds):
                for (a, t) in zip(args, types):
                    assert isinstance(a, t), f'arg {a} does not match {t}'
                return func(*args, **kwds)
            new_func.func_name = func.func_name
            return new_func
        return wrapper

    def returns(rtype):
        def wrapper(func):
            def new_func(*args, **kwds):
                result = func(*args, **kwds)
                assert isinstance(result, rtype), f'return value {result} does not match {rtype}'
                return result
            new_func.func_name = func.func_name
            return new_func
        return wrapper

    @accepts(int, (int,float))
    @returns((int,float))
    def func(arg1, arg2):
        return arg1 * arg2
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

## 推荐阅读

- [PEP 318 - Decorators for Functions and Methods (函数和方法装饰器)](https://peps.python.org/pep-0318/)
- [PEP 3129 – Class Decorators (类装饰器)](https://peps.python.org/pep-3129/)
- [PEP 614 – Relaxing Grammar Restrictions On Decorators (解除装饰器的语法限制)](https://peps.python.org/pep-0614/)
