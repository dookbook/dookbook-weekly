# Context Decorator

## Base Class: `contextlib.ContextDecorator`

Context managers inheriting from **`ContextDecorator`** have to implement *`__enter__()`* and *`__exit__()`* as normal.
`__exit__()` retains its optional exception handling even when used as a decorator.

Example of `ContextDecorator`:

```python
from contextlib import ContextDecorator

class context_decorator(ContextDecorator):
    def __enter__(self):
        print('Entry into runtime context')
        return self

    def __exit__(self, *exc) -> bool:
        print('Exit from runtime context')
        return False

@context_decorator()
def func():
    print('Execute func')


>>> func()
Entry into runtime context
Execute func
Exit from runtime context

>>> with context_decorator():
...     print('Execute with statement body')
...
Entry into runtime context
Execute with statement body
Exit from runtime context
```

### Syntactic Sugar

`ContextDecorator` is just *syntactic sugar*:

```python
@context_decorator()
def func():
    print('Execute func')
```

same with

```python
def func():
    with context_decorator():
        print('Execute func')
```

### Mixin Class

Existing context managers that already have a base class can be extended by using *`ContextDecorator`* as a mixin class:

```python
from contextlib import ContextDecorator

class context_decorator(OtherBaseClass, ContextDecorator):
    def __enter__(self):
        return self

    def __exit__(self, *exc):
        return False
```

## Simple Usage: `@contextlib.contextmanager`

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

Simplified:

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

### Use Cases

1. A template for ensuring that a lock, acquired at the start of a block, is released when the block is left:

    ```python
    @contextmanager
    def locked(lock):
        lock.acquire()
        try:
            yield
        finally:
            lock.release()
    ```

    Used as follows:

    ```python
    with locked(lock):
        # Code here executes with lock held.  The lock is
        # guaranteed to be released when the block is left (even
        # if via return or by an uncaught exception).
    ```

2. A template for opening a file that ensures the file is closed when the block is left:

    ```python
    @contextmanager
    def opened(filename, mode="r"):
        f = open(filename, mode)
        try:
            yield f
        finally:
            f.close()
    ```

    Used as follows:

    ```python
    with opened("/etc/passwd") as f:
        for line in f:
            print line.rstrip()
    ```

3. A template for committing or rolling back a database transaction:

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

4. Redirect stdout temporarily:

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

    Used as follows:

    ```pythohn
    with opened(filename, "w") as f:
        with stdout_redirected(f):
            print "Hello world"
    ```

    This isn’t thread-safe.

5. A variant on `opened()` that also returns an error condition:

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

    Used as follows:

    ```python
    with opened_w_error("/etc/passwd", "a") as (f, err):
        if err:
            print "IOError:", err
        else:
            f.write("Lee::0:0::/:/bin/sh\n")
    ```

6. Simple context manager for the `decimal` module:

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

    Sample usage:

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

7. A "*nested*" context manager that automatically nests the supplied contexts from left-to-right to avoid excessive indentation:

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

    Sample usage:

    ```python
    with nested(a, b, c) as (x, y, z):
        # Perform operation
    ```

    Is equivalent to:

    ```python
    with a as x:
        with b as y:
            with c as z:
                # Perform operation
    ```

## Generator Decorator

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
    """Reference implementation of contextlib.contextmanager.
    """
    def helper(*args, **kwds):
        return GeneratorContextManager(func(*args, **kwds))
    return helper
```

## References

- [Python - `contextlib.ContextDecorator`](https://dookbook.info/docs/python/lastest/en/library/contextlib.html#contextlib.ContextDecorator)
- [Python - `@contextlib.contextmanager`](https://dookbook.info/docs/python/lastest/en/library/contextlib.html#contextlib.contextmanager)
