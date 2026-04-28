# Data Structures & Algorithms (Front-End) — Arrays & strings

DSA shows up in front-end interviews because it tests core problem solving: recognizing patterns, analyzing complexity, and writing correct code under constraints. This section starts with the most common category: **arrays and strings**.

## How to use this file

- Treat each question as a **flashcard**: try solving or explaining out loud first.
- Always state **time/space complexity** and any assumptions.
- Keep a small set of reusable patterns (two pointers, sliding window, hashing).

---

## Arrays & strings — core patterns

### 1) What should you say first in an array/string interview problem?

**Short answer**:
- Clarify inputs/outputs and constraints (length, character set, sorted? duplicates?).
- Identify the likely pattern (two pointers, sliding window, hashmap, prefix sums).
- State the target complexity (often \(O(n)\) or \(O(n \log n)\)).

---

### 2) What are the most common patterns for arrays/strings?

**Short answer**:
- **Two pointers**: sorted arrays, reverse, dedupe, palindrome checks.
- **Sliding window**: subarray/substring with constraints (max/min/at most k).
- **Hash map / frequency counts**: anagrams, duplicates, first unique, “two sum”.
- **Prefix sums**: range sums, “subarray sum equals k”.
- **Sorting**: when order helps or to enable two pointers (trade \(O(n \log n)\)).

---

### 3) When do you use two pointers?

**Short answer**: When you can advance pointers monotonically (often on sorted data) or when you need in-place linear scans from both ends.

**Typical problems**:
- Two Sum (sorted variant)
- Remove duplicates in-place
- Reverse string/array
- Valid palindrome (ignore non-alphanumerics)

---

### 4) When do you use sliding window?

**Short answer**: When you need a **contiguous** subarray/substring and can move left/right bounds while maintaining a running invariant.

**Common clues**:
- “Longest/shortest substring/subarray…”
- “At most k distinct…”
- “Minimum window that contains…”

**Pitfall**: Sliding window doesn’t work for all “sum = k” problems with negatives (prefix sums often needed).

---

### 5) When do you use a hash map?

**Short answer**: When you need fast membership/lookup or frequency counts.

**Examples**:
- Two Sum (unsorted) → map from value to index
- Anagram checks → frequency map
- Longest substring without repeating characters → last seen index map

---

### 6) How do you reason about time and space complexity quickly?

**Short answer**:
- One pass with constant-time work → \(O(n)\).
- Nested loops over n → \(O(n^2)\) (unless the inner pointer only moves forward overall, as in many two-pointer/sliding-window solutions).
- Hash map operations are average \(O(1)\) (but discuss worst case if asked).
- Sorting dominates: \(O(n \log n)\).

---

## Hash maps (maps / dictionaries)

### 1) What is a hash map, conceptually?

**Short answer**: A hash map stores key → value pairs and supports average-case \(O(1)\) insert/lookup/delete. In interviews, it’s the go-to tool for “have I seen this before?” and frequency counting.

---

### 2) When is a hash map the right pattern?

**Short answer**: When you need:
- fast membership tests (“seen” set)
- counts/frequencies (anagrams, top-k, duplicates)
- complements/indices (Two Sum)
- prefix-sum bookkeeping (“subarray sum equals k”)

**Clues**:
- “Find pairs…”
- “Count how many…”
- “First/most frequent…”
- “Contains/duplicate…”

---

### 3) Frequency map pattern (strings/arrays): what does it look like?

**Short answer**: Iterate once; increment counts; then compare/inspect counts.

**Example uses**:
- Valid anagram
- “Can you construct…” problems (ransom note)
- Character frequency sorting / grouping

---

### 4) Complement map pattern: what does it look like?

**Short answer**: For each value \(x\), compute complement \(target - x\) (or a required counterpart), check if it exists in the map, otherwise store \(x\).

**Example uses**:
- Two Sum (unsorted)
- Pair with given difference

---

### 5) Prefix-sum + hash map: why does it work?

**Short answer**: If \(prefix[j] - prefix[i] = k\), then \(prefix[i] = prefix[j] - k\). While scanning, store counts of seen prefix sums; for each new prefix, add `count[prefix - k]`.

