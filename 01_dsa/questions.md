# Data Structures & Algorithms — 90 Questions (basic → advanced)

⭐ = very frequently asked. (This is *conceptual* DSA — complexity, data structures, algorithm ideas. For coding practice, do LeetCode; this is what they ask you to *explain*.)

---

## A. Complexity Analysis

**1. ⭐ What is time complexity / Big-O notation?**
Big-O describes how an algorithm's running time grows as the input size *n* grows, in the **worst case**, ignoring constants and lower-order terms. It tells you scalability, not exact time. O(n) means time grows linearly with input; O(1) means constant regardless of input.

**2. ⭐ What is space complexity?**
How much *extra* memory an algorithm uses as a function of input size (not counting the input itself). e.g. an in-place sort is O(1) extra space; merge sort needs O(n) extra.

**3. ⭐ Explain the common complexities from best to worst.**
**O(1)** constant (array index) < **O(log n)** logarithmic (binary search) < **O(n)** linear (loop) < **O(n log n)** (good sorts) < **O(n²)** quadratic (nested loops) < **O(2ⁿ)** exponential (naive recursion) < **O(n!)** factorial (permutations). Lower grows slower = better.

**4. Big-O vs Big-Ω vs Big-Θ?**
**Big-O**: upper bound (worst case — "no worse than"). **Big-Ω (Omega)**: lower bound (best case — "no better than"). **Big-Θ (Theta)**: tight bound (both — the exact growth rate). Interviews usually mean Big-O (worst case).

**5. Best vs Average vs Worst case?**
The same algorithm can perform differently on different inputs. **Best case**: most favorable input (e.g. already-sorted for some sorts). **Worst case**: least favorable (what Big-O usually reports). **Average case**: expected over random inputs. e.g. QuickSort: best/avg O(n log n), worst O(n²).

**6. Why do we ignore constants in Big-O?**
Because for large *n*, the growth *rate* dominates: O(n) beats O(n²) eventually no matter the constants. `5n` and `100n` are both O(n) — the constant doesn't change scalability. (For small inputs constants matter, but Big-O is about scaling.)

**7. What is amortized complexity?**
The average cost per operation over a sequence, when occasional expensive operations are "spread out." e.g. a dynamic array's `append` is usually O(1), but occasionally O(n) when it resizes — *amortized* O(1) because resizes are rare.

---

## B. Arrays & Strings

**8. ⭐ What is an array?**
A contiguous block of memory storing elements of the same type, accessed by index in **O(1)**. Fixed size (in static arrays). Strength: instant random access. Weakness: insertion/deletion in the middle is O(n) (must shift elements), and fixed size.

**9. Array operations and their complexity?**
Access by index: **O(1)**. Search (unsorted): **O(n)**. Insert/delete at end (dynamic array): amortized **O(1)**. Insert/delete at middle/start: **O(n)** (shifting).

**10. ⭐ What is a dynamic array (ArrayList/vector/Python list)?**
An array that automatically resizes. When full, it allocates a larger array (typically 2×) and copies elements over — that copy is O(n) but happens rarely, giving **amortized O(1)** appends. Combines random access with flexible size.

**11. Array vs Linked List?**
**Array**: O(1) random access, contiguous memory (cache-friendly), but O(n) insert/delete in middle and fixed/resizing cost. **Linked List**: O(1) insert/delete at a known position, dynamic size, but O(n) access (must traverse) and extra memory for pointers, poor cache locality. Use arrays for access-heavy, linked lists for frequent insert/delete.

**12. How is a string stored? Why are strings often immutable?**
A string is typically an array of characters. Immutability (Java/Python) enables safe sharing, caching, hashcode reuse, and thread-safety; "modifying" creates a new string. (See OOP Q52.)

---

## C. Linked Lists

**13. ⭐ What is a linked list?**
A linear data structure where each element (**node**) holds data + a pointer to the next node. Not contiguous in memory. Dynamic size; O(1) insert/delete at a known node; O(n) access/search.

**14. Singly vs Doubly vs Circular linked list?**
**Singly**: each node points to the next only (one direction). **Doubly**: each node points to next *and* previous (bidirectional traversal, easier deletion, more memory). **Circular**: the last node points back to the first (useful for round-robin).

**15. ⭐ How do you detect a cycle in a linked list?**
**Floyd's Cycle Detection ("tortoise and hare")**: two pointers, one moving 1 step, one 2 steps. If there's a cycle, they eventually meet; if the fast pointer hits null, there's no cycle. O(n) time, O(1) space.

