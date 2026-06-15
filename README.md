# JStackLight

A lightweight, high-performance primitive stack library for Java designed to eliminate boxing overhead and provide fast stack operations using array-backed implementations.

---

## Why JStackLight?

Java’s standard stack-like structures (such as `ArrayDeque<Integer>`) use boxed types, which introduce:

- Extra memory allocation per element
- Boxing / unboxing overhead
- Reduced performance in tight loops

JStackLight avoids these issues by using primitive arrays directly.

---

## Features

- Primitive stacks (no boxing/unboxing)
- Fixed-capacity stacks (no resizing)
- Dynamic resizing stacks (auto-growing arrays)
- LIFO iteration support
- Unsafe API for maximum performance
- Safe API for predictability and error handling
- Lightweight and fast
- Simple and easy-to-use
- Fail-fast iterator

---

## Installation

1. Download the jar file from the releases
2. Open Intellij IDEA and load your project'
3. Go to Project Structure -> Modules -> Dependencies
4. Press the + and add the jar file
5. Import it in the project by typing `import com.xJavaFlamex.JStackLight.*;`

---

## Quick Start

```java
IntStack stack = new IntStack(32);

stack.push(10);
stack.push(20);
stack.push(30);

System.out.println(stack.pop());  // 30
System.out.println(stack.peek()); // 20
System.out.println(stack.capacity()); // 32
```

---

## API Overview

### Safe Operations

| Method | Description |
|----------|-------------|
| `push(int item)` | Pushes an item onto the stack. |
| `pushAll(int... items)` | Pushes multiple items onto the stack. |
| `pop()` | Removes and returns the top item. |
| `pop(int depth)` | Removes and returns multiple items from the top as an array. |
| `peek()` | Returns the top item without removing it. |
| `peek(int depth)` | Returns an item at a given depth from the top (`0` = top). |

### Unsafe Operations

| Method | Description |
|----------|-------------|
| `pushUnsafe(int item)` | Pushes an item without validation or capacity checks. |
| `pushAllUnsafe(int... items)` | Pushes multiple items without validation or capacity checks. |
| `popUnsafe()` | Removes and returns the top item without checking if the stack is empty. |
| `popUnsafe(int depth)` | Removes and returns multiple items without validation. |
| `peekUnsafe()` | Returns the top item without validation. |
| `peekUnsafe(int depth)` | Returns an item at a given depth without validation. |

### General Operations

| Method | Description |
|----------|-------------|
| `size()` | Returns the number of elements in the stack. |
| `isEmpty()` | Returns `true` if the stack is empty. |
| `capacity()` | Returns the current backing-array capacity. |
| `clear()` | Removes all elements from the stack. |
| `wipe()` | Removes all elements and clears the backing array. |
| `contains(int item)` | Checks whether the stack contains the specified item. |
| `searchFromTop(int item)` | Searches for an item starting from the top of the stack. |
| `toArray()` | Returns the stack contents as an array (top → bottom). |
| `iterator()` | Iterates through stack elements from top to bottom. |
| `toString()` | Returns a string representation of the stack. |
| `ensureCapacity(int newCapacity)` | Makes sure that the needed capacity is met (Only in dynamic stacks) |
| `trimToSize()` | Trims the stack to its current size to reduce memory usage (Only in dynamic stacks) |

---

## Benchmarks

Benchmark: Push + Pop operations on a stack with 10,000 elements.

*The stacks **were** pre-resized*
| Implementation | Throughput |
|----------------|------------|
| `IntStack (Unsafe)` | ***1.7M ops/ms*** |
| `IntStack (Safe)` | ***1.4M ops/ms*** |
| `FastUtil IntArrayList` | ***1.7M ops/ms*** |
| `Eclipse MutableIntStack` | ***0.6M ops/ms*** |

*The stacks **were not** pre-resized*
| Implementation | Throughput |
|----------------|------------|
| `IntStack (Unsafe)` | ***1.7M ops/ms*** |
| `IntStack (Safe)` | ***1.4M ops/ms*** |
| `FastUtil IntArrayList` | ***0.9M ops/ms*** |
| `Eclipse MutableIntStack` | ***0.6M ops/ms*** |

### Notes
- Results may vary depending on hardware, JVM version, and benchmark methodology.
- Unsafe operations skip safety checks and are intended for performance-critical code.
- Safe operations include bounds and capacity validation while remaining highly competitive with other primitive collections.
- The Benchmarks were done using JMH with 5 Warmups 10 Iterations and 2 Forks

---

## Future Additions

1. `ObjectStack<T>`
2. `CharStack`

---

## License

This project is licensed under the MIT License.
