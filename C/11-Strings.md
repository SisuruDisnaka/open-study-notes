[⬅ Previous: 10. Two-Dimensional Arrays](./10-Two-Dimensional-Arrays.md) &nbsp;|&nbsp; [🏠 C Course Home](./README.md) &nbsp;|&nbsp; Next ➡ [12. File Handling](./12-File-Handling.md)

---

# 📘 11. Strings in C

| | |
|---|---|
| **Difficulty** | 🟡 Intermediate |
| **Estimated Reading Time** | ~16 minutes |
| **Prerequisites** | [Arrays](./09-Arrays.md), [Character Processing](./08-Character-Processing.md) |

**Progress**
```
██████████████████████░░░░  Lesson 11 of 13
```

---

## 🌟 Why Learn This?

- Unlike Python or Java, C doesn't have a built-in "string" type at all.
- A string is just a `char` array with one crucial extra rule.
- Understanding that rule unlocks *why* certain string bugs happen (and how to avoid them), instead of just memorizing library function names.

---

## 🎯 By the End of This Lesson

You should know

✔ Why C strings need a special null terminator, and what breaks without one

✔ The difference between `scanf("%s", ...)` and `fgets()` for reading text

✔ How to safely compare, copy, and manipulate strings using `<string.h>`

✔ How to traverse a string manually, character by character

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

You should be comfortable with arrays ([Lesson 9](./09-Arrays.md)) and characters ([Lesson 8](./08-Character-Processing.md)) - a string is where both ideas merge into one.

---

## 💡 Imagine This...

- Imagine a sentence written on a strip of paper, one letter per box, with no fixed end to the strip - how do you know where the sentence actually stops?
- You'd need a special "stop here" marker at the end, or you'd risk reading garbage text off the unused part of the strip forever.
- That's exactly what the **null character `'\0'`** does in a C string - it's the "stop here" marker, and every string-processing function relies on finding it to know where the real text ends.

---

## 📖 Core Concepts

A string is nothing magical - it's just a character array that ends with a special sentinel: the **null character `'\0'`**.

```c
char word[] = "Hello";   // stored as: H e l l o \0  → 6 bytes used, not 5!
```

> **Trick:** `char name[20]` gives you only **19 usable characters** - the 20th slot is reserved for `'\0'`.

### Reading Strings

```c
scanf("%s", name);                    // reads ONE word only - stops at the first space
fgets(name, sizeof(name), stdin);     // reads a FULL line, safer, prevents buffer overflow
```

> **A favorite comparison question:** `scanf("%s", ...)` vs. `fgets(...)` - `fgets` can capture spaces, `scanf` cannot; `fgets` also guards against overflowing the buffer.

**`fgets` leaves the newline character in the string - remove it like this:**

```c
fgets(name, sizeof(name), stdin);
name[strcspn(name, "\n")] = '\0';   // memorize this exact line - it appears often in exams
```

### Traversing a String Manually

```c
for (i = 0; name[i] != '\0'; i++) {
    printf("%c\n", name[i]);
}
```

> **Key idea:** loop until you hit `'\0'` - this same pattern is how you compute length, count vowels, reverse a string, or check a palindrome by hand.

### An Array of Strings (a 2D char array)

```c
char names[5][20];   // up to 5 strings, each up to 19 chars + '\0'
fgets(names[i], sizeof(names[i]), stdin);
names[2][1]           // the 3rd string's 2nd character
```

### The String Library - `#include <string.h>`

| Function | Purpose |
|---|---|
| `strlen(s)` | Length of the string |
| `strcpy(dest, src)` | Copy `src` into `dest` |
| `strcat(dest, src)` | Append `src` onto the end of `dest` |
| `strcmp(s1, s2)` | `0` if equal, positive if `s1 > s2`, negative if `s1 < s2` |

> ⚠️ **Common mistake:** using `==` to compare strings - this compares their *addresses*, not their content. Always use `strcmp()`.

### Passing Strings to Functions

These two lines mean exactly the same thing:

```c
void display(char str[]);   // equivalent to:
void display(char *str);
```

---

## 🔍 Behind the Scenes

