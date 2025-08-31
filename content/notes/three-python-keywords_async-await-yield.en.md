---
title: "Three Python Keywords: async, await, yield"
date: 2025-08-31T20:38:36+08:00
tags:
  - Python
draft: false
description: "This article introduces three important Python keywords: async, await, and yield, explaining their meanings, functions, and applications in asynchronous programming and generators. It also demonstrates through practical examples how to use these keywords to improve program performance."
summary: Learn about Python's async, await, and yield keywords, understand asynchronous programming and generator usage methods, and improve code execution efficiency.
---

I recently learned about three Python keywords, which I find quite useful:
- `async + await` enable asynchronous programming for multitasking without consuming as many resources as multithreading.
- `yield` helps implement lazy loading in code, where computations are performed only when invoked (i.e., when truly needed), avoiding excessive data accumulation or long execution times.


The content I will cover includes:
1. Explanations of `async`, `await`, and `yield`
2. A brief introduction to asynchronous programming and coroutines
3. A comparison between synchronous and asynchronous operations
4. A practical example (file upload)
5. A summary of the article

## Meanings and Roles of `async`, `await`, and `yield`

### `async`

- **Meaning**: A keyword used to define asynchronous functions (coroutines), marking that the function may contain asynchronous operations and needs to be scheduled via an event loop.
- **Role**: Declares a function as a coroutine, allowing it to use the `await` keyword internally.
- **Syntax**: Prepend the `async` keyword to a function definition (e.g., `async def funcname()`).

```python
# Define an asynchronous function (coroutine)
async def async_function():
    print("This is an asynchronous function")
    # Can use await internally to call other awaitable objects
    await another_async_operation()
    print("Execution completed")
```

### `await`

- **Meaning**: Used to pause the execution of the current coroutine, wait for another awaitable object (e.g., coroutine, Task, Future) to complete, and retrieve its return result.
- **Role**: Voluntarily yields CPU execution rights during waiting, allowing the event loop to schedule other ready tasks, achieving non-blocking waits and improving program efficiency.
- **Syntax**:

```python
import asyncio

async def fetch_data():
    # Simulate asynchronous network request
    await asyncio.sleep(2)  # Non-blocking wait for 2 seconds
    return "Returned data"      # Return data

async def main():
    # Wait for fetch_data to complete and get result
    data = await fetch_data()
    print(data)  # Output: Returned data

asyncio.run(main()) # Run the coroutine
```

### `yield`

- **Meaning**: Used in generator functions to pause execution and return a value; the next call to the generator resumes execution from the paused position.
- **Role**: Converts a function into a generator, enabling iterator functionality to generate data on demand (lazy evaluation) and save memory.
- **Syntax**:

```python
# Define a generator function
def number_generator(n):
    for i in range(n):
        yield i  # Return i when next() is called, then pause execution
        print(f"Continuation after generating {i}")

# Create a generator object
gen = number_generator(3)

# Iterate over the generator
print(next(gen))  # Output: 0
print(next(gen))  # Output: Continuation after generating 0 → 1
print(next(gen))  # Output: Continuation after generating 1 → 2
print(next(gen))  # Triggers StopIteration exception (generation completed)
```

#### Differences Between `yield` and `return`

Both `yield` and `return` are Python keywords for returning values from functions, but their mechanisms and use cases differ fundamentally. The core distinction lies in **whether they terminate function execution** and **how return values are used**.

- `return`: Terminates the function and returns a result
  - **Role**: Immediately stops function execution, returns the result to the caller, and subsequent code in the function is not executed.
  - **Features**: A function can contain multiple `return` statements, but only one will execute (since the function terminates afterward).
  - **Use Case**: Regular functions, for returning computed results and ending the function.
  - **Example**:

```python
def add(a, b):
    # The function terminates immediately after returning the result
    return a + b
 	# The following will never be executed
    print("This line will never execute")

result = add(2, 3)
print(result)  # Output: 5
```


- `yield`: Pause the function and return the current result
  - **Role**: Pauses function execution, returns the current result to the caller, but does not terminate the function; the next call resumes from the paused position.
  - **Features**: A function containing `yield` becomes a **generator**. Calling it does not execute immediately but returns a generator object, which requires `next()` or iteration to trigger execution.
  - **Use Case**: Scenarios requiring "on-demand data generation" (e.g., iterating over large datasets, lazy evaluation) to avoid memory overload from generating all data at once.
  - **Example**:

