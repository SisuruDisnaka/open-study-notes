[⬅ Previous: 09. Arrays](./09-Arrays.md) &nbsp;|&nbsp; [🏠 C Course Home](./README.md) &nbsp;|&nbsp; Next ➡ [11. Strings in C](./11-Strings.md)

---

# 📘 10. Two-Dimensional Arrays

| | |
|---|---|
| **Difficulty** | 🟡 Intermediate |
| **Estimated Reading Time** | ~12 minutes |
| **Prerequisites** | [Arrays](./09-Arrays.md) |

**Progress**
```
████████████████████░░░░░░  Lesson 10 of 13
```

---

## 🌟 Why Learn This?

- A single row of lockers (a normal array) is great for a list - but what about a spreadsheet, a chessboard, or a photo made of pixels arranged in rows and columns?
- For that, you need a **grid**.
- A 2D array is simply an array of arrays - the natural way to represent any data that has two dimensions: rows and columns.

---

## 🎯 By the End of This Lesson

You should know

✔ How to declare, initialize, and index a 2D array

✔ How to loop through a 2D array using nested loops correctly

✔ The exact rule for passing a 2D array to a function (and why the column size is mandatory)

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

You should be comfortable with regular (1D) arrays and nested loops from [Lesson 9](./09-Arrays.md).

---

## 💡 Imagine This...

- Imagine a bingo hall with rows of seats, and every seat labeled by row and seat number - "Row 2, Seat 3."
- To find any specific person, you need *two* numbers, not one.
- A 2D array works exactly the same way: `marks[2][3]` means "row 2, column 3" - you always need both coordinates to locate a single value in the grid.

---

## 📖 Core Concepts

A 2D array is a grid - rows and columns:

```c
int marks[3][4];   // 3 rows, 4 columns = 12 total elements
```

### Access

```c
marks[0][0] = 75;   // row 0, column 0
marks[2][3];         // row 2, column 3
```

### Initialization

```c
int table[2][3] = { {1,2,3}, {4,5,6} };   // full, grouped by row
int table[2][3] = {1,2,3,4,5,6};          // compact, same result
int table[2][3] = { {1,2}, {4} };         // partial - anything missing becomes 0
```

### Nested Loops

The rule: outer loop = rows, inner loop = columns.

```c
for (int i = 0; i < 3; i++) {
    for (int j = 0; j < 4; j++) {
        scanf("%d", &marks[i][j]);
    }
}
```

### Passing 2D Arrays to Functions - a Must-Know Rule

```c
void printMatrix(int a[][3], int rows) { ... }   // CORRECT - column size is given
void display(int a[][]) { ... }                   // WRONG - compiler needs the column size!
```

> **Trick:** for a 2D array parameter, the **column count is mandatory**. The row count can be passed as a separate parameter, or omitted entirely from the brackets.

---

## 🔍 Behind the Scenes

- A 2D array isn't actually stored as a grid in memory - memory is fundamentally one long strip of addresses.
- C stores 2D arrays in **row-major order**: the entire first row, followed immediately by the entire second row, and so on, all laid flat, back to back.
- This is precisely *why* the compiler needs to know the column size in advance - to calculate the correct offset for `a[i][j]`, it needs to know how many elements to "skip" to get past each full row.

---

## 🖥 Visual Explanation

```
int m[2][3] = {{1,2,3},{4,5,6}};

Conceptually:            In actual memory (row-major order):
┌───┬───┬───┐            ┌───┬───┬───┬───┬───┬───┐
│ 1 │ 2 │ 3 │  row 0      │ 1 │ 2 │ 3 │ 4 │ 5 │ 6 │
├───┼───┼───┤            └───┴───┴───┴───┴───┴───┘
│ 4 │ 5 │ 6 │  row 1       row 0 -----|  row 1 -----|
└───┴───┴───┘
```

---

## 💻 Code Example

```c
#include <stdio.h>

void printMatrix(int a[][3], int rows) {
    for (int i = 0; i < rows; i++) {
        for (int j = 0; j < 3; j++) {
            printf("%d ", a[i][j]);
        }
        printf("\n");
    }
}

int main() {
    int matrix[2][3] = { {1, 2, 3}, {4, 5, 6} };
    printMatrix(matrix, 2);
    return 0;
}
```

**Expected Output:**
```
1 2 3
4 5 6
```

**Step-by-step execution:**
1. `matrix` is a 2-row, 3-column grid, initialized row by row.
2. `printMatrix` receives it with the column count `3` fixed in the parameter, and `rows` passed separately.
3. The outer loop walks each row; the inner loop walks each column within that row.

**Why it works:** the function signature `int a[][3]` gives the compiler enough information (the column width) to correctly calculate the memory offset for every `a[i][j]`.

---

## 🎮 Predict the Output

```c
int grid[2][2] = {{1,2},{3,4}};
printf("%d", grid[0][1] + grid[1][0]);
```

<details>
<summary>💡 Reveal the answer</summary>

`5`

`grid[0][1] = 2` and `grid[1][0] = 3`, so `2 + 3 = 5`.
</details>

---

## 🧪 Try It Yourself

Write a program that reads a 3×3 grid of integers from the user and prints the sum of each row.

---

## 🎯 Mini Challenge

Write a function `int trace(int a[][3], int n)` that computes the **trace** of a square matrix - the sum of its diagonal elements (`a[0][0] + a[1][1] + a[2][2]`, generalized for any size `n`).

<details>
<summary>💡 Need a hint?</summary>