- Every string function in `<string.h>` internally does the exact same thing you'd do manually: walk forward through memory one byte at a time until it hits `'\0'`.
- `strlen()` is not magic - it's a loop counting bytes until the terminator.
- This is also *why* a "string" without a null terminator is dangerous: functions like `strlen()` or `printf("%s", ...)` will keep reading past the intended end, right into whatever memory happens to come next, until they get lucky and stumble onto a zero byte somewhere.

---

## 🖥 Visual Explanation

```
char word[] = "Hi!";

Index:     0     1     2     3
        ┌─────┬─────┬─────┬──────┐
        │ 'H' │ 'i' │ '!' │ '\0' │
        └─────┴─────┴─────┴──────┘

The loop `while(word[i] != '\0')` stops the instant it hits that final box.
```

---

## 💻 Code Example

```c
#include <stdio.h>
#include <string.h>

int main() {
    char name[30];

    printf("Enter your full name: ");
    fgets(name, sizeof(name), stdin);
    name[strcspn(name, "\n")] = '\0';   // strip the trailing newline

    printf("Hello, %s! Your name has %zu characters.\n", name, strlen(name));

    return 0;
}
```

**Expected Output** (input `"Nimal Perera"`):
```
Enter your full name: Nimal Perera
Hello, Nimal Perera! Your name has 12 characters.
```

**Step-by-step execution:**
1. `fgets` reads the full line, including the space, unlike `scanf("%s", ...)`.
2. `strcspn(name, "\n")` finds the position of the newline character `fgets` leaves behind, and we overwrite it with `'\0'` to clean up the string.
3. `strlen(name)` counts characters up to (but not including) the `'\0'`.

**Why it works:** `fgets` + the `strcspn` cleanup is the standard, exam-ready pattern for safely reading a full line of text in C.

---

## 🎮 Predict the Output

```c
char s1[] = "cat";
char s2[] = "cat";
if (s1 == s2)
    printf("Equal");
else
    printf("Not Equal");
```

<details>
<summary>💡 Reveal the answer</summary>

`Not Equal`

`s1` and `s2` are two *separate* arrays in memory, each holding a copy of the text `"cat"`. `s1 == s2` compares their *addresses*, which are different, even though their *content* is identical. `strcmp(s1, s2) == 0` would correctly report them as equal.
</details>

---

## 🧪 Try It Yourself

Write a program that reads a word and prints it in reverse, using a manual loop (not a library function).

---

## 🎯 Mini Challenge

Write a function `int isPalindrome(char str[])` that returns `1` if a string reads the same forwards and backwards (e.g. `"level"`), and `0` otherwise.

<details>
<summary>💡 Need a hint?</summary>

Use two index variables - one starting at `0`, one starting at `strlen(str) - 1` - and move them toward each other, comparing characters as you go.
</details>

---

## 🌍 Real World Applications

| Concept | Where it shows up |
|---|---|
| Null-terminated strings | This exact convention is still used throughout C, C++, and many system-level APIs today, decades after it was introduced |
| `strcmp()` | Password verification systems compare submitted text against stored values using content comparison, exactly like `strcmp`, never address comparison |
| Buffer-safe reading (`fgets`) | Real security vulnerabilities (buffer overflows) have historically come from careless string input handling - exactly what `fgets` over `scanf("%s")` protects against |

---

## 🧠 Memory Tricks

- **`\0` is the string's full stop.** No `\0`, no defined end.
- **`char name[20]` = 19 usable characters + 1 reserved for `'\0'`.**
- **Never `==` two strings - always `strcmp()`.** Addresses lie about content.

---

## 🎉 Fun Fact

The null-terminated string convention was a deliberate design trade-off by Dennis Ritchie - other approaches (like storing the length explicitly alongside the text) exist and are used by other languages, but null-termination was simpler to implement in early, memory-constrained systems. That single design decision from the 1970s is directly responsible for a huge share of real-world security vulnerabilities in C code even today.

---

## ⚠ Common Mistakes

```c
// ❌ Wrong
if (name == "Nimal") { ... }   // compares addresses, not content - almost always false

// ✅ Correct
if (strcmp(name, "Nimal") == 0) { ... }
```

