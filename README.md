# .NET Collections in PowerSHell

### A break down in a **visual-style comparison chart**, followed by a **deeper explanation grouped by usage pattern** help provide both a mental model and practical decision points regarding .NET Collections.

 - [Summary Table](#refSummaryTable)
 - [Visual Diagram](#refVisualDiagram)
 - [Sequential Processing](#refSequentialProcessing)
 - [No Duplicate - Uniqueness](#refNoDuplicates)
 - [Key Value Pairing](#refKeyValue)
 - [Thread Safe](#refThreadSafe)
 - [Quick Guide](#refQuickGuide)
 - [Flowchart diagram image](#refFlowChart)
 - [Cheat sheet](#refCheatSheet)
 - [Printable PDF Cheat sheet](dotnet_collections_cheatsheet_ascii.pdf)


### Below is a clear explanation of each of `.NET generic collections` with simple PowerShell examples.

 - [System.Collections.Generic.Queue[string]](#refQueue)
 - [System.Collections.Generic.PriorityQueue[int,string]](#refPriorityQueue)
 - [System.Collections.Generic.HashSet[string]](#refHashSet)
 - [System.Collections.Generic.SortedDictionary[int,string]](#refSortedDictionary)
 - [System.Collections.Generic.SortedList[int,string]](#refSortedList)
 - [System.Collections.Generic.SortedSet[string]](#refSortedSet)
 - [System.Collections.Generic.Stack[string]](#refStack)
 - [System.Collections.Generic.SynchronizedCollection[string]](#refSynchronizedCollection)
 - [System.Collections.Generic.KeyValuePair[int,string]](#refKeyValuePair)


---


### 🔍 Summary Table
<a id="refSummaryTable"></a>
| Collection Type                       | Ordering        | Uniqueness | Thread-safe | Indexed | Use Case                        |
|--------------------------------------|-----------------|------------|-------------|---------|---------------------------------|
| `Queue<T>`                           | FIFO            | ❌         | ❌          | ❌      | Tasks, print queue              |
| `PriorityQueue<TElement, TPriority>` | By priority     | ❌         | ❌          | ❌      | Scheduling by priority          |
| `HashSet<T>`                         | Unordered       | ✅         | ❌          | ❌      | Unique values, fast lookup      |
| `SortedDictionary<TKey,TValue>`      | Sorted by key   | ✅ (keys)  | ❌          | ❌      | Map with sorted keys            |
| `SortedList<TKey,TValue>`            | Sorted by key   | ✅ (keys)  | ❌          | ✅      | Sorted, indexable dictionary    |
| `SortedSet<T>`                       | Sorted          | ✅         | ❌          | ❌      | Sorted unique values            |
| `Stack<T>`                           | LIFO            | ❌         | ❌          | ❌      | Undo, reverse-order processing  |
| `SynchronizedCollection<T>`          | As inserted     | ❌         | ✅          | ✅      | Multi-threaded environments     |


---

## 🔍 Visual Diagram of .NET Generic Collections
<a id="refVisualDiagram"></a>
```
                ┌────────────────────┐
                │   Collection Type  │
                └────────┬───────────┘
                         │
       ┌─────────────────┼────────────────────┐
       ▼                 ▼                    ▼
 [Queue<T>]        [Stack<T>]           [PriorityQueue<T,P>]
 (FIFO order)      (LIFO order)         (Ordered by priority)

       │                 │
   Sequential        Sequential
    Access            Access

       ▼
 ┌────────────────────────────────────────────┐
 │         Keyed or Unkeyed Structures        │
 └────────────────────────────────────────────┘

 ┌─────────────┬─────────────────────┬────────────────────┐
 ▼             ▼                     ▼                    ▼
[HashSet<T>] [SortedSet<T>]    [SortedDictionary<K,V>]  [SortedList<K,V>]
(Unordered)   (Sorted)          (Sorted by key)         (Sorted + indexed)

                   ▼
         [SynchronizedCollection<T>]
            (Thread-safe, list-like)
```

---

## 📘 Deeper Comparison by Use Case

<a id="refSequentialProcessing"></a>
### 🌀 **Sequential Processing**

| Collection | Description | Order | Notes |
|-----------|-------------|-------|-------|
| **Queue<T>** | First-In-First-Out | Yes | `Enqueue()` → `Dequeue()`, e.g. task queue |
| **Stack<T>** | Last-In-First-Out | Yes | `Push()` → `Pop()`, e.g. undo history |
| **PriorityQueue<TElement, TPriority>** | Ordered by priority | Yes (by `TPriority`) | Lower priority values come out first |

---

### 🧩 **Set-like Behavior (Uniqueness)**
<a id="refNoDuplicates"></a>
| Collection | Sorted? | Thread-safe? | Notes |
|------------|---------|--------------|-------|
| **HashSet<T>** | ❌ | ❌ | Unordered set, fastest for existence checks |
| **SortedSet<T>** | ✅ | ❌ | Keeps elements in sorted order, e.g. "abc..." |

---

### 🔑 **Key → Value Mapping**
<a id="refKeyValue"></a>
| Collection | Sorted? | Indexed Access? | Notes |
|------------|---------|------------------|-------|
| **SortedDictionary<TKey, TValue>** | ✅ | ❌ | Better for frequent inserts/removals |
| **SortedList<TKey, TValue>** | ✅ | ✅ | Better for small datasets & index access |
| **PriorityQueue<TElement, TPriority>** | Partially (by priority) | ❌ | Not a full dictionary, but uses key concept |
| **KeyValuePair<TKey, TValue>** | — | — | Lightweight struct for representing a pair |

---

### 🔐 **Thread-safe Use**
<a id="refThreadSafe"></a>
| Collection | Description |
|------------|-------------|
| **SynchronizedCollection<T>** | Lock-wrapped version of a list. Useful in multi-threaded scripts or background jobs |

---

## ✅ When to Use What (Quick Guide)
<a id="refQuickGuide"></a>
| Situation | Recommended Collection |
|-----------|------------------------|
| Task queue, producer-consumer | `Queue<T>` |
| Undo/redo, backtracking | `Stack<T>` |
| Priority-based jobs | `PriorityQueue<TElement, TPriority>` |
| Unique items, fast lookup | `HashSet<T>` |
| Unique sorted items | `SortedSet<T>` |
| Mapping keys to values | `SortedDictionary<K,V>` or `SortedList<K,V>` |
| Thread-safe collection of items | `SynchronizedCollection<T>` |
| Returning 2 related values | `KeyValuePair<K,V>` |

---


<a id="refQueue"></a>
### 🔹 `[System.Collections.Generic.Queue[string]]`  
**Type**: FIFO (First-In-First-Out) collection.

**Use when**: You want to process items in the order they were added (e.g., print jobs, tasks).

**Example**:
```powershell
$q = [System.Collections.Generic.Queue[string]]::new()
$q.Enqueue("task1")
$q.Enqueue("task2")
$q.Dequeue()  # Output: task1
```

---
<a id="refPriorityQueue"></a>
### 🔹 `[System.Collections.Generic.PriorityQueue[int, string]]`  
**Type**: Queue where items are dequeued based on **priority**.

**Use when**: You want to process items by priority rather than insertion order.

**Example**:
```powershell
$pq = [System.Collections.Generic.PriorityQueue[int, string]]::new()
$pq.Enqueue(42, "low")     # Value: 42, Priority: "low"
$pq.Enqueue(7, "high")     # Value: 7, Priority: "high"
$pq.Dequeue()              # Output: 7 (since "high" < "low" lexicographically)
```

📝 In this case, lower string values = higher priority. You can define custom comparers too.

---
<a id="refHashSet"></a>
### 🔹 `[System.Collections.Generic.HashSet[string]]`  
**Type**: Unordered collection of **unique** values.

**Use when**: You need fast lookups and don't want duplicates.

**Example**:
```powershell
$set = [System.Collections.Generic.HashSet[string]]::new()
$set.Add("apple")
$set.Add("banana")
$set.Add("apple")  # Won't be added again
$set.Count         # Output: 2
```

---
<a id="refSortedDictionary"></a>
### 🔹 `[System.Collections.Generic.SortedDictionary[int, string]]`  
**Type**: Dictionary that keeps keys **sorted**.

**Use when**: You want fast lookups **and** need keys in sorted order.

**Example**:
```powershell
$sd = [System.Collections.Generic.SortedDictionary[int, string]]::new()
$sd.Add(3, "c")
$sd.Add(1, "a")
$sd.Add(2, "b")

foreach ($kv in $sd) {
    "$($kv.Key) => $($kv.Value)"
}
# Output in order: 1 => a, 2 => b, 3 => c
```

---
<a id="refSortedList"></a>
### 🔹 `[System.Collections.Generic.SortedList[int, string]]`  
**Type**: Like `SortedDictionary`, but **backed by an array** and uses **less memory**.

**Use when**: You have relatively **few items** and want fast indexed access and sorted keys.

**Example**:
```powershell
$sl = [System.Collections.Generic.SortedList[int, string]]::new()
$sl.Add(10, "ten")
$sl.Add(5, "five")
$sl.Values[0]  # Output: "five"
```

🆚 Compared to `SortedDictionary`, `SortedList` is better for smaller datasets or when index-based access is needed.

---
<a id="refSortedSet"></a>
### 🔹 `[System.Collections.Generic.SortedSet[string]]`  
**Type**: Set of **unique values** that are **automatically sorted**.

**Use when**: You need a sorted, duplicate-free list of items.

**Example**:
```powershell
$ss = [System.Collections.Generic.SortedSet[string]]::new()
$ss.Add("banana")
$ss.Add("apple")
$ss.Add("banana")  # Ignored
$ss                # Output: apple, banana (sorted)
```

---
<a id="refStack"></a>
### 🔹 `[System.Collections.Generic.Stack[string]]`  
**Type**: LIFO (Last-In-First-Out) stack.

**Use when**: You want to access items in reverse order of how they were added (e.g., undo history).

**Example**:
```powershell
$s = [System.Collections.Generic.Stack[string]]::new()
$s.Push("step1")
$s.Push("step2")
$s.Pop()  # Output: step2
```

---
<a id="refSynchronizedCollection"></a>
### 🔹 `[System.Collections.Generic.SynchronizedCollection[string]]`  
**Type**: Thread-safe collection.

**Use when**: You're working with **multiple threads** and need to safely add/remove items.

**Example**:
```powershell
$sync = [System.Collections.Generic.SynchronizedCollection[string]]::new()
$sync.Add("log1")
$sync.Add("log2")
$sync[0]  # Output: log1
```

🔐 Ensures that only one thread modifies the collection at a time.

---


<a id="refFlowChart"></a>

### 🔹 Flowchart diagram image showing the relationship and usage of generic collections

![generic_collections_flowchart](generic_collections_flowchart.png)

---


<a id="refCheatSheet"></a>

### 🔹Cheat sheet with detailed usage comparison

[Printable PDF Cheat sheet](dotnet_collections_cheatsheet_ascii.pdf)

![cheatsheet](cheatsheet.png)




