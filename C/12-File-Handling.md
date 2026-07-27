[⬅ Previous: 11. Strings in C](./11-Strings.md) &nbsp;|&nbsp; [🏠 C Course Home](./README.md) &nbsp;|&nbsp; Next ➡ [13. Structures](./13-Structures.md)

---

# 📘 12. File Handling

| | |
|---|---|
| **Difficulty** | 🟡 Intermediate |
| **Estimated Reading Time** | ~14 minutes |
| **Prerequisites** | [Strings in C](./11-Strings.md) |

**Progress**
```
██████████████████████████░  Lesson 12 of 13
```

---

## 🌟 Why Learn This?

- Every variable you've used so far lives in RAM, and RAM is wiped clean the instant your program ends.
- If you want data to actually *survive* - a saved game, a config file, a log of transactions - it needs to live on disk, in a file.
- File handling is how C reaches outside its own temporary memory bubble and interacts with the permanent world.

---

## 🎯 By the End of This Lesson

You should know

✔ How to open, read, write, and close files safely

✔ Every file mode (`r`, `w`, `a`, and their variants) and when to use each

✔ How to read multiple records from a file using `fscanf()`'s return value

✔ Why checking `fopen()`'s return value against `NULL` is non-negotiable

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

You should be comfortable reading/writing formatted data with `printf`/`scanf` from [Lesson 2](./02-Data-Types-Variables-IO.md), and strings from [Lesson 11](./11-Strings.md).

---

## 💡 Imagine This...

- Imagine a notebook you can write in during class - it stays exactly as you left it even after you go home, come back the next day, and open it again.
- Your program's variables are like a whiteboard: the instant class ends (the program closes), everything on it is erased.
- A **file** is the notebook: durable, persistent, and there for you the next time you open it.

---

## 📖 Core Concepts

```c
FILE *fp;
fp = fopen("marks.txt", "r");
if (fp == NULL) { printf("Error opening file!\n"); return 1; }
```

### File Modes - Memorize This Table

| Mode | Meaning |
|---|---|
| `r` | Read (file must already exist) |
| `w` | Write (overwrites, or creates if missing) |
| `a` | Append (adds to the end) |
| `r+` | Read and write |
| `w+` | Read/write, overwriting |
| `a+` | Read/append |

### Writing and Reading

```c
fprintf(fp, "%s %d\n", "Jagath", 80);    // write formatted data to the file

fscanf(fp, "%29s%d", name, &mark);       // read formatted data back
// Note: NO & before "name" (it's a char array), YES & before &mark (it's an int)
```

### Reading Multiple Records - a Standard Exam Pattern Worth Memorizing Whole

```c
while (fscanf(fp, "%29s%d", name, &mark) == 2) {   // returns how many items were read
    printf("%s %d\n", name, mark);
}
```

> **Trick:** `fscanf()` returns the *count of items it successfully read*. Comparing that count against how many format specifiers you gave (here, `2`) is the standard way to detect end-of-file or malformed data.

### Always Close What You Open

```c
fclose(fp);   // flushes/saves data, releases the file resource
```

### Choosing the Right Function

| Task | Function |
|---|---|
| Read one line typed at the keyboard | `fgets()` |
| Read a formatted record from a file | `fscanf()` |
| Read a whole line from a text file | `fgets()` |
| Write formatted data to a file | `fprintf()` |
| Read/write a single character | `fgetc()` / `fputc()` |

---

## 🔍 Behind the Scenes

- When you call `fopen()`, the operating system doesn't hand your program direct access to the physical disk - it creates a buffered connection managed through the `FILE` structure.
- Your reads/writes typically go through an in-memory buffer first for efficiency, only actually touching the disk periodically or when you `fclose()` (which forces a final flush).
- This is exactly why forgetting `fclose()` can sometimes mean your last bit of written data never actually makes it to disk - it was still sitting in the buffer when the program ended.

---

## 🖥 Visual Explanation

