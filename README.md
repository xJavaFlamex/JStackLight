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
- Fixed-capacity stacks (Only for Fixed Stacks)
- LIFO iteration support
- Unsafe API for maximum performance
- Safe API for predictability and error handling
- Lightweight and fast
- Simple and easy-to-use
- Fail-fast iterator

### Available Stack Types

Dynamic (auto-growing)
- *IntStack*
- *DoubleStack*
- *FloatStack*
- *LongStack*

Fixed (fixed capacity)
- *FixedIntStack*
- *FixedDoubleStack*
- *FixedFloatStack*
- *FixedLongStack*

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
IntStack stack = new IntStack();

stack.push(10);
stack.push(20);
stack.push(30);

System.out.println(stack.pop());  // 30
System.out.println(stack.peek()); // 20
System.out.println(stack.capacity()); // 16
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

### Unsafe API

The unsafe API (`pushUnsafe()`, `popUnsafe()` ect.) is used for max performance and it achives that by removing error handling.

*DISCLAIMER*

Use only when correctness is already guaranteed by surrounding code.

---

## Benchmarks

Benchmark: Push + Pop operations on a stack with 100 and 10,000 elements.

*The stacks **were** pre-resized*
| Implementation | Throughput | Elements |
|----------------|------------|----------|
| `IntStack (Unsafe)` | ***31.0k ops/ms*** | 100 |
| `FastUtil IntArrayList` | ***24.0k ops/ms*** | 100 |
| `IntStack (Safe)` | ***12.0k ops/ms*** | 100 |
| `ArrayDeque<Integer>` | ***5.0k ops/ms*** | 100 |
| `Eclipse MutableIntStack` | ***4.0k ops/ms*** | 100 |

| Implementation | Throughput | Elements |
|----------------|------------|----------|
| `IntStack (Unsafe)` | ***670 ops/ms*** | 10000 |
| `FastUtil IntArrayList` | ***471 ops/ms*** | 10000 |
| `IntStack (Safe)` | ***150 ops/ms*** | 10000 |
| `ArrayDeque<Integer>` | ***45 ops/ms*** | 10000 |
| `Eclipse MutableIntStack` | ***50 ops/ms*** | 10000 |

### Analysis

**JStackLight (Unsafe)**

Provides the highest throughput due to removal of safety checks and minimal overhead. Best suited for performance-critical code where capacity is guaranteed externally.

**JStackLight (Safe)**

Offers a balance between correctness and performance. Slight overhead is introduced due to bounds checking and capacity management.

**FastUtil**

fastutil demonstrates strong performance in large workloads due to highly optimized primitive array structures.

**Eclipse Collections**

Eclipse Collections prioritizes general-purpose flexibility, resulting in slightly higher overhead in stack-specific workloads.

**ArrayDeque**

Standard JDK implementation using boxed Integer values, introducing allocation and boxing overhead.

### Benchmark Code

package org.example;

```java
import com.xJavaFlamex.JStackLight.IntStack;
import it.unimi.dsi.fastutil.ints.IntArrayList;
import org.eclipse.collections.api.factory.primitive.IntStacks;
import org.eclipse.collections.api.stack.primitive.MutableIntStack;
import org.openjdk.jmh.annotations.*;
import org.openjdk.jmh.infra.Blackhole;

import java.util.ArrayDeque;
import java.util.concurrent.TimeUnit;

@BenchmarkMode(Mode.Throughput)
@OutputTimeUnit(TimeUnit.MILLISECONDS)
@Warmup(iterations = 5, time = 1)
@Measurement(iterations = 10, time = 1)
@Fork(2)
@State(Scope.Thread)
public class App {

    @Param({"100", "10000"})
    public int size;

    private IntStack myStack;
    private IntArrayList fastutil;
    private MutableIntStack eclipse;
    private ArrayDeque<Integer> deque;
    
    @Setup(Level.Invocation)
    public void setup() {
        myStack = new IntStack(size);
        fastutil = new IntArrayList(size);
        eclipse = IntStacks.mutable.empty();
        deque = new ArrayDeque<>(size);
    }
    
    @Benchmark
    public void jstack_safe(Blackhole bh) {
        for (int i = 0; i < size; i++) {
            myStack.push(i);
        }
        for (int i = 0; i < size; i++) {
            bh.consume(myStack.pop());
        }
    }
    
    @Benchmark
    public void jstack_unsafe(Blackhole bh) {
        for (int i = 0; i < size; i++) {
            myStack.pushUnsafe(i);
        }
        for (int i = 0; i < size; i++) {
            bh.consume(myStack.popUnsafe());
        }
    }
    
    @Benchmark
    public void fastutil(Blackhole bh) {
        for (int i = 0; i < size; i++) {
            fastutil.add(i);
        }
        for (int i = 0; i < size; i++) {
            bh.consume(fastutil.removeInt(fastutil.size() - 1));
        }
    }
    
    @Benchmark
    public void eclipse(Blackhole bh) {
        for (int i = 0; i < size; i++) {
            eclipse.push(i);
        }
        for (int i = 0; i < size; i++) {
            bh.consume(eclipse.pop());
        }
    }
    
    @Benchmark
    public void arrayDeque(Blackhole bh) {
        for (int i = 0; i < size; i++) {
            deque.push(i);
        }
        for (int i = 0; i < size; i++) {
            bh.consume(deque.pop());
        }
    }
}
```

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
