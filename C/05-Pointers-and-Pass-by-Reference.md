[⬅ Previous: 04. Operators & Type Conversion](./04-Operators-and-Type-Conversion.md) &nbsp;|&nbsp; [🏠 C Course Home](./README.md) &nbsp;|&nbsp; Next ➡ [06. Decision Making](./06-Decision-Making.md)

---

# 📘 5. Pointers & Pass-by-Reference

| | |
|---|---|
| **Difficulty** | 🟡 Intermediate |
| **Estimated Reading Time** | ~20 minutes |
| **Prerequisites** | [Operators & Type Conversion](./04-Operators-and-Type-Conversion.md) |

**Progress**
```
██████████░░░░░░░░░░░░░░░░  Lesson 5 of 13
```

---

## 🌟 Why Learn This?

Take a breath. A few honest facts before we start:

- This is the topic students fear most - and also the single most tested topic in the entire course, and the one interviewers ask about the most.
- It's genuinely not complicated once the *mental model* clicks.
- Pointers are why C can talk directly to hardware, build data structures like linked lists, and share large chunks of memory between functions without wastefully copying it.
- They are the foundation of everything "systems-level" about C.

---

## 🎯 By the End of This Lesson

You should know

✔ What a memory address is, and what a pointer actually stores

✔ The two completely different jobs of the `*` symbol

✔ The real difference between pass-by-value and pass-by-reference