```
Your Program                  Buffer                    Disk File
┌─────────────┐          ┌───────────┐            ┌─────────────┐
│ fprintf(fp,  │ ───────▶│  (memory)  │  fclose()  │ marks.txt    │
│  "Jagath 80")│          │  Jagath 80 │ ─────────▶ │ Jagath 80    │
└─────────────┘          └───────────┘            └─────────────┘
```

---

## 💻 Code Example

```c
#include <stdio.h>

int main() {
    FILE *fp = fopen("marks.txt", "w");
    if (fp == NULL) {
        printf("Error opening file!\n");
        return 1;
    }

    fprintf(fp, "Nimal 82\n");
    fprintf(fp, "Kamal 58\n");
    fclose(fp);

    // Now read it back
    fp = fopen("marks.txt", "r");
    if (fp == NULL) {
        printf("Error opening file!\n");
        return 1;
    }

    char name[30];
    int mark;
    while (fscanf(fp, "%29s%d", name, &mark) == 2) {
        printf("%s scored %d\n", name, mark);
    }
    fclose(fp);

    return 0;
}
```

**Expected Output:**
```
Nimal scored 82
Kamal scored 58
```

**Step-by-step execution:**
1. The file is opened in `"w"` mode, and two records are written with `fprintf`.
2. `fclose(fp)` flushes and saves the data to disk.
3. The file is reopened in `"r"` mode.
4. `fscanf` reads one record at a time in a loop, stopping once it can't successfully read both a name and a mark (i.e., when it reaches the end of the file).

**Why it works:** `fscanf`'s return value (the count of successfully read items) is exactly what lets the loop know when to stop - no need to guess how many records the file contains in advance.

---

## 🎮 Predict the Output

```c
FILE *fp = fopen("data.txt", "r");
if (fp == NULL) {
    printf("Failed");
} else {
    printf("Success");
}
```

<details>
<summary>💡 Reveal the answer</summary>

This depends entirely on whether `data.txt` already exists in the program's working directory. `"r"` mode requires the file to already exist - if it doesn't, `fopen()` returns `NULL`, and `"Failed"` prints. This is precisely why checking against `NULL` is mandatory, not optional.
</details>

---

## 🧪 Try It Yourself

Modify the code example to append a third student's record to `marks.txt` using `"a"` mode, without erasing the first two.

---

## 🎯 Mini Challenge

Write a program that reads a list of numbers from a file (one per line) and writes only the even numbers into a second file.

<details>
<summary>💡 Need a hint?</summary>

You'll need two `FILE *` pointers open simultaneously - one for reading the source, one for writing the destination - and remember to `fclose()` both.
</details>

---

## 🌍 Real World Applications

| Concept | Where it shows up |
|---|---|
| File handling | Every configuration file (`.env`, `.json`, `.ini`) your applications read on startup relies on exactly this read/open/close pattern |
| Append mode | Log files (server logs, crash logs) are written using append mode so new entries never overwrite the history |
| Buffered I/O | Databases and text editors carefully manage exactly when data is flushed to disk to balance performance against the risk of data loss on a crash |

---

## 🧠 Memory Tricks

- **`r` = Read (file must exist). `w` = Write (overwrites or creates). `a` = Append (adds to the end).**
- **Always pair `fopen` with `fclose`** - like locking the door you unlocked.
- **`fscanf`'s return value tells you how many fields it actually read** - use it as your loop's stopping condition.

---

## 🎉 Fun Fact

Unix-like systems (including Linux and macOS) follow the philosophy "everything is a file" - not just documents, but hardware devices, network sockets, and even running processes can be interacted with through the exact same `open`/`read`/`write`/`close` operations you just learned in C, just at a lower level. The file-handling mental model you built today scales up to how the entire operating system thinks.

---

## ⚠ Common Mistakes

```c
// ❌ Wrong - no NULL check, will crash if the file doesn't exist
FILE *fp = fopen("data.txt", "r");
fscanf(fp, "%d", &x);   // crash if fp is NULL!

// ✅ Correct
FILE *fp = fopen("data.txt", "r");
if (fp == NULL) {
    printf("Error opening file!\n");
    return 1;
}
fscanf(fp, "%d", &x);
```
*Why:* `fopen()` returns `NULL` if it fails (missing file, permissions issue) - using that `NULL` pointer without checking leads directly to a crash.