```c
// ❌ Wrong - scanf stops at the first space, losing "Perera"
char name[30];
scanf("%s", name);   // input "Nimal Perera" → name only holds "Nimal"

// ✅ Correct
fgets(name, sizeof(name), stdin);
```

---

## 🚫 Beginner Traps

- **"`scanf("%s", ...)` reads an entire line."** False - it stops at the first space or newline.
- **"You need `&` before a string variable name in `scanf`."** False - a string variable name already decays into an address, unlike a plain `int`.
- **"`strlen()` counts the `'\0'` too."** False - `strlen()` returns the count of characters *before* the null terminator, not including it.

---

## 📌 Exam Tips

- Memorize the exact `fgets` + `strcspn` newline-stripping pattern - it appears constantly in exam-standard code.
- Any `==` used to compare two strings is almost always the intended bug in a "spot the error" question - the fix is always `strcmp()`.
- Know that `char name[20]` allows only 19 real characters.

---

## 🎤 Interview Questions

**Q: Why does C need a null terminator for strings, when arrays already know their own declared size?**
> Because a string's *content length* can be shorter than the array's *declared size* - e.g. `char name[20]` might only hold `"Sam"`, five characters including the terminator. Without `'\0'` marking where the actual text ends, functions like `strlen()` would have no way to know where to stop.

**Q: What's the difference between `scanf("%s", ...)` and `fgets()`?**
 > `scanf("%s", ...)` reads a single whitespace-delimited token and provides no protection against buffer overflow if the input exceeds the buffer's size. `fgets()` reads up to a specified maximum length (including spaces, stopping at a newline or that limit), making it the safer choice for reading full lines of user input.

---

## ❓ Quiz

**Multiple Choice**

1. What character marks the end of a C string? <br>
   A) `'\n'`  
   B) `'\0'`  
   C) `EOF`  
   D) A space
2. What does `char name[10]` actually allow for usable text? <br>
   A) 10 characters  
   B) 9 characters  
   C) 11 characters  
   D) Unlimited
3. Which function correctly compares string content? <br>
   A) `==`  
   B) `strcmp()`  
   C) `strlen()`  
   D) `strcpy()`
4. Why is `fgets()` often preferred over `scanf("%s", ...)`? <br>
   A) It's shorter to type  
   B) It safely reads full lines including spaces  
   C) It's faster  
   D) It doesn't need a buffer
5. What does `strlen("Hi")` return? <br>
   A) 3  
   B) 2  
   C) 1  
   D) 0

<details><summary>✅ Reveal Answers</summary>

1. B  2. B  3. B  4. B  5. B
</details>

**True / False**

1. `s1 == s2` correctly compares the content of two C strings.
2. `strlen()` includes the null terminator in its count.
3. A string in C is fundamentally just a character array ending in `'\0'`.

<details><summary>✅ Reveal Answers</summary>

1. False  2. False  3. True
</details>

**Short Answer**

1. Explain why `char name[20] = "Hello";` uses more memory than the visible 5 letters suggest.

<details><summary>✅ Reveal Guidance</summary>

Because the array is declared to hold 20 bytes total, and even though only 5 characters are visible text, C also stores the required `'\0'` terminator right after them (using a 6th byte), with the remaining declared bytes left unused/uninitialized until the program writes something else into them.
</details>

---

## 📝 Summary

You now understand that C strings are just character arrays ending in `'\0'`, know when to use `fgets` vs `scanf("%s", ...)`, and can compare, copy, and manipulate strings safely using `<string.h>` instead of the `==` trap.

---

## 🚀 What's Next?

Next: **File Handling** - how to make your data survive after the program closes, by reading and writing to disk.

---

## 📚 References

- Kernighan, B. W., & Ritchie, D. M. - *The C Programming Language* (2nd Edition), Prentice Hall.
- [cppreference.com - string.h](https://en.cppreference.com/w/c/string/byte)

---

[⬅ Previous: 10. Two-Dimensional Arrays](./10-Two-Dimensional-Arrays.md) &nbsp;|&nbsp; [🏠 C Course Home](./README.md) &nbsp;|&nbsp; Next ➡ [12. File Handling](./12-File-Handling.md)
