# Programming Fundamentals — 40 Questions

⭐ = very frequently asked. The language-agnostic basics recruiters use to check you understand *how programs actually work*.

---

## A. How code runs

**1. ⭐ Compiler vs Interpreter?**
A **compiler** translates the *entire* source code into machine code *before* running (C, C++, Go, Rust) — fast execution, errors caught at compile time, but a build step. An **interpreter** executes code *line by line* at runtime (Python, JavaScript) — flexible, easier to debug, platform-independent, but slower. Some languages (Java) do both: compile to bytecode, then interpret/JIT it.

**2. ⭐ What is the difference between compiled and interpreted languages — with Java/Python examples?**
**Java**: source → compiled to **bytecode** (`.class`) → run by the **JVM** (which interprets + JIT-compiles to native). "Write once, run anywhere." **Python**: source → compiled to bytecode (`.pyc`) → executed by the Python interpreter. Both have a bytecode step; the difference is degree of ahead-of-time compilation and the runtime.

**3. What is bytecode?**
An intermediate, platform-independent low-level representation between source code and machine code. A virtual machine (JVM, Python VM) runs it. It enables portability — the same bytecode runs anywhere the VM exists. (You met `.pyc` in the project-anatomy repo.)

**4. What is a JIT compiler?**
Just-In-Time compiler — compiles frequently-run bytecode into native machine code *during* execution (at runtime), combining interpreter flexibility with compiled speed. Used by the JVM and modern JS engines (V8). "Hot" code paths get compiled and cached.

**5. What is the difference between a static and dynamic type system?**
**Static typing** (Java, C++): variable types are checked at *compile time*; type errors caught early; more verbose. **Dynamic typing** (Python, JS): types checked at *runtime*; flexible, less code, but type errors surface only when the bad line runs.

**6. Strongly vs weakly typed?**
**Strongly typed**: the language strictly enforces type rules, with few implicit conversions (Python — `"5" + 5` errors). **Weakly typed**: more implicit coercion (JavaScript — `"5" + 5` gives `"55"`). Orthogonal to static/dynamic.

---

## B. Memory

**7. ⭐ Stack vs Heap memory?**
**Stack**: stores local variables and function-call frames; LIFO; fast (just move a pointer); auto-managed (freed on return); limited size. **Heap**: dynamic allocation (`new`/`malloc`); large/flexible; manually freed or garbage-collected; slower; can fragment. (Also in OS Q66.)

**8. ⭐ What is the call stack?**
The region of memory tracking active function calls. Each call **pushes** a stack frame (parameters, locals, return address); returning **pops** it. It's how the program knows where to return after a function finishes. Too-deep recursion overflows it.

**9. What causes a memory leak?**
Allocating memory that's never freed and no longer reachable for reuse — it accumulates until the program runs out. In manual-memory languages (C/C++): forgetting `free`. In GC languages: unintentionally keeping references (e.g. growing a global list, unclosed resources). *(My agno file-handle fix prevented a resource leak — the same idea.)*

**10. ⭐ What is garbage collection? Trade-offs?**
Automatic reclamation of memory no longer referenced (Java, Python, Go). **Pro**: no manual freeing, fewer leaks/dangling pointers. **Con**: runtime overhead and unpredictable pauses (GC "stop-the-world"). Manual memory (C/C++) is faster but error-prone.

**11. What is a dangling pointer / use-after-free?**
A pointer referencing memory that's already been freed. Dereferencing it causes undefined behavior/crashes/security holes. A C/C++ hazard; GC languages prevent it by not freeing memory that's still referenced.

