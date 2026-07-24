[⬅ Previous: 08. Character Processing](./08-Character-Processing.md) &nbsp;|&nbsp; [🏠 C Course Home](./README.md) &nbsp;|&nbsp; Next ➡ [10. Two-Dimensional Arrays](./10-Two-Dimensional-Arrays.md)

---

# 📘 9. Arrays

| | |
|---|---|
| **Difficulty** | 🟡 Intermediate |
| **Estimated Reading Time** | ~15 minutes |
| **Prerequisites** | [Character Processing](./08-Character-Processing.md), [Pointers](./05-Pointers-and-Pass-by-Reference.md) |

**Progress**
```
██████████████████░░░░░░░░  Lesson 9 of 13
```

---

## 🌟 Why Learn This?

- Without arrays, storing 100 students' marks would mean declaring 100 separate variables — `mark1`, `mark2`, all the way to `mark100` — completely unmanageable, and impossible to loop over.
- Arrays are the first "collection" data structure you'll learn.
- They're the foundation every later data structure (2D arrays, strings, structures, even linked lists) builds on top of.

---

## 🎯 By the End of This Lesson

You should know

✔ How to declare, initialize, and index arrays correctly

✔ Why array indices start at 0, and why going out of bounds is dangerous

✔ How arrays are passed to functions — and why they behave differently from plain variables

✔ How to loop through an array to process every element

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

Make sure you're comfortable with [loops](./07-Loops-and-Unary-Operators.md) and [pointers](./05-Pointers-and-Pass-by-Reference.md) — arrays lean heavily on both.

---

## 💡 Imagine This...

- Imagine a row of exactly 5 lockers in a school hallway, numbered starting from **0**, not 1 — locker 0, locker 1, locker 2, locker 3, locker 4.
- Each locker can hold one item of the same general kind (say, a textbook).
- If you try to open "locker 5" on a 5-locker row, you're not just going to find it empty — you might be reaching into the *neighboring classroom's* wall, since that space was never allocated to you.
- That's exactly what happens when you access `arr[5]` on a 5-element array: it doesn't politely fail, it silently reads or corrupts memory that isn't yours.

---

## 📖 Core Concepts

An **array** stores many values of the *same type*, side by side in memory, under one name.

```c
int marks[5];                          // uninitialized — contains GARBAGE values!
int numbers[5] = {10, 20, 30, 40, 50}; // explicit size
int numbers[] = {10, 20, 30, 40, 50};  // implicit size — the compiler counts (5) for you
int numbers[5] = {10, 20};             // partial init — remaining elements become 0
```

> **Rule:** Always initialize your arrays. Uninitialized elements don't default to 0 — they hold whatever garbage bits happened to be sitting in that memory already.

**Indexing starts at 0.** For a 5-element array, valid indices are `0` through `4`.

> ⚠️ **Classic exam trap:** `marks[5]` on a size-5 array is **out of bounds** — undefined behavior. The compiler often won't stop you; it'll just silently read or corrupt memory it shouldn't.

```c
for (i = 0; i < 5; i++) scanf("%d", &marks[i]);
for (i = 0; i < 5; i++) printf("%d ", marks[i]);
```

**Compound assignment shorthand:** `sum += marks[i];` is identical to `sum = sum + marks[i];` (also works with `-=`, `*=`, `/=`, `%=`).

### Passing Arrays to Functions — a Key Conceptual Leap

```c
void change(int arr[]) { arr[0] = 100; }
change(numbers);   // numbers[0] IS modified back in main() too!
```

> **Why:** arrays are passed by **address automatically** — no `&` needed. The function operates directly on the original array, unlike a plain `int`/`float`/`char`, which is passed by value (a copy).

```c
int findSum(int arr[], int size) {
    int sum = 0;
    for (int i = 0; i < size; i++) sum += arr[i];
    return sum;
}
```

> **Common mistake:** forgetting to also pass the `size` — a C array doesn't "know" its own length once it's inside a function, so you must tell it explicitly.

