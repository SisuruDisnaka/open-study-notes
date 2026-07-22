[⬅ Previous: 01. Programming Fundamentals](./01-Programming-Fundamentals.md) &nbsp;|&nbsp; [🏠 C Course Home](./README.md) &nbsp;|&nbsp; Next ➡ [03. Functions & Modular Programming](./03-Functions-and-Modular-Programming.md)

---

# 📘 2. Data Types, Variables & I/O

| | |
|---|---|
| **Difficulty** | 🟢 Beginner |
| **Estimated Reading Time** | ~15 minutes |
| **Prerequisites** | [Programming Fundamentals](./01-Programming-Fundamentals.md) |

**Progress**
```
████░░░░░░░░░░░░░░░░░░░░░░  Lesson 2 of 13
```

---

## 🌟 Why Learn This?

Every program that has ever existed does one basic thing:

- Takes some data **in**
- **Stores** it somewhere
- Produces some data **out**

Variables are the "somewhere." Get comfortable with them and I/O now - everything else in this course (loops, arrays, structures) is just more elaborate ways of organizing variables.

---

## 🎯 By the End of This Lesson

You should know

✔ How to declare variables of different types, and pick the right format specifier for each

✔ How to safely take input with `scanf()` - and why forgetting `&` is the #1 beginner bug

✔ What escape sequences are and how to use them in output

✔ The naming rules that make a variable name valid in C

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

You should be comfortable with the basic C program skeleton from [Lesson 1](./01-Programming-Fundamentals.md) - we'll be filling in the body of `main()` from here on.

---

## 💡 Imagine This...

Imagine moving into a new house and labeling boxes as you unpack: `"Kitchen"`, `"Books"`, `"Winter Clothes"`.

- Each label tells you *what kind of thing* lives in that box.
- It lets you find it again later without opening every single box in the house.

A **variable** is exactly that: a labeled box in memory.

- `int age` is a box labeled `age`.
- The `int` tells the computer: "only whole numbers go in this box, and set aside exactly enough space for one."
- Every variable declaration you write is really just labeling and sizing a box before you put anything inside it.

---

## 📖 Core Concepts

Every single line of C code you write is one of four kinds of **statement** - and every statement ends with a semicolon `;`.

| Statement type | Example |
|---|---|
| Declaration | `int age;` |
| Assignment | `age = 20;` |
| Input/Output | `printf("Hi");` |
| Control | `if (age > 18) {...}` |

### Comments

```c
// this is a single-line comment
/* this is a
   multi-line comment */
```

### Output with `printf()`

```c
printf("Hello\n");   // \n is an escape sequence meaning "new line"
```

| Escape Sequence | Meaning |
|---|---|
| `\n` | New line |
| `\t` | Horizontal tab |
| `\\` | A literal backslash |
| `\"` | A literal double quote |

### Variables

Variables are labeled boxes in memory that hold a value of a specific type:

```c
int age = 20;
float salary = 50000.50;
char grade = 'A';
```

Every variable needs a **format specifier** so `printf`/`scanf` know how to read or display its bits correctly:

| Format Specifier | Data Type |
|---|---|
| `%d` | `int` |
| `%f` | `float` |
| `%c` | `char` |
| `%s` | string (`char[]`) |
| `%lf` | `double` |

**Variable naming rules:** must start with a letter or underscore, no spaces, cannot be a reserved keyword (like `int` or `return`), and C is **case-sensitive** (`age` and `Age` are different variables).

### Input with `scanf()`

```c
int age;
scanf("%d", &age);   // the & means "the ADDRESS of age", not the value
```