**16. How do you reverse a linked list?**
Iterate through, reversing each node's `next` pointer to point to the previous node, tracking prev/current/next. O(n) time, O(1) space. (Or recursively, O(n) space.) A super-common interview task.

**17. How to find the middle of a linked list?**
Two pointers: slow (1 step) and fast (2 steps). When fast reaches the end, slow is at the middle. One pass, O(n).

---

## D. Stacks & Queues

**18. ⭐ What is a stack?**
A **LIFO** (Last In First Out) structure — the last element added is the first removed. Operations: **push** (add to top), **pop** (remove from top), **peek/top** (view top), all O(1). Think: a stack of plates. Used for: function call stack, undo, expression evaluation, backtracking.

**19. ⭐ What is a queue?**
A **FIFO** (First In First Out) structure — the first element added is the first removed. Operations: **enqueue** (add to rear), **dequeue** (remove from front), O(1). Think: a line at a counter. Used for: scheduling, BFS, buffering.

**20. Real uses of stacks?**
Function call stack (managing calls/recursion), expression evaluation (infix→postfix), balanced-parentheses checking, undo/redo, browser back button, DFS, backtracking.

**21. What is a circular queue? Why?**
A queue where the rear wraps around to the front of a fixed array (using modulo), reusing freed space from dequeues. Avoids the wasted space a naive array-queue suffers after dequeuing. Efficient fixed-size buffer.

**22. What is a deque?**
Double-Ended Queue — insertion and deletion allowed at *both* ends. Generalizes both stack and queue. Used in sliding-window problems.

**23. What is a priority queue?**
A queue where elements have priorities and the highest- (or lowest-) priority element is dequeued first, regardless of insertion order. Usually implemented with a **heap** (O(log n) insert/remove). Used in Dijkstra, scheduling, top-K problems.

**24. How do you implement a queue using two stacks (or vice versa)?**
**Queue with 2 stacks**: one for enqueue (push), one for dequeue (when empty, pour the first into it reversing order). Amortized O(1). A classic puzzle testing understanding of LIFO/FIFO.

---

## E. Trees

**25. ⭐ What is a tree?**
A hierarchical, non-linear structure of nodes with a **root**, where each node has children, and there are no cycles. Terms: **root** (top), **leaf** (no children), **parent/child**, **height** (longest root-to-leaf path), **depth** (distance from root), **subtree**.

**26. ⭐ What is a binary tree?**
A tree where each node has at most **two** children (left and right). Variants: full, complete, perfect, balanced, degenerate.

**27. Full vs Complete vs Perfect binary tree?**
**Full**: every node has 0 or 2 children. **Complete**: all levels filled except possibly the last, which fills left-to-right (heap shape). **Perfect**: all internal nodes have 2 children and all leaves are at the same level.

**28. ⭐ What is a Binary Search Tree (BST)?**
A binary tree where for every node, all left-subtree values are *smaller* and all right-subtree values are *larger*. This ordering enables O(log n) search/insert/delete **if balanced** — but degrades to O(n) if it becomes skewed (like a linked list).

**29. ⭐ BST operations complexity?**
Search/Insert/Delete: **O(log n)** average (balanced), **O(n)** worst case (skewed tree). In-order traversal gives sorted order. Balancing (AVL/Red-Black) guarantees O(log n).

**30. ⭐ What are tree traversals?**
Ways to visit all nodes. **DFS** types: **In-order** (Left→Root→Right — gives sorted order in a BST), **Pre-order** (Root→Left→Right — copy/serialize a tree), **Post-order** (Left→Right→Root — delete a tree, evaluate expressions). **BFS**: **Level-order** (visit level by level, uses a queue).

**31. In-order, Pre-order, Post-order — when to use each?**
**In-order**: get sorted data from a BST. **Pre-order**: serialize/clone a tree (root first). **Post-order**: safely delete (children before parent), or evaluate expression trees. **Level-order**: shortest-path-like, level processing.

**32. ⭐ What is a balanced tree? Why balance?**
A tree where the heights of subtrees differ by at most a small constant, keeping height O(log n) so operations stay fast. Without balancing, inserts can create a skewed O(n) tree. **AVL** (strictly balanced, more rotations) and **Red-Black** (looser, fewer rotations, used in many libraries/maps) are self-balancing BSTs.

