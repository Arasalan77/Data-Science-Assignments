# Python OOP — Operator Overloading (Set 4)

This README answers five targeted questions about **operator overloading** and **sequence/iteration protocols** in Python classes, with concise explanations, best practices, and runnable examples.

---

## 🔎 Quick Reference

| Purpose | Implement | Notes |
|---|---|---|
| Make objects **iterable** | `__iter__` (and iterator’s `__next__`) | Preferred modern protocol. `__iter__` returns an iterator. |
| Legacy/sequence-based iteration | `__getitem__` | If `__iter__` is absent, Python repeatedly calls `obj[0]`, `obj[1]`, … until `IndexError`. |
| User-facing printing | `__str__` | Used by `print(obj)` and `str(obj)`. |
| Developer/debug printing | `__repr__` | Used by `repr(obj)`, interactive REPL, and inside containers. Fallback for `__str__` if not defined. |
| Indexing & slicing (read) | `__getitem__(key)` | `key` may be an `int` or a `slice` (`slice(start, stop, step)`). |
| Indexing & slicing (write) | `__setitem__(key, value)` | Same `key` rules as `__getitem__`. |
| Indexing & slicing (delete) | `__delitem__(key)` | Same `key` rules as above. |
| In‑place addition | `__iadd__(self, other)` | Handles `+=`. If missing, Python falls back to `__add__`. |

---

## Q1. Which two operator overloading methods can you use in your classes to support iteration?

**Two ways to support iteration:**

1) **Iterator protocol (preferred):** Implement `__iter__` to return an iterator whose class defines `__next__`.
2) **Sequence protocol (legacy/back-compat):** Implement `__getitem__` to return successive items starting from index `0` and raise `IndexError` to stop.

```python
class Countdown:
    def __init__(self, n): self.n = n
    def __iter__(self):                # iterator protocol
        current = self.n
        while current > 0:
            yield current
            current -= 1

class SeqStyle:
    def __init__(self, data): self.data = list(data)
    def __getitem__(self, idx):        # sequence protocol
        return self.data[idx]          # IndexError naturally stops iteration
```

---

## Q2. In what contexts do the two operator overloading methods manage printing?

- `__str__(self)`: **User‑friendly** string, used by `print(obj)`, `str(obj)`, and f-strings like `f"{obj}"` (via `__format__` fallback).
- `__repr__(self)`: **Unambiguous/developer** string, used by `repr(obj)`, the REPL, stack traces, and container displays (e.g., lists of objects). If `__str__` is not defined, Python falls back to `__repr__` for `str(obj)`.

```python
class Person:
    def __init__(self, name): self.name = name
    def __repr__(self): return f"Person(name={self.name!r})"   # dev view
    def __str__(self):  return self.name                       # user view

p = Person("Ada")
print(str(p))   # Ada
print(repr(p))  # Person(name='Ada')
```

---

## Q3. In a class, how do you intercept slice operations?

Implement the item access methods and check for a `slice` key:

- Read: `__getitem__(key)`
- Write: `__setitem__(key, value)`
- Delete: `__delitem__(key)`

```python
class Window:
    def __init__(self, data): self.data = list(data)

    def __getitem__(self, key):
        if isinstance(key, slice):
            return Window(self.data[key])       # return same-type view/copy
        return self.data[key]

    def __setitem__(self, key, value):
        if isinstance(key, slice):
            self.data[key] = value              # sequence replacement
        else:
            self.data[key] = value

    def __delitem__(self, key):
        del self.data[key]                       # works for int or slice
```

> Note: For multi-dimensional keys (e.g., `obj[i, j]`), `key` will be a `tuple`; you decide how to interpret it.

---

## Q4. In a class, how do you capture in-place addition?

Implement `__iadd__(self, other)` for `+=`. If it’s not present, Python tries `__add__` and rebinds the result to the target name.

- **Mutable types** typically **mutate in place** and return `self`.
- **Immutable types** should return a **new object**.

```python
class Bag:
    def __init__(self, items=None): self.items = list(items or [])
    def __iadd__(self, other):
        if isinstance(other, Bag):
            self.items.extend(other.items)
        else:
            self.items.append(other)
        return self                         # in-place, returns self
```

---

## Q5. When is it appropriate to use operator overloading?

Use operator overloading when it **aligns with natural domain semantics** and **improves readability** without surprising users:

- ✅ Mathematical/structured types: vectors, matrices, complex numbers, dates/times, units.
- ✅ Collection-like behavior: indexing, slicing, iteration for custom containers.
- ✅ String/debug views: always implement a good `__repr__`, and optionally `__str__`.

**Best practices checklist**

- Implement operators **consistently** (e.g., if `__eq__` then consider `__hash__` for immutable types; if `__add__`, consider `__radd__`/`__iadd__`).
- Maintain **type safety** and clear error messages (`TypeError` for unsupported types).
- Preserve **immutability** contracts (return new objects for immutable classes).
- Avoid **surprising semantics** (don’t overload `+` to do non-additive actions).
- Keep performance in mind; prefer simple, predictable implementations.

---

## ✅ Minimal Example Putting It All Together

```python
class Vector:
    def __init__(self, x, y): self.x, self.y = x, y

    # printing
    def __repr__(self): return f"Vector({self.x}, {self.y})"
    def __str__(self):  return f"⟨{self.x}, {self.y}⟩"

    # iteration
    def __iter__(self):
        yield self.x
        yield self.y

    # indexing & slicing
    def __getitem__(self, key):
        data = (self.x, self.y)
        if isinstance(key, slice):
            return data[key]                # tuple slice
        return data[key]

    # addition
    def __add__(self, other):
        if not isinstance(other, Vector):
            return NotImplemented
        return Vector(self.x + other.x, self.y + other.y)

    def __iadd__(self, other):
        if not isinstance(other, Vector):
            return NotImplemented
        self.x += other.x; self.y += other.y
        return self
```

---

### © Notes
- Tested behavior applies to Python 3.x.
- For formatting with f-strings, Python uses `__format__`, which by default falls back to `__str__` unless you supply a custom format.