✔ Why C is technically "always pass by value," even when pointers are involved

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
- [Challenge Yourself](#-challenge-yourself)
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

Make sure you're solid on functions and parameters from [Lesson 3](./03-Functions-and-Modular-Programming.md) - pointers will click much faster with that foundation in place.

---

## 💡 Imagine This...

Every house on a street has an **address**.

- If I hand you a slip of paper with my house's address written on it, I haven't given you my house - I've given you *directions to find it*.
- You can go to that address, walk in, and rearrange my furniture.
- The paper itself is small and easy to carry, but it gives you full access to something much bigger.

A **pointer** is exactly that slip of paper:

- It doesn't hold a value directly - it holds an *address*, directions to where a value lives in memory.
- Just like the paper lets you walk in and rearrange furniture, a pointer lets a function reach outside its own private workspace and directly modify a variable that technically belongs to someone else.

---

## 📖 Core Concepts

Every variable lives somewhere in memory, and that "somewhere" has an address - just like every house has a street address. A **pointer** is simply a variable that stores *an address* instead of storing an ordinary value.

```c
int a = 10;
int *p;      // p is declared as "a pointer to an int"
p = &a;      // p now stores the ADDRESS of a (not the value 10!)
printf("%d", *p);   // *p means "go to that address and give me the value stored there" → 10
```

| Symbol | Meaning |
|---|---|
| `&a` | "Address of `a`" |
| `int *p;` | Declares `p` as a pointer to an `int` |
| `p = &a;` | Store the address of `a` inside `p` |
| `*p` | "Dereference" - the value stored **at** the address `p` is holding |

> 🧠 **The asterisk `*` secretly has two completely different jobs, and mixing them up is the #1 source of pointer confusion:**
> 1. In a **declaration**: `int *p;` reads as "p is a pointer" (a type descriptor)
> 2. In an **expression**: `*p` reads as "go to that address and fetch the value" (an action - dereferencing)

### Pass by Value vs. Pass by Reference - a Guaranteed Exam Question

```c
// PASS BY VALUE - the original is untouched
void change(int x) { x = 100; }
change(a);      // a is STILL 10 - only a temporary COPY named x was changed

// PASS BY REFERENCE (achieved using a pointer) - the original IS changed
void change(int *x) { *x = 100; }
change(&a);     // a becomes 100! We handed over a's ADDRESS, not a copy of its value
```

| Feature | Pass by Value | Pass by Reference (via pointer) |
|---|---|---|
| What's actually copied into the function | A copy of the value | The address of the variable |
| Does the original variable change? | No | Yes |
| Requires pointers? | No | Yes |

> **The exact wording examiners want:** C is technically **always** "pass by value" - even when you use pointers, what gets copied into the function is a copy of *the address*. But because that copied address still points at the same physical memory location as the original variable, modifying `*x` inside the function really does modify the original. This subtlety is a favorite 2-mark theory question.

### Reading Input Through a Pointer Parameter

```c
void readNumber(int *num) {
    scanf("%d", num);   // no & here! `num` is already an address, since it's declared as int*
}
// Calling it: readNumber(&n);
```

### Math Functions - `#include <math.h>`

| Function | Purpose |
|---|---|
| `sqrt(x)` | Square root |
| `pow(x, y)` | x raised to the power y |
| `ceil(x)` | Round up |
| `floor(x)` | Round down |
| `round(x)` | Round to nearest |
| `fabs(x)` | Absolute value |

Most of these return a `double`. To land on a plain integer, cast the result: `(int)round(4.6)` → `5`.

---

## 🔍 Behind the Scenes

- Under the hood, every pointer is just a regular number - the size of one memory address (typically 4 or 8 bytes, depending on your system's architecture).
- `int *p` and `char *q` don't take up different amounts of memory *as pointers themselves*.
- The difference is that the compiler now knows how many bytes to read starting from that address, and how far to move if you do arithmetic like `p + 1`.
- Arrays and pointers are close cousins for exactly this reason: an array name, in most expressions, "decays" into a pointer to its first element.

---

## 🖥 Visual Explanation

```
   p                     age
┌──────────┐         ┌──────────┐
│  0x1000  │───────▶│   20     │
└──────────┘         └──────────┘
 address: 0x2000      address: 0x1000

p stores the ADDRESS 0x1000.
*p means "go to 0x1000 and read what's there" → 20
```

```
PASS BY VALUE                       PASS BY REFERENCE

main()                              main()
 a = 10  [0x1000]                    a = 10  [0x1000]
   |                                   |
   │ copy value 10                     │ copy the ADDRESS 0x1000
   ▼                                   ▼
change(int x)                       change(int *x)
 x = 10  [0x3000]  ← separate box     x = 0x1000  [0x3000]
 x = 100 → only THIS box changes      *x = 100 → writes to 0x1000!
                                       → a in main() becomes 100
```

---

## 💻 Code Example

```c
#include <stdio.h>

void swap(int *x, int *y) {
    int temp = *x;
    *x = *y;
    *y = temp;
}

int main() {
    int a = 5, b = 10;

    printf("Before: a=%d, b=%d\n", a, b);
    swap(&a, &b);
    printf("After:  a=%d, b=%d\n", a, b);

    return 0;
}
```

**Expected Output:**
```
Before: a=5, b=10
After:  a=10, b=5
```

**Step-by-step execution:**
1. `a=5`, `b=10` in `main()`.
2. `swap(&a, &b)` sends the *addresses* of `a` and `b`, not copies of their values.
3. Inside `swap`, `x` points at `a`'s address, `y` points at `b`'s address.
4. `temp = *x` saves `a`'s value (5).
5. `*x = *y` writes `b`'s value (10) into `a`'s address.
6. `*y = temp` writes the saved 5 into `b`'s address.
7. Because `x` and `y` point at the *original* variables, the swap is visible back in `main()`.

**Why it works:** this is the entire point of pass-by-reference - without pointers, `swap` would only shuffle its own private copies, and `a`/`b` in `main()` would be completely untouched.

---

## 🎮 Predict the Output

```c
int a = 4, b = 6;
int *p = &a, *q = &b;
*p = *p + *q;
printf("%d %d", a, b);
```

<details>
<summary>💡 Reveal the answer</summary>

`10 6`

`*p = *p + *q` writes `4 + 6 = 10` directly into the address `p` points at, which is `a`. `b` is never touched.
</details>

---

## 🧪 Try It Yourself

Rewrite the `readNumber(int *num)` example from Core Concepts as a full program: write a function that reads a number through a pointer parameter, then a second function `void doubleIt(int *num)` that doubles whatever value that pointer points to. Call both from `main()`.

---

## 🎯 Mini Challenge

Write a function `void getMinMax(int arr[], int size, int *min, int *max)` that finds both the minimum and maximum of an array **in a single pass**, writing both results back through pointer parameters (since a function can only `return` one value directly).

<details>
<summary>💡 Need a hint?</summary>

This is exactly *why* pointer parameters matter - you need to send back two results, and a plain `return` can only carry one value out of a function.
</details>

---

## 🏆 Challenge Yourself

Implement your own version of `strlen()` - call it `int myStrlen(char *str)` - using only pointer arithmetic (no array indexing with `[]`, no calling the real `strlen()`). Hint: keep a pointer moving forward through the string, counting steps until you hit `'\0'`.

---

## 🌍 Real World Applications

| Concept | Where it shows up |
|---|---|
| Pointers | The Linux kernel is full of pointer manipulation for managing processes and memory directly |
| Pass-by-reference | Any function that needs to return more than one value (like `getMinMax` above) relies on this pattern in real production code |
| Pointer arithmetic | High-performance code (game engines, image processing) uses raw pointer arithmetic to avoid the overhead of repeated array-index calculations |

---

## 🧠 Memory Tricks

- **`&` = "Address of."** **`*` in a declaration = "this is a pointer."** **`*` in an expression = "go there."**
- **Think of a pointer as a sticky note with an address on it, not the item itself.**
- **Pass by reference = "give them the key to your house." Pass by value = "give them a photocopy of your house key" - useless for actually getting in.**

---

## 🎉 Fun Fact

The very first widely used C compiler didn't even *require* you to declare a function's return type or parameter types explicitly - that stricter checking (function prototypes) was only formalized with ANSI C in 1989. Before that, pointer-related bugs were even more common than they are today, because the compiler simply had less information to catch mistakes with.

---

## ⚠ Common Mistakes

```c
// ❌ Wrong - forgetting & when calling a pass-by-reference function
int a = 5;
void change(int *x) { *x = 100; }
change(a);     // compiler error or garbage - 'a' is a value, not an address!

// ✅ Correct
change(&a);
```
*Why:* the function parameter is declared as a pointer (`int *x`), so it expects an *address* as its argument - not the value itself.

---

## 🚫 Beginner Traps

- **"A pointer and the variable it points to are the same thing."** False - a pointer is a *separate* variable that happens to store an address; changing the pointer itself (`p = &b;`) doesn't touch the original variable at all, only `*p = ...` does.
- **"You always need `&` when calling any function."** False - only when the function expects a pointer parameter. If it expects a plain `int`, passing `&a` would be a type mismatch.

---

## 📌 Exam Tips

- Be ready to state, in your own words, why C is "always pass by value" even with pointers - this exact theory question appears constantly.
- Trace pointer questions by drawing two boxes: one for the pointer, one for the variable it points to, with an arrow between them - don't try to do it purely in your head.
- Watch for the double meaning of `*`: declaration vs. dereference. Examiners deliberately mix both uses in the same snippet.

---

## 🎤 Interview Questions

**Q: What is the difference between `p` and `*p` when `p` is declared as `int *p`?**
> `p` itself holds a memory address. `*p` dereferences that address, giving you the actual integer value stored there.

**Q: Why is C considered "pass by value" even when using pointers?**
> Because what gets copied into a function is always a value - when you pass a pointer, the value being copied happens to be a memory address. The function receives its own copy of that address, but since both the original and the copy point at the same memory location, modifying the data at that address affects the original variable.

**Q: What is a "dangling pointer"?**
> A pointer that still holds the address of memory that has already been freed or has gone out of scope (like a local variable after its function has returned) - dereferencing it leads to undefined behavior.

---

## ❓ Quiz

**Multiple Choice**

1. What does `&a` mean? <br>
   A) Multiply `a`  
   B) Address of `a`  
   C) Value of `a`  
   D) A logical AND
