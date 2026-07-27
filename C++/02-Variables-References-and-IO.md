[⬅ Previous](./01-Introduction-to-Cpp.md) &nbsp;|&nbsp; [🏠 C++ Course Home](./README.md) &nbsp;|&nbsp; Next ➡ [03. Functions & Overloading](./03-Functions-and-Overloading.md)

---

# 📘 2. Variables, References & I/O

| | |
|---|---|
| **Difficulty** | 🟢 Beginner |
| **Estimated Reading Time** | ~15 minutes |
| **Prerequisites** | [Lesson 1: Introduction to C++](./01-Introduction-to-Cpp.md) |

**Progress**
```
████░░░░░░░░░░░░░░░░░░░░░░  Lesson 2 of 15
```

---

## 🌟 Why Learn This?

Variables are how a program remembers anything at all. C++ gives you everything C had - plus a new tool, the **reference**, that makes sharing data between functions dramatically safer.

- References remove an entire category of bugs pointers are famous for: forgetting to check for `NULL`, or accidentally reassigning what a pointer points to.
- Every modern C++ codebase - browsers, game engines, trading systems - leans on references constantly, especially for function parameters.

Get comfortable with references now, because from Lesson 5 onward, almost every class method you write will use them.

---

## 🎯 By the End of This Lesson

You should know

✔ The rules for declaring and initializing variables in C++

✔ How `cin`/`cout` handle multiple values and types automatically

✔ What a reference is, and how it differs from a pointer

✔ When to use a reference vs. a pointer vs. pass-by-value

✔ Why references must be initialized immediately and can never be reseated

✔ Common `cin` pitfalls that silently break input parsing

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

- The skeleton of a C++ program (`#include`, `main()`, `cout`) from Lesson 1.
- Basic C-style variable declarations, if you've done any C or C++ before - not required, but helpful.

---

## 💡 Imagine This...

Imagine two people sharing an apartment. A **pointer** is like giving your roommate a piece of paper with the apartment's address written on it - they can visit, but they could also lose the paper, write down the wrong address, or the paper could say "no address" (`nullptr`) and lead nowhere.

A **reference** is like your roommate simply *being* a second name on the same lease. There's no separate piece of paper to lose - "roommate" and "you" are just two names for the same apartment, permanently, from the moment the lease is signed.

That permanence is the whole point of references: once bound, a reference can never be pointed somewhere else.

---

## 📖 Core Concepts

### Declaring Variables

C++ variable declaration looks identical to C:

```cpp
int age = 20;
double price = 9.99;
char grade = 'A';
bool isPassing = true;   // bool is native to C++ - no <stdbool.h> needed
```

| Type | Size (typical) | Example |
|---|---|---|
| `int` | 4 bytes | `int count = 10;` |
| `double` | 8 bytes | `double gpa = 3.75;` |
| `char` | 1 byte | `char letter = 'X';` |
| `bool` | 1 byte | `bool done = false;` |

### `cin` and `cout`

```cpp
int age;
std::cout << "Enter your age: ";
std::cin >> age;
std::cout << "You are " << age << " years old." << std::endl;
```

| Piece | Meaning |
|---|---|
| `std::cin >> age;` | Reads one value from input directly into `age` - no `&` needed, unlike `scanf` |
| `std::cout << ... << ...;` | Chains as many values as you want into one statement |

### References

A **reference** is an alias - another name for an existing variable.

```cpp
int score = 90;
int &ref = score;   // ref is now another name for score

ref = 95;            // this changes score too
std::cout << score;  // prints 95
```

| Rule | Why it matters |
|---|---|
| Must be initialized at declaration | `int &ref;` alone won't compile - a reference can't exist without something to refer to |
| Cannot be reseated | Once `ref` refers to `score`, it can never refer to a different variable |
| No null references | Unlike pointers, a reference always refers to something valid |

### References vs. Pointers vs. Pass-by-Value

| Style | Syntax | Can be null? | Can be reseated? | Typical use |
|---|---|---|---|---|
| Pass-by-value | `void f(int x)` | N/A | N/A | Small data you don't want the function to modify |
| Pass-by-pointer | `void f(int *x)` | Yes | Yes | Optional data, or when you need to reseat |
| Pass-by-reference | `void f(int &x)` | No | No | Default choice for "let the function modify this" |

