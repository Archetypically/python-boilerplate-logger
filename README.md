# python-boilerplate-logging

A logger by developers for developers.

## Motivation

Too many times during active development have I done the following:

```python
def callable(*args, **kwargs):
  log.debug("got here test")
  ...
  return result

def main():
  ...
  log.debug("got here1")
  ...
  log.debug("got here2")
  log.debug(f"now calling callable() with args: {args}")
  result = callable(*args)
  log.debug(f"callable() returned with: {result}")
  ...
  log.debug("got here 5")
```

**Let's get the "got here" out of here.**

`boilerplate-logger` provides decorator interfaces to automatically log inputs and output of functions and methods invocations to ease development cycles and improve execution tracing.

## Usage

#### Configuration

`boilerplate-logger` uses Python `logging` under the hood; access the underlying `logger` object using `boilerplate.logger` or `logging.getLogger("boilerplate")`.

#### Simple Function Logging

```python
import boilerplate as bp


@bp.log
def add(x, y):
    return x + y


@bp.log("inputs")
def subtract(x, y):
    return x - y


@bp.log("outputs")
def multiply(x, y):
    return x * y

def main():
  add(1, 1)
  subtract(2, 1)
  multiply(4, 5)

if __name__ == "__main__":
  main()
```

Which gives you:

```
[__main__.add] inputs: [args: (1, 1)] [kwargs: {}]
[__main__.add] returns: [2]
[__main__.subtract] inputs: [args: (2, 1)] [kwargs: {}]
[__main__.multiply] returns: [20]
```

#### Exception Trace

```python
import boilerplate as bp


@bp.trace
def add(x, y):
    return x + y


@bp.trace
def subtract(x, y):
    return x - y


@bp.trace
def multiply(x, y):
    return x * y

def main():
  add(1, 1)
  subtract(2, 1)
  bp.flush()

  bp.register_uncaught_exception_handler()
  multiply(4, 5)
  raise Exception("trigger flush of boilerplate logs")

if __name__ == "__main__":
  main()
```

Which gives you:

```
== BOILERPLATE TRACE:
[__main__.add] inputs: [args: (1, 1)] [kwargs: {}]
[__main__.add] returns: [2]
[__main__.subtract] inputs: [args: (2, 1)] [kwargs: {}]
[__main__.subtract] returns: [1]
== END BOILERPLATE TRACE ==
Registered as uncaught exception handler!
Exception occurred; flushing boilerplate trace.
== BOILERPLATE TRACE:
[__main__.multiply] inputs: [args: (4, 5)] [kwargs: {}]
[__main__.multiply] returns: [20]
== END BOILERPLATE TRACE ==
Traceback (most recent call last):
  File "trace_example.py", line 25, in <module>
    raise Exception("trigger flush of boilerplate logs")
Exception: trigger flush of boilerplate logs
```