2. In `int *p;`, what is `p`? <br>
   A) An `int` value  
   B) A pointer to an `int`  
   C) An array  
   D) A function
3. What does `*p` do in an expression? <br>
   A) Declares a pointer  
   B) Multiplies `p`  
   C) Dereferences `p` (fetches the value it points to)  
   D) Nothing
4. Why is `swap(a, b)` (without `&`) unable to actually swap two variables? <br>
   A) It's a syntax error  
   B) It only swaps copies, leaving the originals untouched  
   C) `swap` doesn't exist  
   D) It swaps them correctly
5. What is a pointer, fundamentally?
   A) A value  
   B) A variable that stores a memory address  
   C) A function  
   D) A type of loop

<details><summary>✅ Reveal Answers</summary>

1. B  2. B  3. C  4. B  5. B
</details>

**True / False**

1. C is technically always "pass by value," even when pointers are used.
2. `int *p;` and `*p = 5;` use `*` for the exact same purpose.
3. A pointer variable itself takes up memory, just like any other variable.

<details><summary>✅ Reveal Answers</summary>

1. True  2. False (declaration vs. dereference - two different jobs)  3. True
</details>

**Short Answer**

1. Explain, in one or two sentences, why modifying `*x` inside a function can change a variable back in `main()`, but modifying a plain `int` parameter cannot.

<details><summary>✅ Reveal Guidance</summary>

When a plain `int` is passed, the function receives an independent copy of the value, so changes stay local. When a pointer is passed, the function receives a copy of the *address* of the original variable - and dereferencing that address with `*x = ...` writes directly into the original variable's memory location, so the change is visible outside the function too.
</details>

---

## 📝 Summary

Pointers store addresses, not values. `&` gets an address, `*` either declares a pointer or dereferences one depending on context, and pass-by-reference is how C lets a function reach outside its own private stack frame to modify the caller's variables. If this still feels slightly uncomfortable, re-read the "Imagine This" analogy - pointers click through repeated exposure, not a single perfect explanation.

---

## 🚀 What's Next?

With pointers under your belt, you're ready for **Decision Making** - `if`, `switch`, and the operator precedence traps examiners love hiding inside conditions.

---

## 📚 References

- Kernighan, B. W., & Ritchie, D. M. - *The C Programming Language* (2nd Edition), Prentice Hall.
- [cppreference.com - Pointer declaration](https://en.cppreference.com/w/c/language/pointer)

---

[⬅ Previous: 04. Operators & Type Conversion](./04-Operators-and-Type-Conversion.md) &nbsp;|&nbsp; [🏠 C Course Home](./README.md) &nbsp;|&nbsp; Next ➡ [06. Decision Making](./06-Decision-Making.md)