---

## 🔍 Behind the Scenes

- An array is really just a single contiguous block of memory, with the compiler doing the addressing math for you.
- `arr[i]` is literally computed as `*(arr + i)` — take the array's starting address, move forward `i` elements (each `sizeof(type)` bytes wide), and dereference.
- This is exactly why array indexing and pointer arithmetic are two faces of the same underlying mechanism.
- It's also why passing an array to a function is really just passing the address of its first element.

---

## 🖥 Visual Explanation

```
int numbers[5] = {10, 20, 30, 40, 50};

Index:     0     1     2     3     4
        ┌─────┬─────┬─────┬─────┬─────┐
        │ 10  │ 20  │ 30  │ 40  │ 50  │
        └─────┴─────┴─────┴─────┴─────┘
Address: 1000  1004  1008  1012  1016   (each int = 4 bytes on most systems)

numbers[2] is the same as *(numbers + 2)
```

---

## 💻 Code Example

```c
#include <stdio.h>

int findSum(int arr[], int size) {
    int sum = 0;
    for (int i = 0; i < size; i++) {
        sum += arr[i];
    }
    return sum;
}

int main() {
    int numbers[5] = {10, 20, 30, 40, 50};
    int total = findSum(numbers, 5);
    printf("Total = %d\n", total);
    return 0;
}
```

**Expected Output:**
```
Total = 150
```

**Step-by-step execution:**
1. `numbers` is initialized with five values.
2. `findSum(numbers, 5)` passes the array (as an address) and its size.
3. Inside `findSum`, the loop adds each element to `sum`.
4. `150` is returned and printed.

**Why it works:** because we explicitly pass `size` alongside the array — without it, `findSum` would have no way of knowing where the array actually ends.

---

## 🎮 Predict the Output

```c
int arr[3] = {1, 2, 3};
arr[1] = arr[0] + arr[2];
printf("%d %d %d", arr[0], arr[1], arr[2]);
```

<details>
<summary>💡 Reveal the answer</summary>

`1 4 3`

`arr[1]` is overwritten with `arr[0] + arr[2] = 1 + 3 = 4`. `arr[0]` and `arr[2]` are untouched.
</details>

---

## 🧪 Try It Yourself

Write a function `int findMax(int arr[], int size)` that returns the largest value in an array, and call it on an array of your choosing.

---

## 🎯 Mini Challenge

Write a program that reads 10 integers into an array, then reverses the array **in place** (without using a second array) and prints the result.

<details>
<summary>💡 Need a hint?</summary>

Swap the first and last elements, then the second and second-to-last, and so on, moving two index pointers toward the middle.
</details>

---

## 🌍 Real World Applications

| Concept | Where it shows up |
|---|---|
| Arrays | Every spreadsheet application stores rows of data in array-like structures under the hood |
| Bounds checking | Buffer overflow vulnerabilities — a major category of real security exploits — are caused by exactly the out-of-bounds access this lesson warns about |
| Passing arrays by address | Image-processing libraries pass large pixel arrays to functions without copying, for performance reasons — the same mechanism you just learned |

---

## 🧠 Memory Tricks

- **Index 0 = "zero steps from the start,"** not "the first item's number."
- **Arrays are automatically passed by address — no `&` needed, unlike plain variables.**
- **Always pass the size alongside the array — it doesn't carry that information with it.**

---

## 🎉 Fun Fact

The famous **Heartbleed** security bug (2014), which exposed sensitive data on a huge portion of the internet's servers, was ultimately caused by a buffer over-read — reading past the end of an allocated memory block, precisely the kind of bug that comes from ignoring array bounds. A "beginner mistake" at massive, real-world scale.

---

## ⚠ Common Mistakes