> ⚠️ **The single most common C bug in exams:** forgetting `&` in `scanf()`. The rule: use `&` for numeric variables. **Do not** use `&` for character arrays / strings - a string variable name is already an address (you'll see exactly why once we reach Pointers).
>
> Also: use `" %c"` (note the leading space) when reading a character right after reading a number - it skips the leftover newline character sitting in the input buffer.

---

## 🔍 Behind the Scenes

What `scanf("%d", &age)` really does at the CPU level:

- The CPU doesn't know "age" as a name - that name exists only for *you*.
- Once compiled, `age` is simply a numeric memory address, like `0x7ffee2a1c04c`.
- `&age` computes that address.
- `scanf` receives that raw address and writes the incoming bytes directly there.
- This is precisely why forgetting `&` breaks things - without it, `scanf` receives the garbage *value* currently sitting at that address and tries to treat it as an address to write to, which usually crashes.

---

## 🖥 Visual Explanation

```
Variable: age
Address:  0x1000
┌──────────┐
│   20     │
└──────────┘
   ↑
 age (value 20, stored at address 0x1000)
```

---

## 💻 Code Example

```c
#include <stdio.h>

int main() {
    int a, b, sum;

    printf("Enter two numbers: ");
    scanf("%d %d", &a, &b);   // & is required - we're writing INTO these variables

    sum = a + b;
    printf("Sum = %d\n", sum);

    return 0;
}
```

**Expected Output** (if the user enters `4` and `7`):
```
Enter two numbers: 4 7
Sum = 11
```

**Step-by-step execution:**
1. `a`, `b`, `sum` are declared but hold garbage values initially.
2. `scanf` writes `4` into `a`'s address and `7` into `b`'s address.
3. `sum = a + b` computes `11` and stores it in `sum`.
4. `printf` reads `sum` and displays it.

**Why it works:** `&a` and `&b` give `scanf` the actual memory locations to write into.

---

## 🎮 Predict the Output

```c
int x = 10;
char c = 'Z';
printf("%d %c", x, c);
```

<details>
<summary>💡 Reveal the answer</summary>

`10 Z`

Each format specifier consumes one argument in order - `%d` matches `x`, `%c` matches `c`.
</details>

---

## 🧪 Try It Yourself

Write a program that declares an `int`, a `float`, and a `char`, reads all three from user input, and prints them back in a single formatted sentence like: `"You are 20 years old, weigh 65.50 kg, and your grade is A."`

---

## 🎯 Mini Challenge

Write a program that reads a temperature in Celsius as a `float` and converts it to Fahrenheit using `F = C * 9/5 + 32`. Print the result to 2 decimal places using `%.2f`.

<details>
<summary>💡 Need a hint?</summary>

Watch out for integer division - `9/5` alone would truncate to `1` in C. Make sure at least one of those numbers is written as a decimal (`9.0/5`) or cast appropriately.
</details>

---

## 🌍 Real World Applications

| Concept | Where it shows up |
|---|---|
| Format specifiers | Every logging system (server logs, crash reports) formats data using printf-style specifiers, even in other languages |
| Input validation | Every web form validates input types before submitting - the same core idea as matching `scanf` types correctly |

---

## 🧠 Memory Tricks

- **`&` = "Address of."** Picture a little hand reaching out to grab a location, not a value.
- **`%d` for whole numbers ("decimal integer"), `%f` for "floating" decimals, `%c` for a single character.**

---

## 🎉 Fun Fact

`printf` and `scanf` have barely changed since C was standardized in 1989 (ANSI C, or "C89") - the exact same format specifiers you're learning today were already standard over 35 years ago.

---

## ⚠ Common Mistakes

```c
// ❌ Wrong
int age;
scanf("%d", age);     // missing &, this usually crashes or corrupts memory

// ✅ Correct
scanf("%d", &age);
```
*Why:* `scanf` needs an *address* to write into, not the current (garbage) value.

---

## 🚫 Beginner Traps

- **"C automatically initializes variables to 0."** False for local variables - they hold unpredictable garbage until you explicitly assign a value.
- **"Any word can be a variable name."** False - reserved keywords like `int`, `return`, `float` cannot be used as variable names.

---

## 📌 Exam Tips

- The `&` rule of thumb: use `&` in `scanf` for *numeric* variables only, never for strings/char arrays.
- Know every format specifier in the table above cold - matching the wrong one to a variable type is a frequent "spot the bug" question.

---

## 🎤 Interview Questions

**Q: What's the difference between `=` and `==` in C?**
`=` is the assignment operator - it stores a value into a variable. `==` is the equality comparison operator. Mixing them up inside a condition is one of C's most common bugs (covered in depth in [Lesson 6](./06-Decision-Making.md)).

**Q: What is a variable's "scope"?**
Scope defines *where* in the code a variable is visible. A variable declared inside a function is **local**; a variable declared outside any function is **global**.

---

## ❓ Quiz

**Multiple Choice**

1. What format specifier matches a `float`? <br>
   A) `%d`  
   B) `%f`  
   C) `%c`  
   D) `%s`
   
2. What does `&age` represent? <br>
   A) The value of `age`  
   B) The address of `age`  
   C) A new variable  
   D) A syntax error

3. Which of these is a valid C variable name? <br>
   A) `2ndValue`  
   B) `int`  
   C) `_value2`  
   D) `my value`

4. What does `\n` do inside a string? <br>
   A) Prints a backslash  
   B) Moves to a new line  
   C) Prints "n"  
   D) Nothing

5. Which statement type is `age = 20;`? <br>
   A) Declaration  
   B) Assignment  
   C) Control  
   D) Input

<details><summary>✅ Reveal Answers</summary>

1. B  2. B  3. C  4. B  5. B
</details>

**True / False**

1. `scanf("%s", name)` needs an `&` before `name` when `name` is a char array.
2. C is case-sensitive, so `age` and `Age` are different variables.
3. Local variables are automatically set to 0 if you don't initialize them.

<details><summary>✅ Reveal Answers</summary>

1. False  2. True  3. False
</details>

**Short Answer**

1. Why does forgetting `&` in `scanf("%d", age)` often crash a program?

<details><summary>✅ Reveal Guidance</summary>

Because `scanf` expects an address to write the input into. Without `&`, it receives whatever garbage value currently sits in `age` and tries to treat that value as if it were a valid memory address to write to - which is usually invalid and crashes the program.
</details>

---

## 📝 Summary

You can now declare variables of different types, choose the correct format specifier, safely read input with `scanf` (including the critical `&` rule), and produce formatted output with `printf` and escape sequences. This is the vocabulary every later lesson assumes you already speak fluently.

---

## 🚀 What's Next?

Next, you'll learn to organize your code into reusable **functions** - breaking big problems into small, manageable pieces, exactly like the Top-Down Design idea from Lesson 1.

---

## 📚 References

- Kernighan, B. W., & Ritchie, D. M. - *The C Programming Language* (2nd Edition), Prentice Hall.
- [cppreference.com - printf/scanf format specifiers](https://en.cppreference.com/w/c/io/fprintf)

---

[⬅ Previous: 01. Programming Fundamentals](./01-Programming-Fundamentals.md) &nbsp;|&nbsp; [🏠 C Course Home](./README.md) &nbsp;|&nbsp; Next ➡ [03. Functions & Modular Programming](./03-Functions-and-Modular-Programming.md)
