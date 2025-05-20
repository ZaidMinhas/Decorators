# Decorators

This repo is a basic introduction to decorators in python and examples to help you understand their use case.


Lets say you have a simple function f(x) and you need to add extra functionality.
```py
def f(x):
	return x*x
```
This functionality could be specific to that funcion/class, generalized to any function for testing, or used for something bigger (ie a function used by a discord bot).

function decorator is a function that wraps f(x) to give it extra functionality.
Running f(2) would return `Running f: 4`

```py
def decorator(func):
	def wrapper(x):
		return "Running " + func.__name__ + ": " + str(func(x))

	return wrapper

def f(x):
	return x*x

f = decorator(f)
```

The last line, `f = decorator(f)`, is the main decorator part which has its own way of being used in python using @

```py
def decorator(func):
	def wrapper(x):
		return "Running " + func.__name__ + ": " + str(func(x))

	return wrapper

@decorator
def f(x):
	return x*x
```
The only issue with this now is that you cant use f without it using the decorator too.


## Basic Usage

Dectorator that can be used for any function
```py
def log_call(func):
    def wrapper(*args, **kwargs):
        print(f"Calling {func.__name__} with {args}, {kwargs}")
        return func(*args, **kwargs)
    return wrapper
```

```
@log_call
def greet(name):
    print(f"Hello, {name}!")

greet("Alice")
```
>>> Calling greet with ('Alice',), {}
>>> Hello, Alice!

Func to find avg time taken to run a functiob and its std
```py
import time
import statistics

def func_stats(func):
	def wrapper(*args, **kwargs):
		# Collect data
		t = []
		for _ in range(100):
			start = time.time()
			result = func(*args, **kwargs)
			end = time.time()
			t.append(end-start)
			
		# calculate statistics
		mean = statistics.mean(t)
		std_dev = statistics.stdev(t)
		print(f"mean: {mean:.5f}, std: {std_dev:.5f}")

		return result
	return wrapper

@func_stats
def f(x):
	s = 0
	for i in range(x):
		for j in range(x):
			s = i*x + j
	return s
```

```
def memoize(func):
    cache = {}
    def wrapper(n):
        if n in cache:
            return cache[n]
        result = func(n)
        cache[n] = result
        return result
    return wrapper

@memoize
def fib(n):
    if n <= 1:
        return n
    return fib(n-1) + fib(n-2)

print(fib(30))  # Much faster due to caching
```

## Argument passing

Sometimes we want to add arguments to the decorator function itself, heres an example

```py
def repeat(n):
    def decorator(func):
        def wrapper(*args, **kwargs):
            for _ in range(n):
                func(*args, **kwargs)
        return wrapper
    return decorator

@repeat(3)
def greet():
    print("Hello!")

greet()
# Output:
# Hello!
# Hello!
# Hello!
```

```py
def log(func):
    def wrapper(*args, **kwargs):
        print(f"Calling {func.__name__}")
        return func(*args, **kwargs)
    return wrapper

def shout(func):
    def wrapper(*args, **kwargs):
        result = func(*args, **kwargs)
        return result.upper() + "!"
    return wrapper

@log
@shout
def say_hello():
    return "hello"

print(say_hello())
# Output:
# Calling wrapper  (original name gets masked — we'll fix this below)
# HELLO!
```
