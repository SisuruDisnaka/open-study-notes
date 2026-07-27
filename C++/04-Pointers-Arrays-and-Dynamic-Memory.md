[⬅ Previous](./03-Functions-and-Overloading.md) &nbsp;|&nbsp; [🏠 C++ Course Home](./README.md) &nbsp;|&nbsp; Next ➡ [05. Introduction to OOP & Classes](./05-Introduction-to-OOP-and-Classes.md)

---

# 📘 4. Pointers, Arrays & Dynamic Memory

| | |
|---|---|
| **Difficulty** | 🟡 Intermediate |
| **Estimated Reading Time** | ~16 minutes |
| **Prerequisites** | [Lesson 2: Variables, References & I/O](./02-Variables-References-and-IO.md) |

**Progress**
```
████████░░░░░░░░░░░░░░░░░░  Lesson 4 of 15
```

---

## 🌟 Why Learn This?

References cover most day-to-day cases, but sometimes you genuinely need memory that outlives the function that created it, or a variable that might legitimately point to "nothing." That's what pointers and dynamic memory are for.

- Every data structure you'll build later (linked lists, trees) is fundamentally a network of pointers.
- Understanding `new`/`delete` here sets up Lesson 14's smart pointers, which automate the cleanup you're about to do manually.

---

## 🎯 By the End of This Lesson

You should know

✔ How C++ pointers work - identical to C, with a few extra tools

✔ How arrays and pointers relate to each other in memory

✔ How to allocate and free memory on the heap with `new`/`delete`

✔ What a **memory leak** and a **dangling pointer** are, and how to avoid both

✔ Why `std::array` and `std::vector` are usually better choices than raw arrays

---

## 📚 Table of Contents

