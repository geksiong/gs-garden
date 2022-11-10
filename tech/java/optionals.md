---
title: Optionals in Java
date: 2022-10-24

tags:
- java
---

# Optionals in Java

`null` check is a code smell.

Do not return null, instead return an empty collection (`Optional.empty() `)

What if we are returning a single value? In the past, we return null. Now, we should return `Option<T>`.

## When to use Optionals, or not

- If a result is a Collection, *do not* use `Optional`.
- If a method will always return some value, *do not* use `Optionaal`.
- If a method may or may not return a single value as a result, *then* use `Optional`.
- Do not use `Optional<T>` as a parameter to methods. If needed, use overloading instead.
- There is little reason to use `Optional` as a field.

---
**Sources**

1. Design Patterns Revisited in Modern Java by Venkat Subramaniam