---

## 🔍 Behind the Scenes

A reference isn't magic:

- At the machine level, it's usually implemented exactly like a pointer - an address stored in memory.
- The difference is entirely in what the **compiler allows you to do** with it.
- The compiler enforces "always initialized, never reseated, never null" as *language rules*, catching violations at compile time instead of leaving them as run-time bugs the way raw pointers do.

This is a recurring theme in C++: many features exist purely to move mistakes from run-time (where they crash users' programs) to compile-time (where they crash your build, which is much cheaper to fix).

---

## 🖥 Visual Explanation

```
int score = 90;
int &ref = score;

Memory:
+-------+
| score | = 90   ◄── both "score" and "ref" are names for THIS box
+-------+
   ▲
   └── ref (just another label, not a separate box)
```

Compare to a pointer, which gets its **own** box that stores an address:

```
int score = 90;
int *ptr = &score;

+-------+       +---------+
| score | = 90  |   ptr   | = address of score
+-------+       +---------+
   ▲                 │
   └─────────────────┘
```

---

## 💻 Code Example

```cpp
#include <iostream>

void doubleValue(int &num) {
    num = num * 2;
}

int main() {
    int x = 10;
    std::cout << "Before: " << x << std::endl;
    doubleValue(x);
    std::cout << "After: " << x << std::endl;
    return 0;
}
```

**Expected Output:**
```
Before: 10
After: 20
```

**Step-by-step execution:**
1. `x` is created with the value `10`.
2. `doubleValue(x)` is called - because the parameter is `int &num`, `num` becomes another name for `x`, not a copy.
3. `num = num * 2;` modifies `x` directly, through the reference.
4. Back in `main`, `x` has actually changed to `20` - no pointer syntax (`*`, `&`) was needed at the call site.

**Why it works:** if `doubleValue` had taken `int num` (by value), it would only modify a local copy, and `x` in `main` would stay `10`. The reference is what lets the function reach back and change the caller's variable directly.

---

## 🎮 Predict the Output

```cpp
#include <iostream>
using namespace std;

void reset(int &val) {
    val = 0;
}

int main() {
    int a = 5;
    int &b = a;
    reset(b);
    cout << a << " " << b;
    return 0;
}
```

<details>
<summary>💡 Reveal the answer</summary>

```
0 0
```

`b` is just another name for `a`, so changing `b` (directly or through `reset`) changes `a` too - there's only one variable here, wearing two name tags.
</details>

---

## 🧪 Try It Yourself

Modify the code example above to:
1. Write a function `increment(int &num)` that adds 1 instead of doubling.
2. Call both `doubleValue` and `increment` on the same variable, back to back, and predict the final value before running it.
3. Try declaring `int &ref;` with no initializer and read the exact compiler error.

---

## 🎯 Mini Challenge

Write a function `swapValues(int &a, int &b)` that swaps two integers using references (no pointers, no `std::swap`), then call it in `main` and print both values before and after.

<details>
<summary>💡 Need a hint?</summary>

You'll need one temporary variable inside the function to hold `a`'s original value while you overwrite it.
</details>

---

## 🌍 Real World Applications

| Concept | Where it shows up |
|---|---|
| Reference parameters | Nearly every function in real C++ codebases that needs to modify caller data |
| `const &` (you'll meet this soon) | Passing large objects efficiently without copying - used everywhere in game engines |
| `cin`/`cout` | Command-line tools, competitive programming, quick debugging output |

---

## 🧠 Memory Tricks

- **Pointer = a note with an address on it. Reference = a second nametag on the same box.**
- **References: bind once, never reseat. Pointers: reseat freely, but check for null.**

---

## 🎉 Fun Fact

References were one of the very first features Bjarne Stroustrup added to "C with Classes" - he wanted operator overloading (Lesson 7) to work naturally, and that required a way to pass arguments without exposing raw pointer syntax at every call site.

---

## ⚠ Common Mistakes

```cpp
// ❌ Wrong - reference declared without initialization
int &ref;

// ✅ Correct - reference must be bound immediately
int value = 10;
int &ref = value;
```
*Why:* a reference has no existence independent of the variable it refers to - the compiler has nowhere to "point" it until you tell it.


---

## 🚫 Beginner Traps

- **"A reference is basically the same as a pointer, just with nicer syntax."** Not quite - a reference can't be null and can't be reseated, which pointers can. Those restrictions are the entire value of using one.
- **"`cin >> age` needs `&age` like `scanf` does."** False - `cin` uses the `>>` operator, not a function call, so it can bind directly to the variable without you taking its address manually.

---

## 📌 Exam Tips

- A classic short-answer question: "List two differences between references and pointers." Know: null-ability and reseating.
- Be ready to trace code where a reference parameter modifies a caller's variable - this is a very common practical question.
- Remember: `int &ref = value;` - the `&` here means "reference," not "address-of." Same symbol, different meaning depending on context - a favorite exam trick.

---

## 🎤 Interview Questions

**Q: What's the difference between a reference and a pointer in C++?**
- A reference must be initialized when declared and can never be reseated to refer to something else; a pointer can be reassigned freely and can be `nullptr`.
- References use plain variable syntax at the call site; pointers require `*` to dereference and `&` to take an address.

**Q: Why prefer references over pointers for function parameters?**
- References guarantee the argument is valid (never null), removing a whole class of null-pointer bugs.
- The calling code stays clean - no `&` at the call site, no `*` inside the function.

---

## ❓ Quiz

**Multiple Choice**

1. Which of these must happen when declaring a reference? <br>
   A) It must be set to `nullptr`  
   B) It must be initialized immediately  
   C) It must be a pointer first  
   D) Nothing special is required

