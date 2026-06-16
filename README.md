# JStackLight

A lightweight, easy-to-use, low-allocation primitive stack for Java designed for hot loops and performance-critical systems.

---

## Why JStackLight?

Java’s standard stack-like structures (such as `ArrayDeque<Integer>`) use boxed types, which introduce:

- Extra memory allocation per element
- Boxing / unboxing overhead
- Reduced performance in tight loops

JStackLight:

- Reduces boxing overhead
- Minimizes GC pressure
- Provides predictable and stable performance
- Provides fast O(1) operations

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
2. Open IntelliJ IDEA and load your project
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

### Unchecked Operations

*These methods skip validation to provide max performance*

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

> *DISCLAIMER*
> 
> Use the unchecked API only when correctness is already guaranteed by surrounding code.

---

## Benchmarks

The benchmarks were executed using JMH

### Configuration
- Mode: Average Time
- Warmup: 5 Iterations
- Measurement: 10 Iterations
- Forks: 2

### Rules

- Pre-sized data structures to remove resizing bias
- Each benchmark executes full stack workloads (push/pop loops) to simulate realistic usage patterns.
- Blackhole is used where required to prevent dead-code elimination

### Results (*ns/op*)

#### 100 Elements

| Operation | ArrayDeque<Integer> | FastUtil | JStackLight (Safe) | JStackLight (Unsafe) |
| --------- | ------------------- | -------- | ------------------ | -------------------- |
| push      | 91.7                | 24.3     | 43.0               | 22.9                 |
| pop       | 161.2               | 39.1     | 49.4               | 31.0                 |
| peek      | 97.8                | 24.1     | 23.3               | 23.2                 |
| push+pop  | 161.1               | 39.2     | 74.4               | 31.0                 |


#### 10000 Elements

| Operation | ArrayDeque<Integer> | FastUtil | JStackLight (Safe) | JStackLight (Unsafe) |
| --------- | ------------------- | -------- | ------------------ | -------------------- |
| push      | 13488               | 298.9    | 2810.3             | 292.6                |
| pop       | 19099               | 2071.6   | 3014.5             | 1458.9               |
| peek      | 14058               | 266.2    | 291.6              | 299.1                |
| push+pop  | 19488               | 2044.7   | 6484.1             | 1440.9               |



### Notes
- Results may vary depending on hardware, JVM version, and benchmark methodology.
- `ArrayDeque<Integer>` is slower due to boxing overhead.
- `FastUtil` represents a high-performance primitive baseline.
- `JStackLight` Unsafe provides maximum throughput by removing validation checks.
- `JStackLight` Safe includes bounds checking and fail-fast behavior, which introduces overhead but ensures correctness.

### Benchmark Code

```java
package org.example;

import com.xJavaFlamex.JStackLight.IntStack;
import it.unimi.dsi.fastutil.ints.IntArrayList;
import org.openjdk.jmh.annotations.*;
import org.openjdk.jmh.infra.Blackhole;

import java.util.ArrayDeque;
import java.util.concurrent.TimeUnit;

@BenchmarkMode(Mode.AverageTime)
@OutputTimeUnit(TimeUnit.NANOSECONDS)
@Warmup(iterations = 5, time = 1)
@Measurement(iterations = 10, time = 1)
@Fork(2)
@State(Scope.Thread)
public class App {
    @Param({"100", "10000"})
    public int size;

    private IntStack stack;
    private IntArrayList fastutil;
    private ArrayDeque<Integer> deque;

    @Setup(Level.Invocation)
    public void setup() {
        stack = new IntStack(size);
        fastutil = new IntArrayList(size);
        deque = new ArrayDeque<>(size);
    }

    @Benchmark
    public void pushSafe() {
        for (int i = 0; i < size; i++) {
            stack.push(i);
        }
    }

    @Benchmark
    public void pushUnsafe() {
        for (int i = 0; i < size; i++) {
            stack.pushUnsafe(i);
        }
    }

    @Benchmark
    public void pushFastUtil() {
        for (int i = 0; i < size; i++) {
            fastutil.add(i);
        }
    }

    @Benchmark
    public void pushArrayDeque() {
        for (int i = 0; i < size; i++) {
            deque.push(i);
        }
    }

    @Benchmark
    public void popSafe(Blackhole bh) {

        for (int i = 0; i < size; i++) {
            stack.pushUnsafe(i);
        }

        for (int i = 0; i < size; i++) {
            bh.consume(stack.pop());
        }
    }

    @Benchmark
    public void popUnsafe(Blackhole bh) {

        for (int i = 0; i < size; i++) {
            stack.pushUnsafe(i);
        }

        for (int i = 0; i < size; i++) {
            bh.consume(stack.popUnsafe());
        }
    }

    @Benchmark
    public void popFastUtil(Blackhole bh) {

        for (int i = 0; i < size; i++) {
            fastutil.add(i);
        }

        for (int i = 0; i < size; i++) {
            bh.consume(fastutil.removeInt(fastutil.size() - 1));
        }
    }

    @Benchmark
    public void popArrayDeque(Blackhole bh) {

        for (int i = 0; i < size; i++) {
            deque.push(i);
        }

        for (int i = 0; i < size; i++) {
            bh.consume(deque.pop());
        }
    }

    @Benchmark
    public void peekSafe(Blackhole bh) {

        for (int i = 0; i < size; i++) {
            stack.pushUnsafe(i);
        }

        bh.consume(stack.peek());
    }

    @Benchmark
    public void peekUnsafe(Blackhole bh) {

        for (int i = 0; i < size; i++) {
            stack.pushUnsafe(i);
        }

        bh.consume(stack.peekUnsafe());
    }

    @Benchmark
    public void peekFastUtil(Blackhole bh) {

        for (int i = 0; i < size; i++) {
            fastutil.add(i);
        }

        bh.consume(fastutil.getInt(fastutil.size() - 1));
    }

    @Benchmark
    public void peekArrayDeque(Blackhole bh) {

        for (int i = 0; i < size; i++) {
            deque.push(i);
        }

        bh.consume(deque.peek());
    }

    @Benchmark
    public void pushPopSafe(Blackhole bh) {

        for (int i = 0; i < size; i++) {
            stack.push(i);
        }

        for (int i = 0; i < size; i++) {
            bh.consume(stack.pop());
        }
    }

    @Benchmark
    public void pushPopUnsafe(Blackhole bh) {

        for (int i = 0; i < size; i++) {
            stack.pushUnsafe(i);
        }

        for (int i = 0; i < size; i++) {
            bh.consume(stack.popUnsafe());
        }
    }

    @Benchmark
    public void pushPopFastUtil(Blackhole bh) {

        for (int i = 0; i < size; i++) {
            fastutil.add(i);
        }

        for (int i = 0; i < size; i++) {
            bh.consume(fastutil.removeInt(fastutil.size() - 1));
        }
    }

    @Benchmark
    public void pushPopArrayDeque(Blackhole bh) {

        for (int i = 0; i < size; i++) {
            deque.push(i);
        }

        for (int i = 0; i < size; i++) {
            bh.consume(deque.pop());
        }
    }
}
```

---

## Future Additions

1. `ObjectStack<T>`
2. `CharStack`

---

## License

This project is licensed under the MIT License.
