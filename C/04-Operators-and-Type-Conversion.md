[⬅ Previous: 03. Functions & Modular Programming](./03-Functions-and-Modular-Programming.md) &nbsp;|&nbsp; [🏠 C Course Home](./README.md) &nbsp;|&nbsp; Next ➡ [05. Pointers & Pass-by-Reference](./05-Pointers-and-Pass-by-Reference.md)

---

# 📘 4. Arithmetic Operators & Type Conversion

| | |
|---|---|
| **Difficulty** | 🟢 Beginner |
| **Estimated Reading Time** | ~12 minutes |
| **Prerequisites** | [Functions & Modular Programming](./03-Functions-and-Modular-Programming.md) |

**Progress**
```
████████░░░░░░░░░░░░░░░░░░  Lesson 4 of 13
```

---

## 🌟 Why Learn This?

Here's an uncomfortable truth: `5 / 2` does **not** equal `2.5` in C. It equals `2`. This single fact trips up more beginners - and even working professionals switching from Python - than almost anything else in the language. Understanding *why* this happens will save you hours of confused debugging later, and it's one of the most reliably tested exam topics in this entire course.

---

## 🎯 By the End of This Lesson

You should know

✔ The five basic arithmetic operators, including the modulus operator

✔ Operator precedence, and how to control it with parentheses

✔ Exactly when integer division silently throws away decimal information

✔ The difference between implicit and explicit (cast) type conversion

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

You should already be comfortable declaring `int` and `float` variables from [Lesson 2](./02-Data-Types-Variables-IO.md).

---

## 💡 Imagine This...

Imagine you're paying for groceries in a currency that only exists in whole coins - no cents allowed.

- If your bill comes out to $10.75, the till simply drops the .75 and charges you $10 - no rounding, no warning.
- That's *exactly* what `int / int` does in C.
- It isn't rounding - it's **truncating**, chopping off everything after the decimal point, without asking.

---

## 📖 Core Concepts

### The Basics

| Operator | Meaning | Example |
|---|---|---|
| `+` | Addition | `a + b` |
| `-` | Subtraction | `a - b` |
| `*` | Multiplication | `a * b` |
| `/` | Division | `a / b` |
| `%` | Modulus (remainder) | `a % b` |

Two results worth burning into memory:

```
10 / 3  →  3   (integer division - the decimal part is simply thrown away)
10 % 3  →  1   (the remainder left over)
```

### Precedence

`*` and `/` happen before `+` and `-`, exactly like in ordinary math. Use `()` whenever you want to force a different order.

```
2 + 3 * 4  = 14
(2 + 3) * 4 = 20
```

### The Classic Exam Trap - Type Conversion

```c
float x = 5 / 2;         // 5 and 2 are BOTH int → integer division happens FIRST → 2
                          // THEN that 2 gets converted to float → x = 2.0, NOT 2.5!

float x = (float)5 / 2;  // now 5 becomes 5.0 BEFORE dividing → 5.0 / 2 = 2.5 ✅
```

> **Golden Rule:** In `a / b`, if **both** `a` and `b` are `int`, you get integer division - *no matter what type you're storing the answer in.* The type of the variable you assign to happens *after* the division is already done. Cast at least one operand to `float`/`double` **before** the division to get a decimal result.

### Implicit vs. Explicit Conversion

| Implicit (automatic) | Explicit (you choose - "casting") |
|---|---|
| `5 + 2.5 → 7.5` (the `int` 5 is silently promoted to `float`) | `(float)5 / 2 → 2.5` (you force the conversion yourself) |

---

## 🔍 Behind the Scenes

- `int` and `float` are stored in completely different bit layouts internally.
- An `int` is a straightforward binary number.
- A `float` uses a scheme (IEEE 754) that splits its bits into a sign, an exponent, and a fraction.
- When C "converts" between them, it isn't just relabeling the same bits - the CPU runs an actual conversion instruction to reinterpret the value in the new format.
- This is also why float math can occasionally produce tiny rounding surprises (like `0.1 + 0.2` not being *exactly* `0.3`) - it's a completely different internal representation, not a flaw in your code.

---

## 🖥 Visual Explanation

```
5 / 2   (both int)
   │
   ▼
Integer division happens FIRST → result = 2 (decimal thrown away)
   │
   ▼
THEN converted to float for storage → 2.0

vs.

(float)5 / 2
   │
   ▼
5 becomes 5.0 BEFORE dividing
   │
   ▼
5.0 / 2 → 2.5  ✅
```

---

## 💻 Code Example

```c
#include <stdio.h>

int main() {
    int a = 7, b = 2;

    printf("Wrong: %d / %d = %.2f\n", a, b, (float)(a / b));
    printf("Right: %d / %d = %.2f\n", a, b, (float)a / b);

    return 0;
}
```

**Expected Output:**
```
Wrong: 7 / 2 = 3.00
Right: 7 / 2 = 3.50
```

**Step-by-step execution:**
1. In the first line, `a / b` computes integer division first (`7 / 2 = 3`), and only *then* gets cast to `float` - so the decimal is already lost.
2. In the second line, `a` is cast to `float` *before* the division happens, so `7.0 / 2 = 3.5` is computed correctly.

**Why it works:** casting changes *when* the type conversion happens - and that timing is everything.

---

## 🎮 Predict the Output

```c
int a = 9, b = 4;
printf("%d %d", a / b, a % b);
```

<details>
<summary>💡 Reveal the answer</summary>

`2 1`