**Example uses**:
- Subarray sum equals k
- Count subarrays with sum divisible by k

---

### 6) JS-specific: `Object` vs `Map` vs `Set` in interviews?

**Short answer**:
- Use `Map` when keys aren’t just small strings or when you want reliable iteration semantics.
- Use plain objects for simple string-key frequency maps (but be careful with prototype pitfalls).
- Use `Set` for membership-only “seen” logic.

**Practical guidance**:
- For frequency: `new Map()` or `{}` both work; `Map` avoids key collisions like `"__proto__"`.

---

### 7) Common pitfalls with hash maps

**Short answer**:
- Forgetting to include all parameters in the key (when keying by multiple fields)
- Off-by-one in “last seen index” problems
- Not updating counts when shrinking a sliding window
- Using the wrong type for keys (stringifying objects accidentally)

---

## Stacks & queues

### 1) What is a stack, and what is it good for?

**Short answer**: A stack is LIFO (last in, first out). It’s great for “nested/undo/backtrack” problems: parentheses matching, path simplification, monotonic stack patterns, DFS-like traversal state.

**Common operations**:
- push, pop, peek → typically \(O(1)\)

---

### 2) What is a queue, and what is it good for?

**Short answer**: A queue is FIFO (first in, first out). It’s great for processing in arrival order: BFS, task scheduling, sliding window buffers, producer/consumer.

**Common operations**:
- enqueue, dequeue, front/peek → typically \(O(1)\) with the right structure

---

### 3) How do you implement stacks and queues in JavaScript?

**Short answer**:
- Stack: use an array with `push`/`pop`.
- Queue: avoid `shift()` for large data (it’s \(O(n)\)); prefer a pointer-based queue (array + head index) or a deque implementation.

**Queue sketch**:
- store items in an array
- keep `head` index
- `enqueue`: `arr.push(x)`
- `dequeue`: return `arr[head++]`

---

### 4) Classic stack problem: Valid Parentheses — what’s the pattern?

**Short answer**: Scan characters; push opening brackets; when you see a closing bracket, check it matches the top of the stack; stack must end empty.

**Key pitfalls**:
- Closing bracket with empty stack
- Mismatched types

---

### 5) Monotonic stack: when does it show up?

**Short answer**: When you need “next greater/smaller element”, “daily temperatures”, “largest rectangle in histogram”. The stack maintains a monotonic property (increasing/decreasing) so each element is pushed/popped at most once → \(O(n)\).

---

### 6) BFS with a queue: what’s the high-level idea?

**Short answer**: Visit nodes level by level. Enqueue a start node, then repeatedly dequeue, visit neighbors, enqueue unvisited neighbors.

**Common gotcha**: mark nodes visited when enqueuing (not when dequeuing) to avoid duplicates.

---

## Trees & graphs

### 1) What is a tree vs a graph?

**Short answer**:
- **Tree**: a connected, acyclic graph with a root in most interview formulations (each node has 0..k children). There’s exactly one simple path between any two nodes.
- **Graph**: a set of vertices and edges; can be directed/undirected, cyclic/acyclic, weighted/unweighted, connected/disconnected.

---

### 2) DFS vs BFS: when do you use each?

**Short answer**:
- **DFS** (depth-first): explore as far as possible; great for recursion/backtracking, path existence, components, topological ideas.
- **BFS** (breadth-first): explore level by level; best for **shortest path in unweighted graphs** and “minimum steps” problems.

**Rule of thumb**:
- “Minimum number of moves” → BFS.
- “Explore all possibilities / backtracking” → DFS.

---

### 3) How do you traverse a binary tree? (pre/in/post-order)

**Short answer**:
- **Preorder**: node → left → right
- **Inorder**: left → node → right (sorted output for BST)
- **Postorder**: left → right → node

**Common uses**:
- Preorder: serialize/copy tree
- Inorder: BST in sorted order
- Postorder: compute from children up (heights, delete tree)

---

### 4) Recursion vs iteration: what are the trade-offs in JS?