- [Before We Start](#-before-we-start)
- [Imagine This...](#-imagine-this)
- [Core Concepts](#-core-concepts)
- [Behind the Scenes](#-behind-the-scenes)
- [Visual Explanation](#-visual-explanation)
- [Code Example](#-code-example)
- [Predict the Output](#-predict-the-output)
- [Try It Yourself](#-try-it-yourself)
- [Mini Challenge](#-mini-challenge)
- [Real World Applications](#-real-world-applications)
- [Memory Tricks](#-memory-tricks)
- [Fun Fact](#-fun-fact)
- [Common Mistakes](#-common-mistakes)
- [Beginner Traps](#-beginner-traps)
- [Exam Tips](#-exam-tips)
- [Interview Questions](#-interview-questions)
- [Quiz](#-quiz)
- [Summary](#-summary)
- [What's Next?](#-whats-next)
- [References](#-references)

---

## 🧠 Before We Start

You should be comfortable with:

- References from Lesson 2 (pointers are their lower-level cousin).
- Basic pointer syntax if you've done C - not required, but this lesson moves quickly if so.

---

## 💡 Imagine This...

A **stack variable** is like a hotel room booked for exactly the length of your stay - the moment you check out (the function returns), the room is cleared for the next guest, whether you wanted it kept or not.

A **heap allocation** (`new`) is like renting an apartment - it stays yours for as long as you want, *but nobody automatically ends the lease for you*. If you move out without formally canceling it (`delete`), you keep paying rent on an apartment nobody's using - that's a **memory leak**.

---

## 📖 Core Concepts

### Pointers (Quick Refresher)

```cpp
int age = 20;
int *ptr = &age;    // ptr stores the ADDRESS of age

std::cout << *ptr;   // dereference: prints 20 (the VALUE at that address)
std::cout << ptr;    // prints the address itself
```

| Symbol | Meaning |
|---|---|
| `&x` | "address of x" |
| `*ptr` | "the value ptr points to" (dereference) |
| `int *ptr` | declares ptr as a pointer to an int |

### Arrays and Pointers

An array's name naturally **decays** into a pointer to its first element:

```cpp
int scores[3] = {90, 85, 70};
int *ptr = scores;      // no & needed - scores already decays to a pointer

std::cout << *(ptr + 1);  // 85 - pointer arithmetic moves by sizeof(int)
std::cout << scores[1];    // 85 - identical result, cleaner syntax
```

`arr[i]` is literally defined as shorthand for `*(arr + i)` - array indexing *is* pointer arithmetic wearing a friendlier costume.

### Dynamic Memory: `new` and `delete`

| C | C++ |
|---|---|
| `int *p = (int*) malloc(sizeof(int));` | `int *p = new int;` |
| `free(p);` | `delete p;` |
| `int *arr = (int*) malloc(5 * sizeof(int));` | `int *arr = new int[5];` |
| `free(arr);` | `delete[] arr;` |

```cpp
int *ptr = new int(42);   // heap-allocate one int, initialized to 42
std::cout << *ptr;         // 42
delete ptr;                 // frees the memory
ptr = nullptr;               // good practice: avoid a dangling pointer
```

> **Rule:** every `new` needs exactly one matching `delete` (and every `new[]` needs `delete[]`, not `delete`). Mismatching them is undefined behavior.

### The Two Classic Memory Bugs

| Bug | What happens | Cause |
|---|---|---|
| **Memory leak** | Allocated memory is never freed | Forgetting `delete`, or losing the only pointer to it |
| **Dangling pointer** | A pointer still points to memory that's already been freed | Using `ptr` after `delete ptr;` without resetting it |

### Safer Alternatives

```cpp
#include <array>
#include <vector>

std::array<int, 3> fixedScores = {90, 85, 70};   // fixed size, stack-based, bounds-checkable
std::vector<int> dynamicScores = {90, 85, 70};    // resizable, manages its own heap memory
dynamicScores.push_back(100);                       // grows automatically - no new/delete needed
```

Modern C++ style strongly favors `std::vector` and `std::array` over raw `new[]`/`delete[]` arrays - you'll use `std::vector` constantly starting in Lesson 12.

---

## 🔍 Behind the Scenes

Every running program has (at minimum) two memory regions relevant here:

```
+--------------------+
|       Stack         |  ◄── local variables, function calls - fast, automatic cleanup
+--------------------+
|       Heap           |  ◄── new/delete memory - flexible, but YOU manage its lifetime
+--------------------+
```

- The **stack** cleans itself up automatically the instant a function returns - that's why a local `int x` disappears when its function ends.
- The **heap** has no such automatic timer - memory allocated with `new` stays reserved until an explicit `delete`, even after every pointer to it has gone out of scope.
- That's precisely how a **memory leak** happens: the memory is still reserved, but nothing left in your program can reach it to free it.

---

## 🖥 Visual Explanation

```
int *ptr = new int(42);

Stack:                Heap:
+---------+           +----+
|   ptr   |──────────►| 42 |
+---------+           +----+

delete ptr;   // heap memory is freed

Stack:                Heap:
+---------+           +----+
|   ptr   |──────────►| ?? |  ◄── ptr is now DANGLING - still points here, but it's invalid
+---------+           +----+

ptr = nullptr;   // now ptr safely points to nothing

Stack:
+---------+
|   ptr   |──► nullptr
+---------+
```

---

## 💻 Code Example

```cpp
#include <iostream>

int main() {
    int *scores = new int[3];   // dynamically allocate an array of 3 ints
    scores[0] = 90;
    scores[1] = 85;
    scores[2] = 70;

    for (int i = 0; i < 3; i++) {
        std::cout << "Score " << i << ": " << scores[i] << std::endl;
    }

    delete[] scores;   // must match new[] with delete[]
    scores = nullptr;
    return 0;
}
```

**Expected Output:**
```
Score 0: 90
Score 1: 85
Score 2: 70
```

**Step-by-step execution:**
1. `new int[3]` reserves space for 3 integers on the heap and returns a pointer to the first one.
2. `scores[0]`, `scores[1]`, `scores[2]` use array-indexing syntax, even though `scores` is a raw pointer - this works because indexing *is* pointer arithmetic.
3. The loop prints each value.
4. `delete[] scores;` frees all 3 integers' worth of memory at once - `delete` (without `[]`) here would be a bug.
5. Setting `scores = nullptr;` afterward avoids leaving a dangling pointer around.

**Why it works:** because array indexing and pointer arithmetic are the same operation under the hood, a raw heap-allocated array behaves identically to a stack array for everyday use - the only extra responsibility is remembering to free it.

---

## 🎮 Predict the Output

```cpp
#include <iostream>
using namespace std;

int main() {
    int *ptr = new int(10);
    cout << *ptr << endl;
    delete ptr;
    ptr = nullptr;
    if (ptr == nullptr) {
        cout << "ptr is safely null" << endl;
    }
    return 0;
}
```

<details>
<summary>💡 Reveal the answer</summary>

```
10
ptr is safely null
```

The value is read before deletion, then the pointer is explicitly reset - this is exactly the defensive pattern that prevents dangling-pointer bugs.
</details>

---

## 🧪 Try It Yourself

Modify the code example above to:
1. Change the array size to 5 and fill it with values you choose.
2. Deliberately write `delete scores;` instead of `delete[] scores;`, and look up why this is undefined behavior (most compilers won't catch it for you).
3. Replace the whole example with a `std::vector<int>` and notice you no longer need `new`, `delete`, or a fixed size at all.

---

## 🎯 Mini Challenge

Write a function `int* createSquares(int n)` that heap-allocates an array of `n` integers, fills it with `1², 2², ..., n²`, and returns the pointer. In `main`, call it, print the results, then free the memory correctly.

<details>
<summary>💡 Need a hint?</summary>

The function needs `new int[n]`; the caller (not the function) is responsible for calling `delete[]` once it's done using the array.
</details>

---

## 🌍 Real World Applications

| Concept | Where it shows up |
|---|---|
| Pointers | Every data structure (linked lists, trees, graphs) you'll build in future courses |
| `new`/`delete` | Game engines managing custom memory pools for performance |
| `std::vector` | The default "just give me a resizable array" choice in nearly all modern C++ code |

---

## 🧠 Memory Tricks

- **Stack = hotel room, cleaned automatically at checkout. Heap = apartment, YOU cancel the lease.**
- **`new[]` and `delete[]` are a matched pair - mixing `new[]` with plain `delete` is like returning a car with the wrong set of keys.**

---

## 🎉 Fun Fact

Memory leaks were such a notorious source of real-world bugs and security vulnerabilities that an entire category of tools - like Valgrind - exists purely to hunt them down. Modern C++ (Lesson 14's smart pointers) was largely designed to make this class of tool unnecessary.

---

## ⚠ Common Mistakes

```cpp
// ❌ Wrong - allocated with new[], freed with delete (no brackets)
int *arr = new int[10];
delete arr;

// ✅ Correct - matching new[] / delete[]
int *arr = new int[10];
delete[] arr;
```
*Why:*
- `delete` only knows how to destroy one object.
- `delete[]` knows how to walk the whole array and destroy each element.
- Using the wrong one causes undefined behavior - it may work by accident on simple types and silently corrupt memory on others.

---

## 🚫 Beginner Traps

- **"Once I `delete` a pointer, it becomes `nullptr` automatically."** False - `delete` frees the memory but leaves the pointer's value (the old address) untouched, which is exactly what makes it dangling. You must set it to `nullptr` yourself.
- **"`std::vector` is slower, so I should always use raw arrays for performance."** In practice, `std::vector` is implemented as a thin wrapper around a heap array and performs virtually identically, while eliminating manual memory bugs entirely.

---

## 📌 Exam Tips

- Know the four allocation pairs cold: `new`/`delete`, `new[]`/`delete[]`, and their `malloc`/`free` equivalents from C.
- Be ready to define "memory leak" and "dangling pointer" precisely and distinguish them - a very common short-answer pairing.
- `arr[i]` ≡ `*(arr + i)` is a frequently tested identity - know it exactly.

---

## 🎤 Interview Questions

**Q: What is the difference between a memory leak and a dangling pointer?**
- A memory leak is allocated memory that's never freed - it stays reserved forever, wasting resources.
- A dangling pointer is a pointer that still holds the address of memory that has already been freed - using it is undefined behavior.

**Q: Why does `std::vector` reduce memory bugs compared to raw pointers?**
- It manages its own heap allocation internally, automatically freeing memory when it goes out of scope (RAII, covered in Lesson 6).
- It handles resizing automatically, removing the need for manual `new[]`/`delete[]` calls entirely.

---

## ❓ Quiz

**Multiple Choice**

1. What does `new int[5]` do? <br>
   A) Allocates 5 bytes  
   B) Allocates space for 5 integers on the heap  
   C) Creates a stack array  
   D) Frees memory

2. What must match `new[]`? <br>
   A) `delete`  
   B) `free`  
   C) `delete[]`  
   D) Nothing - it cleans itself up

3. What is a dangling pointer? <br>
   A) A pointer that was never initialized  
   B) A pointer to memory that has already been freed  
   C) A pointer to a stack variable  
   D) A null pointer

4. What does `arr[i]` translate to internally? <br>
   A) `arr + i`  
   B) `*(arr + i)`  
   C) `&arr[i]`  
   D) `i[arr]` is invalid  

5. Which is generally preferred in modern C++ over a raw dynamic array? <br>
   A) `malloc`  
   B) `std::vector`  
   C) `new[]` directly  
   D) A global array

<details><summary>✅ Reveal Answers</summary>

1. B  2. C  3. B  4. B  5. B
</details>

**True / False**

1. Stack memory is automatically freed when a function returns.
2. `delete ptr;` automatically sets `ptr` to `nullptr`.
3. Array indexing and pointer arithmetic are fundamentally the same operation in C++.

<details><summary>✅ Reveal Answers</summary>

1. True  2. False  3. True
</details>

**Short Answer**

1. Why is it good practice to set a pointer to `nullptr` immediately after calling `delete` on it?

<details><summary>✅ Reveal Guidance</summary>

- `delete` frees the memory but doesn't change the pointer's stored address, so the pointer still "looks" valid even though the memory behind it is gone.
- Setting it to `nullptr` afterward makes any accidental future use immediately detectable - dereferencing `nullptr` crashes loudly.
- Without that reset, you get a dangling-pointer bug instead, which silently corrupts memory and fails unpredictably.
</details>

---

## 📝 Summary

You now understand how pointers and arrays relate to each other, how to manage heap memory manually with `new`/`delete`, and the two classic bugs - memory leaks and dangling pointers - that manual memory management invites. You've also seen that `std::array` and `std::vector` solve most of these problems automatically, which is why modern C++ style reaches for them first.

---

## 🚀 What's Next?

In the next lesson, you'll take the biggest conceptual leap of this course: **object-oriented programming and classes** - bundling data and behavior into a single reusable unit for the first time.

---

## 📚 References

- Stroustrup, B. - *The C++ Programming Language* (4th Edition), Addison-Wesley.
- ISO/IEC 14882 - the official C++ Language Standard.

---

[⬅ Previous](./03-Functions-and-Overloading.md) &nbsp;|&nbsp; [🏠 C++ Course Home](./README.md) &nbsp;|&nbsp; Next ➡ [05. Introduction to OOP & Classes](./05-Introduction-to-OOP-and-Classes.md)