```c
// ❌ Wrong — off-by-one, index 5 doesn't exist on a size-5 array
int arr[5];
for (int i = 0; i <= 5; i++) { arr[i] = i; }   // i == 5 is out of bounds!

// ✅ Correct
for (int i = 0; i < 5; i++) { arr[i] = i; }
```
*Why:* `<=` includes index `5`, which doesn't exist on a 5-element array (valid indices are `0`–`4`). Always double-check `<` vs `<=` in loop conditions involving array bounds.

---

## 🚫 Beginner Traps

- **"Arrays automatically know their own size once passed to a function."** False — you must pass the size as a separate parameter.
- **"`int arr[5];` initializes all elements to 0."** False — uninitialized local arrays hold garbage values, not zeros, unless you explicitly initialize them.

---

## 📌 Exam Tips

- For `int a[5]`, valid indices are `0` to `4` — a favorite "spot the bug" question deliberately uses `a[5]`.
- When a function needs to modify the caller's array, remember it happens automatically — no `&` required, unlike ordinary variables.
- Always check whether a loop uses `<` or `<=` against the array size.

---

## 🎤 Interview Questions

**Q: Why are arrays passed to functions "by reference" automatically in C, without needing `&`?**
> Because an array name decays into a pointer to its first element in most expressions, including function calls — so what's actually passed is already an address, achieving the same effect as pass-by-reference without any extra syntax.

**Q: What is undefined behavior, and how does array indexing relate to it?**
> Undefined behavior means the C standard places no requirements on what happens — accessing an index outside an array's allocated bounds (like `arr[5]` on a 5-element array) reads or writes memory that may belong to something else entirely, and the compiler is not required to catch or prevent it.

---

## ❓ Quiz

**Multiple Choice**

1. For `int arr[5]`, what is the valid index range? <br>
   A) 1 to 5  
   B) 0 to 5  
   C) 0 to 4  
   D) 1 to 4
2. How are arrays passed to functions in C by default? <br>
   A) By value (copied)  
   B) By address, automatically  
   C) They can't be passed  
   D) Only with `&`
3. What must you pass alongside an array to a function that processes it? <br>
   A) Nothing else is needed  
   B) Its size  
   C) Its type name as a string  
   D) A pointer to `main()`
4. What happens when you access `arr[5]` on a 5-element array? <br>
   A) A compiler error  
   B) The program pads it with 0  
   C) Undefined behavior  
   D) It automatically resizes
5. What do uninitialized local array elements contain? <br>
   A) Always 0  
   B) Garbage values  
   C) `NULL`  
   D) The array's size

<details><summary>✅ Reveal Answers</summary>

1. C  2. B  3. B  4. C  5. B
</details>

**True / False**

1. Modifying an array inside a function changes the original array in `main()`.
2. Arrays in C automatically know their own length once passed into a function.
3. `int numbers[] = {1,2,3};` lets the compiler determine the array's size automatically.

<details><summary>✅ Reveal Answers</summary>

1. True  2. False  3. True
</details>

**Short Answer**

1. Explain why passing an array to a function behaves differently from passing a plain `int`.

<details><summary>✅ Reveal Guidance</summary>

A plain `int` is passed by value — the function receives an independent copy, so changes don't affect the original. An array name decays into a pointer to its first element, so what's actually passed is the array's address; the function operates on the same memory as the original, so any modifications are visible back in the caller.
</details>

---

## 📝 Summary

You now understand how to declare, initialize, index, and loop through arrays, why index 0 is the starting point, why bounds matter, and how arrays are automatically passed by address to functions (unlike plain variables).

---

## 🚀 What's Next?

Next: **Two-Dimensional Arrays** — arrays of arrays, the natural structure for grids, tables, and matrices.

---

## 📚 References

- Kernighan, B. W., & Ritchie, D. M. — *The C Programming Language* (2nd Edition), Prentice Hall.

---

[⬅ Previous: 08. Character Processing](./08-Character-Processing.md) &nbsp;|&nbsp; [🏠 C Course Home](./README.md) &nbsp;|&nbsp; Next ➡ [10. Two-Dimensional Arrays](./10-Two-Dimensional-Arrays.md)