**Short answer**:
- Recursion is clean for trees, but deep recursion can hit call stack limits.
- Iteration with an explicit stack/queue avoids recursion depth issues.

**Interview tip**: Mention you can convert recursive DFS to iterative using a stack.

---

### 5) Graph representation: adjacency list vs adjacency matrix

**Short answer**:
- **Adjacency list**: space \(O(V+E)\), efficient for sparse graphs (most interview cases).
- **Adjacency matrix**: space \(O(V^2)\), fast edge lookup, better for dense graphs.

---

### 6) What is the “visited” set for, and when do you need it?

**Short answer**: A visited set prevents infinite loops and repeated work in graphs with cycles. In trees you often don’t need it (no cycles), unless you treat it as an undirected graph (parent links) or have shared references.

---

### 7) Topological sort: when does it show up (high level)?

**Short answer**: In directed acyclic graphs (DAGs) to order tasks with dependencies (course schedule, build order). Common approaches: DFS postorder or Kahn’s algorithm (in-degrees + queue).

---

## Recursion

### 1) What is recursion?

**Short answer**: A function calling itself to solve a problem by reducing it to smaller subproblems until it reaches a **base case**.

---

### 2) What are the two essential parts of any recursive solution?

**Short answer**:
- **Base case**: when to stop recursing.
- **Recursive step**: how the problem size decreases toward the base case.

**Common failure mode**: missing/incorrect base case → infinite recursion.

---

### 3) How do you think about recursion in interviews? (mental checklist)

**Short answer**:
- Define the function contract: input → output.
- Identify the base case(s).
- Identify the smaller subproblem and ensure progress.
- Decide what the function returns vs what is accumulated (return value vs side effects).

---

### 4) Recursion vs iteration: what’s the trade-off?

**Short answer**:
- Recursion can be simpler for trees/backtracking.
- Iteration avoids call stack limits and can be easier to control for performance.

**JS note**: Deep recursion can cause “Maximum call stack size exceeded”; converting DFS recursion to an explicit stack is a common fix.

---

### 5) What is backtracking?

**Short answer**: A recursion pattern that explores choices, and when a choice doesn’t work, it “backs up” and tries another option (often using a temporary path list that you push/pop).

**Common problems**:
- permutations/combinations/subsets
- word search
- n-queens

---

### 6) What is memoization and when does it help?

**Short answer**: Caching results of recursive calls to avoid repeated work. It turns many exponential recursive solutions (like naive Fibonacci) into polynomial time.

**Interview hint**: If you see repeated subproblems, consider memoization or DP.

---

## Sorting & searching

### 1) When is sorting a good idea in interviews?

**Short answer**: When sorting simplifies the problem enough to justify the cost \(O(n \log n)\)—often to enable **two pointers**, grouping, or deduping.

**Examples**:
- Merge intervals
- 3Sum / kSum patterns
- Grouping anagrams (via sorted key)
- Deduping + scanning

---

### 2) What should you say about JavaScript `sort()` in interviews?

**Short answer**: JS `Array.prototype.sort()` sorts **lexicographically by default**, so you must provide a comparator for numbers.

**Example**:

```js
[10, 2, 1].sort();            // ["1","10","2"] style behavior
[10, 2, 1].sort((a, b) => a - b); // [1, 2, 10]
```

---

### 3) What is binary search and when does it apply?

**Short answer**: Binary search finds a target in a **sorted** array in \(O(\log n)\) by repeatedly halving the search space.

**Common variations**:
- Find first/last occurrence (lower/upper bound)
- Search in rotated sorted array
- “Binary search on answer” (search the feasible value range)

---

### 4) What is “binary search on answer”?

**Short answer**: When you can check a condition `isFeasible(x)` that is monotonic (true for all \(x \ge k\) or \(x \le k\)), you can binary search the answer space even if the input isn’t a sorted array.

**Examples**:
- Minimum capacity/time to finish tasks
- Minimum speed to process items

---

### 5) Linear search vs binary search: what’s the trade-off?

**Short answer**:
- Linear search: \(O(n)\), works on unsorted data.
- Binary search: \(O(\log n)\), requires sorted/monotonic structure.