`9 / 4` truncates to `2`. `9 % 4` gives the remainder, `1`.
</details>

---

## 🧪 Try It Yourself

Write a program that reads a number of total minutes from the user and converts it into hours and remaining minutes, using `/` for hours and `%` for the leftover minutes (e.g. 130 minutes → "2 hours and 10 minutes").

---

## 🎯 Mini Challenge

Write a function `float calculateAverage(int a, int b, int c)` that correctly returns the average of three integers as a decimal - make sure it doesn't fall into the integer-division trap.

<details>
<summary>💡 Need a hint?</summary>

The sum of three `int`s is still an `int`. You need to cast *before* dividing by the count of 3.
</details>

---

## 🌍 Real World Applications

| Concept | Where it shows up |
|---|---|
| Modulus (`%`) | Used everywhere to detect even/odd numbers, wrap indices around in circular buffers, and convert seconds into hours:minutes:seconds |
| Integer division bugs | Real financial software bugs have been caused by exactly this trap - silently truncated currency calculations |

---

## 🧠 Memory Tricks

- **`int / int` always truncates - cast BEFORE you divide, not after.**
- **`%` = "what's left over," not "what percentage."** Think of splitting cookies among friends - `%` is how many are left when no more even splits are possible.

---

## 🎉 Fun Fact

The infamous 1996 Ariane 5 rocket explosion was caused by a data conversion error - a 64-bit float value didn't fit into a 16-bit integer, and the resulting overflow crashed the guidance software 37 seconds after launch. Type conversion isn't just an exam topic - it has genuinely destroyed multi-million dollar hardware.

---

## ⚠ Common Mistakes

```c
// ❌ Wrong
float avg = total / count;   // if both are int, decimals are lost BEFORE the float conversion

// ✅ Correct
float avg = (float)total / count;
```

---

## 🚫 Beginner Traps

- **"An `int` and a `float` divided together always give a decimal."** False - only if at least one operand is *already* a `float`/`double`, or you explicitly cast one.
- **"Casting the result variable's type fixes integer division."** False - `float x = 5/2;` still computes `5/2` as `int` division first; the cast must happen on an *operand*, not the destination variable.

---

## 📌 Exam Tips

- The division trap is one of the most frequently tested single concepts in the entire syllabus - expect it in "predict the output" questions repeatedly.
- Always trace: is this operation happening on two `int`s? If yes, expect truncation, regardless of where the result is stored.

---

## 🎤 Interview Questions

**Q: Why does `5 / 2` equal `2` in C instead of `2.5`?**
Because both operands are integers, C performs integer division, which truncates any decimal remainder. The type of the variable receiving the result has no effect - the division itself happens using integer arithmetic before any conversion occurs.

**Q: What's the difference between implicit and explicit type conversion?**
Implicit conversion happens automatically, chosen by the compiler (e.g. mixing an `int` and a `float` in the same expression promotes the `int`). Explicit conversion - casting - is when the programmer manually forces a specific conversion using syntax like `(float)x`.

---

## ❓ Quiz

**Multiple Choice**

1. What is `10 / 3` in C, with both as `int`? <br>
   A) 3.33  
   B) 3  
   C) 3.0  
   D) 4

2. What is `10 % 3`? <br>
   A) 3  
   B) 0  
   C) 1  
   D) 3.33

3. What does `(float)a / b` achieve that `a / b` alone doesn't? <br>
   A) Nothing different  
   B) Forces the division to happen in floating point  
   C) Rounds the result  
   D) Causes an error

4. Which operator computes the remainder? <br>
   A) `/`  
   B) `//`  
   C) `%`  
   D) `\`

5. What causes truncation in `int x = 7 / 2;`? <br>
   A) The variable type `x`  
   B) Both operands being `int`  
   C) The compiler being outdated  
   D) Nothing, it's not truncated

<details><summary>✅ Reveal Answers</summary>

1. B  2. C  3. B  4. C  5. B
</details>

**True / False**

1. `float x = 5 / 2;` results in `x` being `2.5`.
2. Casting must happen on an operand before division, not on the destination variable, to preserve decimals.
3. `%` works on `float` operands the same way it works on `int` operands.

<details><summary>✅ Reveal Answers</summary>

1. False  2. True  3. False (`%` requires integer operands in standard C)
</details>

**Short Answer**

1. Explain why `float avg = total / count;` can silently produce a wrong (rounded-down) answer.

<details><summary>✅ Reveal Guidance</summary>

If both `total` and `count` are `int`, the division `total / count` is computed as integer division first, discarding any decimal remainder - and only afterward is that already-truncated whole number converted into a `float` for storage in `avg`.
</details>

---

## 📝 Summary

You now understand operator precedence, the modulus operator, and - most importantly - exactly why and when integer division silently discards decimal information, plus how to fix it with a well-placed cast.

---

## 🚀 What's Next?

Next comes the topic most students fear and most interviewers ask about: **pointers**. You already have everything you need to understand them - you just need the right mental model.

---

## 📚 References

- Kernighan, B. W., & Ritchie, D. M. - *The C Programming Language* (2nd Edition), Prentice Hall.
- [cppreference.com - Usual arithmetic conversions](https://en.cppreference.com/w/c/language/conversion)

---

[⬅ Previous: 03. Functions & Modular Programming](./03-Functions-and-Modular-Programming.md) &nbsp;|&nbsp; [🏠 C Course Home](./README.md) &nbsp;|&nbsp; Next ➡ [05. Pointers & Pass-by-Reference](./05-Pointers-and-Pass-by-Reference.md)
