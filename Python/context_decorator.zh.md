# 上下文装饰器 (Context Decorator)

## 基类：`contextlib.ContextDecorator`

继承自 **`ContextDecorator`** 的上下文管理器必须实现 *`__enter__()`* 与 *`__exit__()`*。即使用作装饰器，`__exit__()` 依旧会保持可能的异常处理。

`ContextDecorator` 的示例:

```python
from contextlib import ContextDecorator

class context_decorator(ContextDecorator):
    def __enter__(self):
        print('进入运行时上下文')
        return self

    def __exit__(self, *exc) -> bool:
        print('退出运行时上下文')
        return False

@context_decorator()
def func():
    print('执行 func')


>>> func()
进入运行时上下文
执行 func
退出运行时上下文

>>> with context_decorator():
...     print('执行 with 语句体')
...
进入运行时上下文
执行 with 语句体
退出运行时上下文
```

### 语法糖

`ContextDecorator` 是一个*语法糖*：

```python
@context_decorator()
def func():
    print('执行 func')
```

等同于

```python
def func():
    with context_decorator():
        print('执行 func')
```

### 混合类 (Mixin Class)

现有的上下文管理器即使已经有基类，也可以使用 *`ContextDecorator`* 作为混合类进行扩展：

```python
from contextlib import ContextDecorator

class context_decorator(OtherBaseClass, ContextDecorator):
    def __enter__(self):
        return self

    def __exit__(self, *exc):
        return False
```

## 简洁用法：`@contextlib.contextmanager`

```python
from contextlib import contextmanager

@contextmanager
def managed_resource(*args, **kwds):
    # Code to acquire resource, e.g.:
    resource = acquire_resource(*args, **kwds)
    try:
        yield resource
    finally:
        # Code to release resource, e.g.:
        release_resource(resource)

>>> with managed_resource(timeout=3600) as resource:
...     pass
```

简化了

```python
class managed_resource:
    def __init__(self, *args, **kwds):
        self.resource = None
        self.args = args
        self.kwds = kwds

    def __enter__(self):
        self.resource = acquire_resource(*self.args, **self.kwds)

    def __exit__(self, exc_type, exc_value, traceback) -> bool:
        release_resource(self.resource)
        return False
```

### 使用实例

1. 确保锁的获取和释放：

    ```python
    @contextmanager
    def locked(lock):
        lock.acquire()
        try:
            yield
        finally:
            lock.release()
    ```

    使用如下：

    ```python
    with locked(lock):
        # Code here executes with lock held.  The lock is
        # guaranteed to be released when the block is left (even
        # if via return or by an uncaught exception).
    ```

2. 确保文件的打开和关闭：

    ```python
    @contextmanager
    def opened(filename, mode="r"):
        f = open(filename, mode)
        try:
            yield f
        finally:
            f.close()
    ```

    使用如下：

    ```python
    with opened("/etc/passwd") as f:
        for line in f:
            print line.rstrip()
    ```

3. 确保数据库事务的提交和回滚：

    ```python
    @contextmanager
    def transaction(db):
        db.begin()
        try:
            yield None
        except:
            db.rollback()
            raise
        else:
            db.commit()
    ```

4. 临时重定向 (redirect) 标注输出 (`stdout`)

    ```python
    @contextmanager
    def stdout_redirected(new_stdout):
        save_stdout = sys.stdout
        sys.stdout = new_stdout
        try:
            yield None
        finally:
            sys.stdout = save_stdout
    ```

    使用如下：

    ```pythohn
    with opened(filename, "w") as f:
        with stdout_redirected(f):
            print "Hello world"
    ```

    注意：`stdout_redirected` 非线程安全。