**Interview tip**: If you need many queries, paying a one-time sort \(O(n \log n)\) can be worth it.

---

## Big-O complexity

### 1) What is Big-O (and what is it *not*)?

**Short answer**: Big-O describes how runtime or memory grows with input size \(n\) as \(n\) becomes large. It ignores constants and lower-order terms. It’s an approximation model, not a stopwatch.

---

### 2) Common time complexities you should recognize quickly

**Short answer**:
- \(O(1)\): constant (hash lookup average, array index)
- \(O(\log n)\): halving search space (binary search)
- \(O(n)\): single pass
- \(O(n \log n)\): sort + divide-and-conquer patterns
- \(O(n^2)\): nested loops over n (all pairs)
- \(O(2^n)\), \(O(n!)\): exhaustive subsets/permutations (backtracking without pruning/memo)

---

### 3) How do you compute Big-O for loops with two pointers/sliding window?

**Short answer**: Even with nested-looking loops, if each pointer only moves forward at most \(n\) times total, the runtime is \(O(n)\).

**Example mental model**:
- Left pointer moves \(n\) steps max.
- Right pointer moves \(n\) steps max.
- Total pointer moves \( \le 2n \) → \(O(n)\).

---

### 4) What is amortized analysis? (why `push` is “O(1)”)

**Short answer**: Some operations occasionally do expensive work (like resizing an array), but over many operations the average cost per operation is constant.

**Example**:
- Dynamic arrays occasionally reallocate/copy, but over \(n\) appends the total work is \(O(n)\) → amortized \(O(1)\) per append.

---

### 5) Time vs space: what trade-offs should you mention?

**Short answer**: Many optimizations trade memory for speed (e.g., using a hash map to reduce \(O(n^2)\) to \(O(n)\) but using \(O(n)\) space). Mention both.

---

### 6) What’s a common Big-O pitfall in interviews?

**Short answer**:
- Claiming hash maps are always \(O(1)\) without acknowledging average vs worst-case (usually average is fine to state).
- Forgetting that sorting dominates the runtime when present.
- Missing hidden costs (e.g., `Array.shift()` is \(O(n)\) in JS).

---

## Classic interview questions (prompts)

### 1) Two Sum (array, return indices)

**Goal**: Return indices of two numbers that add to target.

**Expected pattern**: hash map.

---

### 2) Valid Anagram (two strings)

**Goal**: Determine if one string is an anagram of the other.

**Expected pattern**: frequency map or sort.

---

### 3) Longest Substring Without Repeating Characters

**Goal**: Find length of the longest substring with all unique characters.

**Expected pattern**: sliding window + last seen index.

---

### 4) Minimum Window Substring

**Goal**: Smallest substring containing all chars of another string.

**Expected pattern**: sliding window + frequency counts.

---

### 5) Subarray Sum Equals K

**Goal**: Count subarrays whose sum equals k.

**Expected pattern**: prefix sums + hashmap of seen prefix sums.

---

## Suggested practice exercises

- Implement: reverse string in-place, remove duplicates in sorted array (two pointers).
- Implement: longest substring without repeating characters (sliding window).
- Implement: subarray sum equals k (prefix sums).
- Implement: group anagrams (frequency map keying strategy).
- Implement: valid parentheses (stack).
- Implement: next greater element (monotonic stack).
- Implement: BFS traversal using a queue without using `shift()`.
- Implement: binary tree level-order traversal (BFS).
- Implement: max depth of binary tree (DFS).
- Implement: number of connected components in an undirected graph (DFS/BFS + visited).
- Implement: recursive factorial and Fibonacci, then add memoization to Fibonacci.
- Implement: generate all subsets of an array (backtracking).
- Implement: binary search (iterative) and then lower bound (first index ≥ target).
- Implement: search in rotated sorted array.
- For each solution, write the invariant (what is always true during the loop).

## Links / references

- NeetCode patterns (conceptual): https://neetcode.io/
- Big-O cheat sheet: https://www.bigocheatsheet.com/
- VisuAlgo (visualizations): https://visualgo.net/en