2. What does `int &ref = x;` create? <br>
   A) A copy of `x`  
   B) A pointer to `x`  
   C) Another name for `x`  
   D) A new variable unrelated to `x`

3. Can a reference be reseated to refer to a different variable after creation? <br>
   A) Yes, always  
   B) No, never  
   C) Only with a cast  
   D) Only for `int` types

4. What does `cin >> age;` do differently from C's `scanf`? <br>
   A) It requires `&age`  
   B) It doesn't need `&` because `>>` binds directly  
   C) It can't read integers  
   D) It only works with strings

5. Can a reference be null? <br>
   A) Yes  
   B) No  
   C) Only if declared with `nullptr`  
   D) Only in older C++ versions

<details><summary>✅ Reveal Answers</summary>

1. B  2. C  3. B  4. B  5. B
</details>

**True / False**

1. A reference and the variable it refers to always share the same memory location.
2. `int &ref;` (with no initializer) is valid C++.
3. Pass-by-reference lets a function modify the caller's original variable.

<details><summary>✅ Reveal Answers</summary>

1. True  2. False  3. True
</details>

**Short Answer**

1. Why can't a reference be null, while a pointer can?

<details><summary>✅ Reveal Guidance</summary>

- A reference is required to be bound to a valid variable the moment it's declared.
- The language provides no syntax to "unbind" or reset it afterward.
- So there's never a state where it refers to nothing - unlike a pointer, which can be explicitly set to `nullptr`.
</details>

---

## 📝 Summary

You now know how to declare variables in C++, use `cin`/`cout` for I/O, and - most importantly - what a reference is and how it differs from both pass-by-value and pointers. References are the backbone of clean C++ function design, and you'll see them constantly starting with the very next lesson on functions.

---

## 🚀 What's Next?

In the next lesson, you'll learn **functions and overloading** - including how C++ lets multiple functions share the same name, something C never allowed.

---

## 📚 References

- Stroustrup, B. - *The C++ Programming Language* (4th Edition), Addison-Wesley.
- ISO/IEC 14882 - the official C++ Language Standard.

---

[⬅ Previous](./01-Introduction-to-Cpp.md) &nbsp;|&nbsp; [🏠 C++ Course Home](./README.md) &nbsp;|&nbsp; Next ➡ [03. Functions & Overloading](./03-Functions-and-Overloading.md)