**33. AVL vs Red-Black tree?**
**AVL**: stricter balance (faster lookups), but more rotations on insert/delete. **Red-Black**: looser balance (slightly slower lookups), fewer rotations (faster inserts/deletes). Red-Black is used in Java's TreeMap, C++ std::map, Linux kernel — when writes are frequent.

**34. ⭐ What is a heap?**
A **complete binary tree** satisfying the heap property: in a **max-heap**, every parent ≥ its children (max at root); in a **min-heap**, every parent ≤ its children (min at root). Gives O(1) access to max/min, O(log n) insert/extract. Backs priority queues and Heap Sort. Usually stored in an array.

**35. How is a heap stored in an array?**
For a node at index `i`: left child at `2i+1`, right child at `2i+2`, parent at `(i-1)/2`. No pointers needed — compact and cache-friendly. That's why heaps use arrays.

**36. What is a Trie (prefix tree)?**
A tree for storing strings where each node represents a character and paths from root spell words. Enables fast prefix search, autocomplete, and dictionary lookups in O(length of word), regardless of how many words are stored. Used in search suggestions, spell-checkers.

**37. What is a B-tree / B+ tree? Where used?**
Balanced multi-way search trees with many children per node (high fan-out), keeping them shallow — minimizing disk reads. **Databases and file systems** use them for indexing. B+ trees store all data in linked leaves (great for range queries). *(This is why I said B+ trees back DB indexes in the DBMS file.)*

**38. What is a segment tree / Fenwick tree (brief)?**
Advanced structures for **range queries** (sum/min/max over a range) and updates in O(log n). **Segment tree**: versatile range queries. **Fenwick (Binary Indexed) tree**: compact prefix sums. Asked in competitive contexts, rarely in basic fresher rounds.

---

## F. Hashing

**39. ⭐ What is a hash table / hash map?**
A structure mapping keys to values using a **hash function** that converts a key into an array index, giving **average O(1)** insert/lookup/delete. Backs dictionaries, sets, caches. Worst case O(n) if many collisions.

**40. ⭐ What is a hash function?**
A function that maps a key to a fixed-size integer (the index). A good hash function distributes keys uniformly to minimize collisions, is fast, and deterministic (same key → same hash).

**41. ⭐ What is a collision? How is it handled?**
A **collision** is when two different keys hash to the same index. Resolved by: **Chaining** (each bucket holds a linked list/tree of entries) or **Open addressing** (probe for the next free slot — linear/quadratic probing, double hashing). Collisions are why worst-case is O(n).

**42. What is the load factor?**
`number of entries / number of buckets`. As it rises, collisions increase. When it crosses a threshold (e.g. 0.75), the table **resizes** (rehashes everything into a bigger array) to maintain O(1) average performance.

**43. ⭐ Hash table vs BST/Tree map — trade-off?**
**Hash table**: O(1) average operations but unordered (no sorted traversal, no range queries), worst-case O(n). **Balanced BST (tree map)**: O(log n) but keeps keys *sorted* (enables ordered traversal, range queries, floor/ceiling). Use a hash map for speed, a tree map when you need order.

**44. What is a set? How is it implemented?**
A collection of **unique** elements (no duplicates), with fast membership testing. Typically backed by a hash table (HashSet — O(1)) or a balanced BST (TreeSet — O(log n), sorted).

---

## G. Sorting (high-frequency)

**45. ⭐ Compare the major sorting algorithms (time/space/stable).**
| Algorithm | Best | Avg | Worst | Space | Stable |
|-----------|------|-----|-------|-------|--------|
| Bubble | O(n) | O(n²) | O(n²) | O(1) | Yes |
| Selection | O(n²) | O(n²) | O(n²) | O(1) | No |
| Insertion | O(n) | O(n²) | O(n²) | O(1) | Yes |
| Merge | O(n log n) | O(n log n) | O(n log n) | O(n) | Yes |
| Quick | O(n log n) | O(n log n) | O(n²) | O(log n) | No |
| Heap | O(n log n) | O(n log n) | O(n log n) | O(1) | No |

**46. ⭐ Explain Merge Sort.**
**Divide and conquer**: recursively split the array in half until single elements, then **merge** sorted halves back together. Always O(n log n), **stable**, but needs O(n) extra space. Great for linked lists and external sorting (large data on disk).

**47. ⭐ Explain Quick Sort.**
Divide and conquer: pick a **pivot**, **partition** so smaller elements go left and larger go right, then recursively sort each side. Average O(n log n), in-place (O(log n) stack), usually the fastest in practice — but O(n²) worst case (bad pivots, e.g. sorted input with first-element pivot). Fixed by randomized/median pivot.