```python
def number_generator(n):
    for i in range(n):
        # Pause the function, return i, and resume here next time
        yield i
        print(f"Continue execution after generating {i}")  # Executed on next call

# Call the generator function; returns a generator object (no immediate execution)
gen = number_generator(3)

# First call to next(): execute until yielding 0, return 0, and pause
print(next(gen))  # Output: 0

# Second call to next(): resume from pause, print "Continue execution after generating 0", then yield 1 and return 1
print(next(gen))  # Output: Continue execution after generating 0 → 1

# Third call to next(): similarly, return 2
print(next(gen))  # Output: Continue execution after generating 1 → 2

# Fourth call to next(): loop ends, trigger StopIteration exception
print(next(gen))  # Trigger StopIteration exception
```


Summary:
- `return` is "irreversible": The function ends completely after returning a result.
- `yield` is "temporarily paused": Pause after returning intermediate results, and can resume later.

## What is Asynchronous Programming?

Asynchronous programming is a programming paradigm that addresses inefficiencies caused by "waiting" operations (e.g., network requests, file I/O) in programs. It allows the program to avoid blocking during waits and continue executing other tasks.

### Core Issue: Pain Points of Synchronous Programming

In synchronous programming, tasks execute sequentially. When encountering time-consuming operations (e.g., fetching network data), the program "freezes" while waiting for results, unable to perform other tasks in the meantime, leading to inefficiency.

Example:

```python
import time

# Synchronous approach
def get_data():
    # Simulate a network request (takes 2 seconds)
    time.sleep(2)
    return "Data"

print("Start")
data = get_data()  # The program freezes here for 2 seconds, with no other operations
print("Data received:", data)
print("End")
```

This code will pause for 2 seconds at `get_data()`, unable to execute subsequent operations during the wait.

### Solution Idea of Asynchronous Programming

Asynchronous programming allows the program to "skip" a task while waiting for a time-consuming operation, execute other tasks, and return to process the result once the time-consuming operation completes.

Key Concepts:
- **Event Loop**: Acts as a "scheduling center" that manages the execution order of all tasks and decides which task to run at any given time.
- **Coroutine**: A special function (defined with `async def`) that can be paused and resumed, serving as the basic unit of asynchronous tasks.
- **await**: Used in coroutines. When encountering a time-consuming operation (e.g., network request), `await` tells the event loop: "I need to wait for a result; go execute other tasks first."

### Using Asynchronous Programming in Python

Asynchronous programming in Python primarily relies on the `asyncio` library, with core syntax `async/await` for defining and scheduling coroutines. Below are key steps and examples:

#### Running a Coroutine

To run a coroutine, use the `asyncio.run()` function. It creates an event loop and runs the specified coroutine.

```python
import asyncio

async def main():
    print("Start")
    await asyncio.sleep(3)  # Pause for 3 seconds to simulate a long operation
    print("End")

asyncio.run(main())
```

#### Concurrent Execution

Use `asyncio.gather()` to execute multiple coroutines concurrently and wait for all to complete.

```python
import asyncio

async def task1():
    print("Task 1 started")
    await asyncio.sleep(1) # Simulate I/O operation (non-blocking wait)
    print("Task 1 finished")

async def task2():
    print("Task 2 started")
    await asyncio.sleep(2) # Simulate I/O operation (non-blocking wait)
    print("Task 2 finished")

async def main():
    await asyncio.gather(task1(), task2())

asyncio.run(main())
```

#### Creating Tasks

A task is a wrapper around a coroutine, representing an ongoing or upcoming coroutine execution. Use `asyncio.create_task()` to create tasks, which are added to the event loop's task queue and start running automatically (unless the event loop is not started).

```python
async def say_hello():
    print("Hello!")

async def main():
    task = asyncio.create_task(say_hello())
    await task
```

`asyncio.gather()` automatically wraps coroutines into Tasks and starts them (equivalent to calling `create_task()` first). It can also accept tasks created via `asyncio.create_task()`.

Explicitly calling `asyncio.create_task()` offers more flexibility, such as passing specific parameters to coroutines or batch-generating tasks in loops.

#### Timeout Control

Use `asyncio.wait_for()` to set a timeout for a coroutine. If the coroutine does not complete within the specified time, an `asyncio.TimeoutError` exception is raised.

```python
import asyncio

async def long_task():
    await asyncio.sleep(10)
    print("Task finished")

async def main():
    try:
        # Restrict the task to complete within 5 seconds
        await asyncio.wait_for(long_task(), timeout=5)
    except asyncio.TimeoutError:
        print("Task timed out")

asyncio.run(main())  # Output: Task timed out
```

