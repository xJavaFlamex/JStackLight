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

## ⚡ Features

- Primitive stacks (no boxing/unboxing)
- Fixed-capacity stacks (no resizing)
- Dynamic resizing stacks (auto-growing arrays)
- LIFO iteration support
- Unsafe API for maximum performance
- Safe API for predictability and error handling
- Lightweight and fast
- Simple and easy-to-use

---

## Installation

1. Download the jar file from the releases
2. Open Intellij and go to your project'
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