---

## 🚫 Beginner Traps

- **"`"w"` mode adds to the end of an existing file."** False - `"w"` mode **overwrites** the entire file's existing contents. You need `"a"` (append) to add without erasing.
- **"Forgetting `fclose()` doesn't really matter."** False - data can remain stuck in the buffer and never actually reach the disk, and the OS may also run out of available file handles if programs never release them.

---

## 📌 Exam Tips

- The `if (fp == NULL)` check after `fopen()` is nearly guaranteed boilerplate in any file-handling exam question - always include it.
- Know the exact difference between `"w"` (overwrite) and `"a"` (append) - a frequent "predict the resulting file content" question.
- Memorize the `while (fscanf(...) == N)` pattern for reading unknown-length files - where `N` is the number of format specifiers used.

---

## 🎤 Interview Questions

**Q: Why must you always check `fopen()`'s return value?**
> Because `fopen()` returns `NULL` if the file couldn't be opened - for example, if it doesn't exist in read mode, or there's a permissions problem - and attempting to read from or write to a `NULL` file pointer causes the program to crash.

**Q: What's the difference between `"w"` and `"a"` file modes?**
> `"w"` opens a file for writing and immediately erases any existing content (or creates a new file if none exists). `"a"` opens a file for writing but preserves existing content, adding any new data to the end of the file instead.

---

## ❓ Quiz

**Multiple Choice**

1. Which mode overwrites an existing file's content? <br>
   A) `r`  
   B) `a`  
   C) `w`  
   D) `r+`
2. What does `fopen()` return if it fails to open a file? <br>
   A) `0`  
   B) `-1`  
   C) `NULL`  
   D) An error message string
3. Which mode adds new data without erasing existing content? <br>
   A) `w`  
   B) `a`  
   C) `r`  
   D) `x`
4. What does `fscanf()` return? <br>
   A) The file pointer  
   B) The count of successfully read items  
   C) Always `0`  
   D) The file's size
5. Why must you call `fclose()`? <br>
   A) It's optional and does nothing important  
   B) It flushes buffered data to disk and releases the file resource  
   C) It deletes the file  
   D) It renames the file

<details><summary>✅ Reveal Answers</summary>

1. C  2. C  3. B  4. B  5. B
</details>

**True / False**

1. `"r"` mode will create a new file if it doesn't already exist.
2. Checking `fp == NULL` after `fopen()` is considered essential, defensive practice.
3. `fprintf()` is used to write formatted data to a file.

<details><summary>✅ Reveal Answers</summary>

1. False (`"r"` requires the file to already exist)  2. True  3. True
</details>

**Short Answer**

1. Why is `while (fscanf(fp, "%s%d", name, &mark) == 2)` a safer loop condition than assuming a fixed number of records?

<details><summary>✅ Reveal Guidance</summary>

Because it doesn't require knowing the file's length in advance - the loop naturally stops the moment `fscanf` can no longer successfully read both expected fields (whether due to reaching the end of the file or encountering malformed data), instead of relying on a hardcoded record count that might not match the actual file.
</details>

---

## 📝 Summary

You now know how to open, read, write, and safely close files, understand every file mode, and can use `fscanf()`'s return value to read an unknown number of records from a file - the standard exam pattern.

---

## 🚀 What's Next?

Next, the final lesson of this course: **Structures** - how to group different data types together into a single, reusable custom type.

---

## 📚 References

- Kernighan, B. W., & Ritchie, D. M. - *The C Programming Language* (2nd Edition), Prentice Hall.
- [cppreference.com - File I/O](https://en.cppreference.com/w/c/io)

---

[⬅ Previous: 11. Strings in C](./11-Strings.md) &nbsp;|&nbsp; [🏠 C Course Home](./README.md) &nbsp;|&nbsp; Next ➡ [13. Structures](./13-Structures.md)