### Asynchronous Programming Example

```python
import asyncio

# Define asynchronous tasks (coroutines)
async def get_data():
    print("Starting data request...")
    await asyncio.sleep(2) # Simulate network request (non-blocking wait)
    print("Data request completed")
    return "Data"

async def other_task():
    print("Executing other task...")
    await asyncio.sleep(1) # Simulate I/O operation (non-blocking wait)
    print("Other task completed")

# Main coroutine
async def main():
    # Start both tasks simultaneously
    await asyncio.gather(get_data(), other_task())

# Start the event loop
asyncio.run(main())
```


**Execution Order**:
1. The event loop starts, runs `main()`, creates and starts two tasks.
2. `get_data()` begins execution, encounters `await asyncio.sleep(2)`, pauses, and the event loop switches to `other_task()`.
3. `other_task()` executes until `await asyncio.sleep(1)`, pauses. Both tasks are now waiting, and the event loop waits for 1 second.
4. After 1 second, `other_task()` resumes, prints "Other task completed", and ends.
5. After another 1 second (total 2 seconds), `get_data()` resumes, prints "Data request completed", and ends.


**Total execution time: ~2 seconds** (compared to 3 seconds for synchronous execution), showing significant efficiency improvement.

## Case Study

Implement a file upload function using asynchronous programming. The general idea is to retrieve specified file paths (one or more) from the terminal, then asynchronously read multiple files and save them to the current directory.

### Reading File Content with a Generator

Use a generator to read file content line by line, avoiding memory overflow caused by large files.

```python
def file_reader_generator(file_path):
    try:
        with open(file_path, "r", encoding="utf-8") as file:
            for line in file:
                # Pause, return one line, and return the next line on subsequent calls
                yield line
    except Exception as e:
        print(f"Error reading file {file_path}: {e}")
```

### Saving Read Content to Files

Adopt streaming reading: read file content line by line and save it to the target file simultaneously.

```python
async def save_content(original_file_path):
    original_path = Path(original_file_path)
    new_filepath = f"save_{original_path.name}"

    with open(new_filepath, "w", encoding="utf-8") as file:
        for line in file_reader_generator(original_file_path):
            file.write(line)

    print(f"File saved to: {new_filepath}")
```

### Processing Files Asynchronously

```python
async def process_files(file_paths):
    tasks = []
    for file_path in file_paths:
        if os.path.exists(file_path):
            tasks.append(
                asyncio.create_task(save_content(file_path))
            )
        else:
            print(f"File does not exist: {file_path}")
    
    await asyncio.gather(*tasks)
```

### Complete Code

```python
import asyncio
import sys
import os
from pathlib import Path

def file_reader_generator(file_path):
    """
    Read file content using a generator to avoid memory overflow from large files
    """
    try:
        with open(file_path, "r", encoding="utf-8") as file:
            for line in file:
                yield line
    except Exception as e:
        print(f"Error reading file {file_path}: {e}")


async def save_content(original_file_path):
    """Save content to a new file"""
    original_path = Path(original_file_path)
    new_filepath = f"save_{original_path.name}"

    with open(new_filepath, "w", encoding="utf-8") as file:
        for line in file_reader_generator(original_file_path):
            file.write(line)

    print(f"File saved to: {new_filepath}")


async def process_files(file_paths):
    """Process all files"""
    tasks = []
    for file_path in file_paths:
        if os.path.exists(file_path):
            tasks.append(
                asyncio.create_task(save_content(file_path))
            )
        else:
            print(f"File does not exist: {file_path}")
    
    await asyncio.gather(*tasks)


if __name__ == "__main__":
    # Retrieve file path parameters from the terminal
    if len(sys.argv) < 2:
        print("Please provide at least one file path as an argument")
        print("Usage: python main.py file1.txt file2.txt ...")
        sys.exit(1)

    file_paths = sys.argv[1:]
    asyncio.run(process_files(file_paths))
```

## Summary

- `yield` is used in generators to implement the iterator protocol, primarily for data generation and lazy loading.
- `async` and `await` are used in asynchronous programming to implement non-blocking I/O operations and improve program concurrency.
- `async/await` is typically used with the `asyncio` module to build asynchronous applications.

## References

- [Asynchronous I/O | Liao Xuefeng's Official Website](https://liaoxuefeng.com/books/python/async-io/index.html)
- [Python asyncio Module | Runoob Tutorial](https://www.runoob.com/python3/python-asyncio.html)