The diagonal is exactly where the row index equals the column index - `a[i][i]`.
</details>

---

## 🌍 Real World Applications

| Concept | Where it shows up |
|---|---|
| 2D arrays | Spreadsheet software represents your entire worksheet as a giant 2D grid internally |
| Row-major storage | Image processing libraries store pixel grids in row-major order, exactly as C does, for performance reasons |
| Matrices | Game engines and graphics software use 2D (and higher-dimensional) arrays constantly for transformations and grids |

---

## 🧠 Memory Tricks

- **Outer loop = rows, inner loop = columns** - "Row before column, like reading a book top to bottom, then left to right within a line."
- **Column size is mandatory in a 2D array function parameter** - the compiler needs it to do its row-skipping math.

---

## 🎉 Fun Fact

Some programming languages (like Fortran) store 2D arrays in **column-major** order instead of row-major - the opposite of C. This isn't just trivia: numerical computing libraries have to be extremely careful when passing data between C and Fortran-based code (many scientific libraries mix both), because reading a column-major array as if it were row-major produces a completely scrambled, transposed result.

---

## ⚠ Common Mistakes

```c
// ❌ Wrong - swapped loop bounds, mixing up rows and columns
for (int i = 0; i < 3; i++) {        // rows = 3
    for (int j = 0; j < 3; j++) {    // but the array is only 2 columns wide!
        printf("%d ", a[i][j]);
    }
}

// ✅ Correct - match loop bounds exactly to the array's actual dimensions
for (int i = 0; i < 2; i++) {        // rows = 2
    for (int j = 0; j < 3; j++) {    // columns = 3
        printf("%d ", a[i][j]);
    }
}
```
*Why:* mismatching your loop bounds against the array's real dimensions causes out-of-bounds access, exactly like the 1D array bug from the previous lesson - just in two dimensions now.

---

## 🚫 Beginner Traps

- **"You can omit both dimensions when passing a 2D array to a function."** False - the column size is always required so the compiler can calculate memory offsets correctly.
- **"2D arrays are stored as an actual grid, with rows in separate memory blocks."** False - they're stored as one continuous strip of memory, row after row (row-major order).

---

## 📌 Exam Tips

- Always match the outer loop to rows and the inner loop to columns - question setters frequently swap them deliberately to test attentiveness.
- Memorize the exact function signature pattern: `void func(int a[][COLS], int rows)` - the column count in brackets, non-negotiable.
- Trace `a[i][j]` questions by drawing the actual grid on paper, labeling rows and columns explicitly.

---

## 🎤 Interview Questions

**Q: Why must the column size be specified when passing a 2D array to a function, but not the row size?**
> Because the compiler needs to know how many elements make up a single row in order to calculate the correct memory offset for any `array[i][j]` access. The row count is only used for loop bounds and can be passed as a separate integer parameter instead.

**Q: How are 2D arrays actually stored in memory in C?**
> In row-major order - the entire first row is stored contiguously, immediately followed by the entire second row, and so on, all as one continuous block of memory, not as separate grid cells.

---

## ❓ Quiz

**Multiple Choice**

1. What does `marks[2][3]` refer to? <br>
   A) Row 3, Column 2  
   B) Row 2, Column 3  
   C) A 2×3 array  
   D) An error
2. In C, 2D arrays are stored in memory as: <br>
   A) Column-major order  
   B) Row-major order  
   C) Random order  
   D) Separate blocks per row
3. Which is required in a 2D array function parameter? <br>
   A) Row size only  
   B) Column size  
   C) Both, always  
   D) Neither
4. In nested loops for a 2D array, what does the outer loop typically represent? <br>
   A) Columns  
   B) Rows  
   C) Both  
   D) Neither
5. What is `int table[2][3] = {1,2,3,4,5,6};` equivalent to? <br>
   A) `{{1,2},{3,4},{5,6}}`  
   B) `{{1,2,3},{4,5,6}}`  
   C) A syntax error  
   D) `{{1},{2,3},{4,5,6}}`

<details><summary>✅ Reveal Answers</summary>

1. B  2. B  3. B  4. B  5. B
</details>

**True / False**

1. A 2D array in C is stored as separate memory blocks for each row.
2. The row count can be omitted from a 2D array function parameter's brackets.
3. `a[i][j]` always requires two index values.

<details><summary>✅ Reveal Answers</summary>

1. False (it's one continuous block, row-major)  2. True  3. True
</details>

**Short Answer**

1. Why does the compiler require the column size when a 2D array is passed to a function?

<details><summary>✅ Reveal Guidance</summary>

Because 2D arrays are stored as one continuous strip of memory in row-major order, and the compiler needs to know how many elements make up one row in order to correctly calculate the memory offset for any `a[i][j]` access - without the column size, it can't determine where each row begins.
</details>

---

## 📝 Summary

You now know how to declare, initialize, index, and loop through 2D arrays, how they're actually laid out in memory (row-major order), and the non-negotiable rule about specifying column size when passing one to a function.

---

## 🚀 What's Next?

Next: **Strings** - you'll discover that a C string is really just a character array with one special extra rule.

---

## 📚 References

- Kernighan, B. W., & Ritchie, D. M. - *The C Programming Language* (2nd Edition), Prentice Hall.

---

[⬅ Previous: 09. Arrays](./09-Arrays.md) &nbsp;|&nbsp; [🏠 C Course Home](./README.md) &nbsp;|&nbsp; Next ➡ [11. Strings in C](./11-Strings.md)