**12. What is a null pointer / NullPointerException?**
A reference that points to nothing (`null`/`None`/`nullptr`). Using it (calling a method, accessing a field) throws an error (Java's `NullPointerException`, the "billion-dollar mistake"). Prevent with null checks, Optional types, or non-null guarantees.

**13. ⭐ Pass by value vs pass by reference?**
**Pass by value**: the function gets a *copy* — changes don't affect the original. **Pass by reference**: the function gets the *actual* variable — changes affect the original. Java is always pass-by-value (but object *references* are passed by value, so you can mutate the object). Python is "pass by object reference." A classic confusion. (Also OOP Q47.)

---

## C. Control flow & recursion

**14. ⭐ What is recursion? Give the two essential parts.**
A function calling itself on smaller input. Needs a **base case** (when to stop) and a **recursive case** (calls itself on a reduced problem). Without a base case → infinite recursion → stack overflow. e.g. `factorial(n) = n * factorial(n-1)`, base `factorial(0)=1`. (Also DSA Q58.)

**15. What is tail recursion?**
Recursion where the recursive call is the *last* operation in the function. Some compilers optimize it into a loop (tail-call optimization), reusing one stack frame → no stack growth. (Python doesn't do TCO; many functional languages do.)

**16. Recursion vs iteration — trade-offs?**
Recursion is cleaner for recursive structures (trees) but uses stack memory (O(depth)) and has call overhead. Iteration is more memory/time-efficient but sometimes clumsier. Convert recursion to iteration with an explicit stack if depth is a concern.

**17. What is short-circuit evaluation?**
In `A && B`, if `A` is false, `B` isn't evaluated (the result is already false); in `A || B`, if `A` is true, `B` is skipped. Used for safety (`if (obj != null && obj.field)`) and efficiency.

**18. What is the difference between `break`, `continue`, and `return`?**
**`break`**: exit the current loop entirely. **`continue`**: skip to the next iteration of the loop. **`return`**: exit the entire function (and return a value).

---

## D. Data & operators

**19. ⭐ What are primitive vs reference (object) types?**
**Primitives**: hold the actual value directly (int, char, boolean, float) — stored on the stack, copied on assignment. **Reference types**: hold a *reference* (address) to an object on the heap (arrays, objects, strings) — assignment copies the reference, not the object. Explains why mutating a passed object affects the caller.

**20. ⭐ What is the difference between `==` and `.equals()` / `is`?**
`==` compares *references/identity* (same object?) for objects in Java; `.equals()` compares *content/value* (override it). In Python, `==` compares value, `is` compares identity. Comparing strings with `==` when you mean content is a classic bug. (Also OOP Q49.)

**21. Signed vs unsigned integers? What is integer overflow?**
**Signed**: can be negative (one bit for sign). **Unsigned**: only non-negative (full range positive). **Overflow**: a value exceeds the type's max and wraps around (e.g. a 32-bit signed int past ~2.1 billion becomes negative) — a real bug source. Use larger types or check bounds.

**22. Floating-point precision — why is `0.1 + 0.2 != 0.3`?**
Floating-point numbers are stored in binary (IEEE 754) and can't represent some decimals exactly (like 0.1), causing tiny rounding errors. So `0.1 + 0.2` ≈ `0.30000000000000004`. Never compare floats with `==`; use a tolerance (`abs(a-b) < epsilon`). Use decimal types for money.

**23. What are bitwise operators?**
Operate on individual bits: `&` (AND), `|` (OR), `^` (XOR), `~` (NOT), `<<` (left shift = ×2), `>>` (right shift = ÷2). Used for flags, masks, performance tricks, low-level work. e.g. `x & 1` checks if `x` is odd.

**24. What is a constant / immutable variable?**
A variable whose value can't change after assignment (`final` in Java, `const` in C++/JS). Expresses intent, prevents accidental modification, and enables optimizations. (Also OOP Q55.)

---

## E. Functions, scope, errors

**25. What is variable scope? Local vs global?**
**Scope** = where a variable is accessible. **Local**: defined inside a function, accessible only there. **Global**: defined at the top level, accessible everywhere. Prefer local — globals create hidden dependencies and bugs.

**26. What is a closure?**
A function that "remembers" variables from its enclosing scope even after that scope has finished. The inner function "closes over" the outer variables. Used for callbacks, factories, encapsulation. Common in JS/Python.

**27. What is the difference between parameters and arguments?**
**Parameters**: the variable names in the function definition. **Arguments**: the actual values passed when calling. `def f(x)` — `x` is a parameter; `f(5)` — `5` is an argument. (Also OOP Q-functions.)

**28. What is a pure function?**
A function that (1) always returns the same output for the same input and (2) has no side effects (doesn't modify external state). Predictable, testable, cacheable. The basis of functional programming.

**29. What is a side effect?**
Any observable change a function makes beyond returning a value: modifying a global, writing a file, printing, mutating an argument. Side effects make code harder to reason about and test; minimize them.

**30. ⭐ What is exception handling? try/catch/finally?**
A mechanism to handle runtime errors gracefully instead of crashing. **try**: code that might fail. **catch/except**: handle the error. **finally**: always runs (cleanup, e.g. close a file) whether or not an error occurred. *(The `with` statement / context manager from my file-handle fix is a cleaner alternative to try/finally for resources.)*

**31. What is the difference between throwing and catching an exception?**
**Throwing/raising**: signaling an error occurred (`throw`/`raise`). **Catching/handling**: intercepting it to respond (log, retry, recover). Unhandled exceptions propagate up the call stack and crash the program.

**32. Checked vs unchecked exceptions?**
**Checked** (Java): must be declared/handled at compile time (recoverable, e.g. `IOException`). **Unchecked** (RuntimeExceptions, e.g. `NullPointerException`): not compiler-enforced, usually programming bugs. (Also OOP Q57.)

---

## F. Concurrency & misc

**33. ⭐ Process vs Thread (programmer's view)?**
A **process** is an independent program with its own memory. A **thread** runs inside a process and shares its memory with sibling threads. Threads are lighter and communicate easily (shared memory) but require synchronization to avoid race conditions. (Also OS Q5.)

**34. ⭐ What is a race condition?**
When the program's correctness depends on the timing/interleaving of concurrent threads accessing shared data — leading to unpredictable results (e.g. two threads incrementing a counter and losing an update). Fixed with synchronization (locks/mutexes). *(This underlies the async bugs I fixed in open source.)*

**35. What is a deadlock (programming context)?**
Two or more threads each waiting for a resource (lock) the other holds, so none proceeds. Avoid by acquiring locks in a consistent order, using timeouts, or minimizing lock scope. (Also OS Q33.)

**36. What is thread safety?**
Code is thread-safe if it behaves correctly when accessed by multiple threads simultaneously — typically via synchronization, immutability, or avoiding shared state. Immutable objects are inherently thread-safe.

**37. What is the difference between concurrency and parallelism?**
**Concurrency**: structuring a program to handle multiple tasks by interleaving them (possibly on one core). **Parallelism**: actually executing tasks simultaneously on multiple cores. Concurrency is about *dealing with* many things; parallelism is about *doing* many things at once. (Also OS Q62 — central to my async OSS work.)

**38. What is an API (programming context)?**
A defined interface for using a library/service: the functions, classes, and contracts it exposes. You program *against* the API without knowing the implementation — abstraction in action. (Also Networks Q45.)

**39. What is the DRY principle and why does it matter?**
"Don't Repeat Yourself" — avoid duplicating logic; put each piece of knowledge in one place. Duplication means bugs must be fixed in multiple spots and code drifts out of sync. (Also OOP Q45.)

**40. ⭐ What is debugging? What's your approach?**
Finding and fixing bugs. A good approach: **reproduce** the bug reliably → **isolate** where it happens (logging, breakpoints, binary search the code) → **understand** the root cause (not just the symptom) → **fix** → **verify** with a test that would catch a regression. Mentioning "I add a regression test so it can't come back" shows maturity. *(Exactly how I handled my open-source bug fixes.)*

---

## ⭐ The 12 Programming Fundamentals must-knows:
Compiler vs interpreter (1) · bytecode/JVM (2,3) · static vs dynamic typing (5) · stack vs heap (7) · call stack (8) · garbage collection (10) · pass by value vs reference (13) · recursion (14) · primitive vs reference types (19) · == vs equals (20) · race condition (34) · exception handling (30).