**48. ⭐ Merge Sort vs Quick Sort — when to use which?**
**Quick Sort**: faster on average, in-place (less memory), great for arrays — but unstable and O(n²) worst case. **Merge Sort**: guaranteed O(n log n), stable, good for linked lists / huge external data — but O(n) extra space. Quick for general in-memory arrays; Merge when you need stability or worst-case guarantees.

**49. What is a stable sort? Why care?**
A **stable** sort preserves the relative order of equal elements. Matters when sorting by multiple keys (e.g. sort by name, then by age — stability keeps the name-order within same ages). Merge/Insertion/Bubble are stable; Quick/Selection/Heap are not.

**50. Explain Insertion Sort. When is it good?**
Builds the sorted array one element at a time, inserting each into its correct position among the already-sorted part. O(n²) worst but **O(n) on nearly-sorted data** and great for **small arrays** — so hybrid sorts (TimSort) use it for small chunks.

**51. Explain Heap Sort.**
Build a max-heap from the array, then repeatedly extract the max (swap root to end, shrink heap, heapify). O(n log n) always, in-place (O(1) space), but unstable and typically slower than QuickSort in practice (poor cache locality).

**52. Explain Bubble/Selection Sort (and why they're taught but not used).**
**Bubble**: repeatedly swap adjacent out-of-order elements (bubbling the largest to the end). **Selection**: repeatedly find the minimum and put it at the front. Both O(n²) — simple to understand but too slow for real use; they're pedagogical.

**53. What is Counting / Radix / Bucket sort? (non-comparison)**
Sorts that beat O(n log n) by *not comparing* elements. **Counting sort**: count occurrences of each value — O(n+k), good for small integer ranges. **Radix sort**: sort by digit, least to most significant — O(d·n). **Bucket sort**: distribute into buckets, sort each. They trade generality for speed on specific data.

**54. What is the lower bound for comparison-based sorting?**
**O(n log n)** — no comparison sort can do better in the worst case (provable via decision trees). To go faster you must avoid comparisons (counting/radix), which only works on restricted data.

---

## H. Searching

**55. ⭐ Linear vs Binary search?**
**Linear search**: check each element — O(n), works on any (even unsorted) data. **Binary search**: repeatedly halve a **sorted** array by comparing the middle — O(log n), but requires sorted data. Huge speedup: 1 million elements = ~20 comparisons.

**56. ⭐ Explain Binary Search precisely.**
On a sorted array, compare the target to the middle element: if equal, found; if target is smaller, search the left half; if larger, search the right half. Repeat, halving the search space each time → O(log n). Watch for off-by-one bugs in `low`/`high`/`mid`.

**57. What is interpolation search?**
An improvement over binary search for **uniformly distributed** sorted data — it estimates the likely position (like guessing a name's page in a phonebook) instead of always taking the middle. O(log log n) on uniform data, O(n) worst case.

---

## I. Algorithm Paradigms

**58. ⭐ What is recursion?**
A function that calls itself to solve smaller instances of a problem, with a **base case** to stop. Each call adds a frame to the call stack. Elegant for tree/divide-conquer problems but risks stack overflow and can be slower than iteration. e.g. factorial, tree traversal.

**59. Recursion vs Iteration?**
**Recursion**: cleaner for naturally recursive problems (trees, divide & conquer), but uses stack memory (O(depth)) and has call overhead. **Iteration**: more memory-efficient and often faster, but can be clumsier for recursive structures. Any recursion can be converted to iteration (sometimes with an explicit stack).

**60. ⭐ What is Divide and Conquer?**
A paradigm: **divide** the problem into smaller subproblems, **conquer** them recursively, **combine** results. Examples: Merge Sort, Quick Sort, Binary Search. Often gives O(n log n).

**61. ⭐ What is Dynamic Programming (DP)?**
Solving complex problems by breaking them into **overlapping subproblems** and storing each subproblem's solution to avoid recomputation. Requires **optimal substructure** (optimal solution built from optimal subsolutions) + **overlapping subproblems**. Two styles: **memoization** (top-down, cache recursion) and **tabulation** (bottom-up, fill a table). e.g. Fibonacci, knapsack, longest common subsequence.

**62. ⭐ Memoization vs Tabulation?**
**Memoization** (top-down): recursive + a cache of computed results — only computes needed subproblems, but has recursion overhead. **Tabulation** (bottom-up): iterative, fill a table from base cases up — no recursion, often more space-efficient. Same complexity, different style.

**63. ⭐ DP vs Divide and Conquer?**
Both break problems into subproblems. **D&C**: subproblems are *independent* (no overlap) — solve once (Merge Sort). **DP**: subproblems *overlap* — store results to reuse (Fibonacci, where naive recursion recomputes the same values exponentially).

**64. ⭐ What is a Greedy algorithm?**
Makes the **locally optimal** choice at each step, hoping for a global optimum. Fast and simple, but only correct when the problem has the **greedy-choice property** (local optima lead to global). Examples: Dijkstra, Huffman coding, activity selection, Kruskal/Prim. Counter-example: greedy fails for 0/1 knapsack (use DP).

**65. Greedy vs DP — how to choose?**
**Greedy**: when local choices provably lead to the global optimum (faster). **DP**: when you must consider combinations / future consequences of choices (greedy would miss the optimum). If unsure, DP is safer (it explores more); greedy is faster when valid.

**66. What is Backtracking?**
A brute-force-with-pruning technique: build a solution incrementally, and **abandon (backtrack)** a path as soon as it can't lead to a valid solution. Explores a tree of possibilities. Examples: N-Queens, Sudoku, permutations, maze solving.

**67. Backtracking vs Brute Force?**
**Brute force** tries all possibilities blindly. **Backtracking** prunes — it stops exploring a branch the moment it's clearly invalid, drastically cutting the search. Backtracking = smart brute force.

**68. What is the difference between BFS and DFS?**
Both traverse graphs/trees. **BFS** (Breadth-First): explore level by level using a **queue** — finds shortest path in unweighted graphs, more memory. **DFS** (Depth-First): go as deep as possible then backtrack, using a **stack** (or recursion) — less memory, good for cycle detection, topological sort, connectivity.

---

## J. Graphs

**69. ⭐ What is a graph?**
A set of **vertices (nodes)** connected by **edges**. Models networks: social, maps, web links. Types: **directed** (edges have direction) vs **undirected**, **weighted** (edges have costs) vs **unweighted**, **cyclic** vs **acyclic**.

**70. ⭐ How are graphs represented?**
**Adjacency matrix**: a V×V grid, `matrix[i][j]=1` if edge exists — O(V²) space, O(1) edge lookup, good for dense graphs. **Adjacency list**: each vertex stores a list of its neighbors — O(V+E) space, efficient for sparse graphs (most real graphs). Adjacency list is the common choice.

**71. Adjacency matrix vs list — trade-off?**
**Matrix**: O(1) to check if an edge exists, but O(V²) space (wasteful for sparse graphs) and O(V) to find all neighbors. **List**: O(V+E) space (compact for sparse), O(degree) to list neighbors, but O(degree) to check a specific edge. Use list for sparse, matrix for dense or frequent edge-existence checks.

**72. ⭐ BFS in graphs — how and uses?**
Start at a source, visit all neighbors (using a **queue**), then their neighbors, level by level, marking visited. Uses: **shortest path in unweighted graphs**, finding connected components, level-order processing. O(V+E).

**73. ⭐ DFS in graphs — how and uses?**
Go deep along each branch before backtracking (using recursion/**stack**), marking visited. Uses: **cycle detection**, **topological sorting**, finding connected components, path existence, maze/puzzle solving. O(V+E).

**74. ⭐ What is Dijkstra's algorithm?**
Finds the **shortest path** from a source to all vertices in a **weighted graph with non-negative weights**. Greedy: repeatedly pick the closest unvisited vertex (via a **min-heap/priority queue**) and relax its edges. O((V+E) log V). Fails with negative weights (use Bellman-Ford).

**75. Dijkstra vs Bellman-Ford?**
**Dijkstra**: faster (O((V+E)log V)), but only **non-negative** weights. **Bellman-Ford**: slower (O(V·E)), but handles **negative** weights and **detects negative cycles**. Use Dijkstra normally; Bellman-Ford when negative edges are possible.

**76. What is a Minimum Spanning Tree (MST)? Prim vs Kruskal?**
An MST connects all vertices with the **minimum total edge weight**, no cycles. **Prim's**: grow the tree from a start vertex, always adding the cheapest edge to a new vertex (good for dense graphs). **Kruskal's**: sort all edges, add the cheapest that doesn't form a cycle (uses Union-Find; good for sparse graphs). Both greedy.

**77. ⭐ What is topological sorting?**
A linear ordering of vertices in a **Directed Acyclic Graph (DAG)** such that for every edge u→v, u comes before v. Uses: task scheduling with dependencies, build systems, course prerequisites. Done via DFS (reverse finish order) or Kahn's algorithm (BFS with in-degrees). Only possible if no cycle.

**78. ⭐ How do you detect a cycle in a graph?**
**Undirected**: DFS — if you reach an already-visited node that isn't the parent, there's a cycle (or use Union-Find). **Directed**: DFS with a "recursion stack" — if you revisit a node currently in the recursion stack (a "back edge"), there's a cycle. *(This is conceptually the same skill as my Floyd cycle detection in linked lists.)*

**79. What is Union-Find (Disjoint Set)?**
A structure tracking elements partitioned into disjoint sets, with two operations: **find** (which set is x in?) and **union** (merge two sets). With path compression + union by rank, nearly O(1) per operation. Used in Kruskal's MST and cycle detection.

**80. What is the difference between a tree and a graph?**
A **tree** is a special graph: connected, **acyclic**, with exactly V−1 edges and one path between any two nodes. A **graph** can have cycles, be disconnected, and have any number of edges. Every tree is a graph; not every graph is a tree.

---

## K. Mixed / Advanced concepts

**81. What is the two-pointer technique?**
Using two indices moving through data (toward each other, or at different speeds) to solve problems in O(n) instead of O(n²). Examples: pair-sum in a sorted array, removing duplicates, palindrome check, Floyd's cycle detection.

**82. What is the sliding window technique?**
Maintaining a "window" (subarray/substring) that slides over the data, adjusting its bounds, to solve subarray/substring problems in O(n) instead of O(n²). Examples: max sum of k consecutive elements, longest substring without repeats.

**83. What is the difference between an in-place and out-of-place algorithm?**
**In-place**: uses O(1) extra space, modifying the input directly (Quick Sort, Heap Sort, reversing an array). **Out-of-place**: uses extra space proportional to input (Merge Sort needs O(n)). In-place saves memory.

**84. What is a deterministic vs randomized algorithm?**
**Deterministic**: same input → same steps and output every time. **Randomized**: uses randomness in its logic (e.g. randomized QuickSort's pivot) to get good *expected* performance and avoid worst-case adversarial inputs.

**85. What is the difference between P and NP (brief)?**
**P**: problems solvable in polynomial time. **NP**: problems whose solutions can be *verified* in polynomial time. **NP-complete**: the hardest in NP (if one is solved fast, all are). Whether P = NP is the famous open question. Practically: NP-complete problems (e.g. Traveling Salesman) have no known efficient exact solution — use heuristics/approximation.

**86. What is hashing used for besides hash maps?**
Cryptographic integrity (checksums, password hashing), deduplication, caching keys, distributed systems (consistent hashing for sharding), Bloom filters, detecting duplicates. The "map a big thing to a small fingerprint" idea is everywhere.

**87. What is a Bloom filter?**
A space-efficient probabilistic structure that tests set membership: it can say "definitely not in the set" or "possibly in the set" (false positives possible, false negatives impossible). Used when memory is tight and occasional false positives are OK (e.g. "have we seen this URL?").

**88. What is the difference between a data structure and an abstract data type (ADT)?**
An **ADT** defines *what* operations are supported (the interface), e.g. "a Stack supports push/pop." A **data structure** is *how* it's implemented (array vs linked list). A Stack (ADT) can be built on an array or a linked list (data structures).

**89. ⭐ How do you choose the right data structure for a problem?**
Ask: What operations are most frequent? Need fast lookup → hash map. Need sorted order / range queries → balanced BST/heap. Need LIFO/FIFO → stack/queue. Need fast min/max → heap. Need prefix matching → trie. Need relationships/paths → graph. Match the structure's strengths to the operation you do most.

**90. ⭐ Time-space tradeoff — explain with an example.**
Often you can use more memory to save time, or vice versa. Example: a hash map uses O(n) extra space to make lookups O(1) instead of O(n) linear search. Memoization in DP trades space (a cache) for dramatically less time. Choosing the balance is a core engineering judgment.

---

## ⭐ The 15 DSA must-knows:
Big-O + complexity ladder (1,3) · Array vs Linked List (11) · Stack & Queue (18,19) · BST + traversals (28,30) · Balanced trees (32) · Heap (34) · Hash table + collisions (39,41) · Merge vs Quick sort (48) · Binary search (56) · Recursion (58) · DP + memo vs tab (61,62) · Greedy (64) · BFS vs DFS (68) · Dijkstra (74) · choosing a data structure (89).