5. 一种同时返回文件符和错误的变体 `opened()`：

    ```python
    @contextmanager
    def opened_w_error(filename, mode="r"):
        try:
            f = open(filename, mode)
        except IOError, err:
            yield None, err
        else:
            try:
                yield f, None
            finally:
                f.close()
    ```

    使用如下：

    ```python
    with opened_w_error("/etc/passwd", "a") as (f, err):
        if err:
            print "IOError:", err
        else:
            f.write("Lee::0:0::/:/bin/sh\n")
    ```

6. `decimal` 模块的简单上下文管理器：

    ```python
    @contextmanager
    def localcontext(ctx=None):
        """Set a new local decimal context for the block"""
        # Default to using the current context
        if ctx is None:
            ctx = getcontext()
        # We set the thread context to a copy of this context
        # to ensure that changes within the block are kept
        # local to the block.
        newctx = ctx.copy()
        oldctx = decimal.getcontext()
        decimal.setcontext(newctx)
        try:
            yield newctx
        finally:
            # Always restore the original context
            decimal.setcontext(oldctx)
    ```

    示例用法：

    ```python
    from decimal import localcontext, ExtendedContext

    def sin(x):
        with localcontext() as ctx:
            ctx.prec += 2
            # Rest of sin calculation algorithm
            # uses a precision 2 greater than normal
        return +s # Convert result to normal precision

    def sin(x):
        with localcontext(ExtendedContext):
            # Rest of sin calculation algorithm
            # uses the Extended Context from the
            # General Decimal Arithmetic Specification
        return +s # Convert result to normal context
    ```

7. 嵌套的上下文管理器：

    ```python
    @contextmanager
    def nested(*contexts):
        exits = []
        vars = []
        try:
            try:
                for context in contexts:
                    exit = context.__exit__
                    enter = context.__enter__
                    vars.append(enter())
                    exits.append(exit)
                yield vars
            except:
                exc = sys.exc_info()
            else:
                exc = (None, None, None)
        finally:
            while exits:
                exit = exits.pop()
                try:
                    exit(*exc)
                except:
                    exc = sys.exc_info()
                else:
                    exc = (None, None, None)
            if exc != (None, None, None):
                # sys.exc_info() may have been
                # changed by one of the exit methods
                # so provide explicit exception info
                raise exc[0], exc[1], exc[2]
    ```

    示例用法：

    ```python
    with nested(a, b, c) as (x, y, z):
        # Perform operation
    ```

    等同于：

    ```python
    with a as x:
        with b as y:
            with c as z:
                # Perform operation
    ```

## 生成器装饰器 (Generator Decorator)

```python
class GeneratorContextManager(object):

    def __init__(self, gen):
        self.gen = gen

    def __enter__(self):
        try:
            return self.gen.next()
        except StopIteration:
            raise RuntimeError("generator didn't yield")

    def __exit__(self, type, value, traceback):
        if type is None:
            try:
                self.gen.next()
            except StopIteration:
                return
            else:
                raise RuntimeError("generator didn't stop")
        else:
            try:
                self.gen.throw(type, value, traceback)
                raise RuntimeError("generator didn't stop after throw()")
            except StopIteration:
                return True
            except:
                # only re-raise if it's *not* the exception that was
                # passed to throw(), because __exit__() must not raise
                # an exception unless __exit__() itself failed.  But
                # throw() has to raise the exception to signal
                # propagation, so this fixes the impedance mismatch
                # between the throw() protocol and the __exit__()
                # protocol.
                #
                if sys.exc_info()[1] is not value:
                    raise

def contextmanager(func):
    """contextlib.contextmanager 参考实现
    """
    def helper(*args, **kwds):
        return GeneratorContextManager(func(*args, **kwds))
    return helper
```

## 参考资料

- [Python 官方文档 - `contextlib.ContextDecorator`](https://dookbook.info/docs/python/lastest/zh-hans/library/contextlib.html#contextlib.ContextDecorator)
- [Python 官方文档 - `@contextlib.contextmanager`](https://dookbook.info/docs/python/lastest/zh-hans/library/contextlib.html#contextlib.contextmanager)
