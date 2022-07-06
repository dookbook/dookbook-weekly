# 类装饰器 (Class Decorator)

## 语法糖

```python
@decorator_a
@decorator_b
class A:
    pass
```

语法等同于:

```python
class A:
    pass
A = decorator_a(decorator_b(A))
```

## 用法: 类型为类的装饰器

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

## 用法: 给类定义装饰器

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

## 示例: 单实例

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


>>> c1 = MyClass()
>>> c2 = MyClass()
>>> assert c1 == c2
>>> assert c1 is c2
```

## 示例: 实现接口

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

- [PEP 3129 – Class Decorators (类装饰器)](https://peps.python.org/pep-3129/)
- [PEP 614 – Relaxing Grammar Restrictions On Decorators (解除装饰器的语法限制)](https://peps.python.org/pep-0614/)
