# JStackLight

A lightweight, high-performance primitive stack library for Java designed to eliminate boxing overhead and provide fast stack operations using array-backed implementations.

---

## 🚀 Why JStackLight?

Java’s standard stack-like structures (such as `ArrayDeque<Integer>`) use boxed types, which introduce:

- Extra memory allocation per element
- Boxing / unboxing overhead
- Reduced performance in tight loops

JStackLight avoids these issues by using primitive arrays directly.

---

## ⚡ Features

- Primitive stacks (no boxing/unboxing)
  - `IntStack`
  - `LongStack`
  - `FloatStack`
  - `DoubleStack`

- Fixed-capacity stacks (no resizing)
  - `FixedIntStack`
  - `FixedLongStack`
  - `FixedFloatStack`
  - `FixedDoubleStack`

- Dynamic resizing stacks (auto-growing arrays)
- LIFO iteration support
- Optional unsafe methods for maximum performance
- No external dependencies
- Lightweight and fast
- Simple and easy-to-use API

---

## 📦 Installation

Download the source files and include them in your project:

- `IntStack.java`
- `LongStack.java`
- `FloatStack.java`
- `DoubleStack.java`
- `FixedIntStack.java`
- `FixedLongStack.java`
- `FixedFloatStack.java`
- `FixedDoubleStack.java`

Place them inside your Java project source folder.

---

## 🚀 Quick Start

```java
IntStack stack = new IntStack();

stack.push(10);
stack.push(20);
stack.push(30);

System.out.println(stack.pop());  // 30
System.out.println(stack.peek()); // 20
