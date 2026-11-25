# 04 · Why This Is a Computer Engineering Problem

> **Status:** in progress (topic 1 of 5 done) · **Started:** 2026-11-23 · **Interactive version (with an array comparison and a live order book):** [interactive/04-why-a-computer-engineering-problem.html](interactive/04-why-a-computer-engineering-problem.html)

## Goal of this step

Understand what makes a matching engine hard, and which computer engineering topic each difficulty belongs to. This is the "why it matters" part: the advisor and the jury will ask it, and it becomes the background chapter of the thesis.

The step is split into five topics, worked through one at a time:

| # | Topic | Question | Status |
| --- | --- | --- | --- |
| 1 | Data structures | How does the engine keep the order book so that every operation is fast? | Done |
| 2 | Latency | What does "fast" mean here, and where does the time actually go? | Next |
| 3 | Determinism | Why must the same input always give the same trades, and what can quietly break that? | Planned |
| 4 | Crash recovery | How does an event log and replay rebuild the exact state after a crash? | Planned |
| 5 | Concurrency | Why do many real engines do the matching on a single thread? | Planned |

## Content

### Topic 1: Data structures

#### 1.1 What the engine does, again and again

Almost every message makes the engine do one of three things:

| # | Operation | When it happens |
| --- | --- | --- |
| 1 | **Find the best price** (lowest ask or highest bid) | Every incoming order, to check whether it can trade |
| 2 | **Take the oldest order at that price** | Every trade (price-time priority) |
| 3 | **Find any order by its ID and remove it** | Every cancel or modify; the order can be anywhere in the book |

Operation 3 matters more than it looks: in real markets a large share of messages are cancels and modifies, not new orders. That is why the advisor asked for benchmark workloads with a high cancel ratio.

Example used below: four sell orders (asks). Each letter is an order's ID, given in arrival order (A came first). In `A (5)` the number in brackets is the quantity: how many shares the order wants to sell. The price is the level it sits at. So order A means "sell 5 shares, at 101 or more".

```text
price 101 :  A (5)   D (3)      <- best ask; A came first, so A is first in line
price 102 :  B (10)
price 103 :  C (2)
```

For sell orders the best price is the **lowest** one (a buyer wants the cheapest seller). The buy side is the mirror image: the best bid is the **highest** price. Each side has its own structure.

#### 1.2 First try: one unsorted array of all orders

```text
[ A@101, B@102, C@103, D@101 ]      (in arrival order)
```

`A@101` is short for "order A, at price 101".

- 1 (best price): look at every order. O(n).
- 2 (oldest at the best price): the same scan; keep the first one at 101. O(n).
- 3 (cancel): search for the ID, O(n), then shift the later orders left to close the gap, O(n).
- Insert: append at the end, O(1).

With 4 orders this is nothing. A busy book can hold about 100,000 orders, and then every message walks through 100,000 entries.

#### 1.3 Second try: one sorted array

```text
[ A@101, D@101, B@102, C@103 ]      (price low to high, then oldest first)
```

- Operations 1 and 2 become **O(1)**: the first element is both the best price and the oldest order at that price.
- Removing that first order after a full fill shifts everything left: O(n). (A partial fill does not remove it; its quantity just goes down.) Storing the array **in reverse**, best price at the end, makes this removal O(1).
- Insert E@102: binary search finds the spot in O(log n). E goes **after** B, because B arrived first at the same price. Making room shifts C: O(n).
- Cancel: the array is sorted by price, but a cancel carries only an ID, so finding it is still O(n), and removing it shifts the rest: O(n).

| Operation | Unsorted array | Sorted array |
| --- | --- | --- |
| Best price | O(n) | O(1) |
| Oldest at the best price | O(n) | O(1) |
| Cancel by ID | O(n) find + O(n) remove | O(n) find + O(n) remove |
| Insert | O(1) | O(log n) find + O(n) shift |

**Lesson:** sorting made *reading* fast, but any change in the **middle** of a contiguous array still costs O(n). A deque does not help, because it is fast only at its two ends. Every structure is a trade-off.

**Side note (comes back in topic 2):** shifting an array is a `memmove` over contiguous memory, which modern CPUs do very fast. For a small n, an O(n) array can beat an O(log n) tree in real time. Big-O shows how the cost grows, not the real nanoseconds. That is why this project measures instead of guessing.

#### 1.4 Third try: price levels, with a queue per level

Many orders share the same price. A busy book might hold 100,000 orders but only about 200 distinct prices. So instead of sorting orders, **sort the prices**: keep a sorted container of **price levels**, and give each level its own FIFO queue of orders.

```text
price 101 :  A (5)   D (3)
price 102 :  B (10)  E (4)  F (6)
price 103 :  C (2)
```

- New order F@102: find level 102 (binary search, O(log L), where L is the number of levels), then append F to the back of its queue, O(1). A brand-new price adds a level: O(L) in a sorted array, but over about 200 levels, not 100,000 orders.
- A fully filled: pop it from the front of the best level's queue, O(1). When a level becomes empty, it is removed.
- Cancel E: still slow. With only the ID we don't know E's price, so we search every level, O(n). Then removing E from the middle of its queue shifts the orders behind it, O(k), where k is the number of orders at that price.

The cost now depends on **L (levels)** instead of **n (orders)** for everything except cancel.

This is also exactly how Liquibook behaves: its cancel goes to the right price level and then walks through the orders there one by one (`find_on_market`).

#### 1.5 Fixing cancel: a doubly linked list and a hash map

Cancel has two separate problems, each with a classic fix:

1. **Jump from the ID straight to the order.** A **hash map** from order ID to the order gives an O(1) average lookup.
2. **Remove an order from the middle of its queue without shifting.** Use a **doubly linked list** instead of an array. Each order lives anywhere in memory and remembers its two neighbours (`prev` and `next`). Removing E means changing two pointers:

```text
before:   HEAD -> [B] <-> [E] <-> [F] <- TAIL
remove E: B.next = F,  F.prev = B
after:    HEAD -> [B] <---------> [F] <- TAIL
```

Two pointer writes, whatever k is: O(1). Both directions are needed: in a singly linked list, E does not know who points to it, so we would have to walk from the head again. The list still works as a FIFO queue: new orders join at the tail and trades take from the head, both O(1).

Putting the two together, the hash map stores a **pointer to the order's node** in its list. Each order also keeps a pointer to **its price level**, so after unlinking it the engine can fix the level's head or tail and remove the level if it is now empty.

#### 1.6 The whole order book

```text
Order node (one per resting order)          PriceLevel (one per price, per side)
  id, side, price, quantity                   price
  prev -> previous order in the level         head -> oldest order (trades take from here)
  next -> next order in the level             tail -> newest order (new orders join here)
  level -> its PriceLevel

                         ORDER BOOK
  ASKS (sorted, lowest first)
    [101] head -> A(5) <-> D(3) <- tail
    [102] head -> B(10) <-> E(4) <-> F(6) <- tail
    [103] head -> C(2) <- tail
  -- spread --
  BIDS (sorted, highest first)
    [100] head -> G(7) <- tail
    [ 99] head -> H(4) <- tail

  ID INDEX (one hash map for the whole book)
    "A"->A  "B"->B  "C"->C  "D"->D  "E"->E  "F"->F  "G"->G  "H"->H
```

| The book needs to be... | Structure | Cost |
| --- | --- | --- |
| sorted by **price** | a sorted container of price levels | O(1) best price; O(log L) to find a level |
| **FIFO** within a price | a doubly linked list per level | O(1) add at the tail, O(1) take from the head |
| findable by **ID** | a hash map from ID to order node | O(1) on average |

How each operation enters the picture:

| Operation | Entry point | Path |
| --- | --- | --- |
| New order | the opposite side's best level | match from the head; any rest joins the tail of its own level and is added to the map |
| Trade | the best level's head | take from the head, move on to `next` |
| Cancel | the hash map | map -> node -> unlink -> `level` pointer -> fix the level, or remove it if empty |

These three rows are the three research questions the advisor gave: price-level index, FIFO per level with O(1) cancel, and order-ID lookup.

#### 1.7 Worked example: BUY 12 @ 102 arrives (call it X)

Starting from the book in 1.6:

1. Best ask is 101; 101 <= 102, so it crosses. Head of 101 is **A(5)**: trade 5 @ 101, X has 7 left. A is fully filled: unlink it (head of 101 becomes D) and erase "A" from the map.
2. Head of 101 is **D(3)**: trade 3 @ 101, X has 4 left. D is fully filled: unlink it and erase "D". Level 101 is now empty, so it is removed and the best ask becomes 102.
3. Best ask is 102; 102 <= 102, so it crosses. Head of 102 is **B(10)**: trade 4 @ 102, X has 0 left. B is only partly filled: it becomes 6 and **stays at the head**, keeping its time priority. No pointers or map entries change.
4. X is fully filled, so it never enters the book.

```text
TRADE  X x A   5 @ 101
TRADE  X x D   3 @ 101
TRADE  X x B   4 @ 102
```

Every trade happens at the **resting** order's price: X was willing to pay 102 but got 8 shares at 101. Book afterwards:

```text
ASKS   [102] B(6) <-> E(4) <-> F(6)
       [103] C(2)
BIDS   [100] G(7)
       [ 99] H(4)
MAP    "B" "C" "E" "F" "G" "H"
```

Each structure was touched only at its ends (a list head, the front of the levels, single map entries). Nothing was searched or shifted.

#### 1.8 Two catches

- **The structures must stay in sync.** When an order leaves the book, it must leave its list *and* the map. If one is forgotten, the map points to freed memory: a crash or a silently wrong trade. Rules such as "every order in a list has exactly one map entry, and every map entry points to an order in a list" are **invariants**. The advisor listed "order book invariants maintained" as core scope, and v1's unit tests will check them.
- **Linked list nodes are scattered in memory.** Following a pointer to a node that is not in the CPU cache is slow. This is where topic 2 (latency) starts, and it is why v3 tries intrusive lists and pre-allocated memory.

#### 1.9 How this connects to the roadmap

The C++ standard library already has this exact shape: `std::map` (a balanced tree) for the price levels, `std::list` (a doubly linked list) per level, and `std::unordered_map` (a hash map) for the ID index. So v1 can be built from standard containers and be correct. v3 then replaces each one with a faster version and measures the difference (see [`../application/ROADMAP.md`](../application/ROADMAP.md)).

#### Check yourself (topic 1)

<details><summary>1. Why is an unsorted array a bad order book, even though inserting is O(1)?</summary>

Finding the best price and the oldest order at it needs a scan of every order, O(n), on every incoming message.
</details>

<details><summary>2. In a sorted array, why is cancel still O(n)?</summary>

The array is sorted by price, but a cancel carries only an ID, so finding the order is a scan. Removing it from the middle then shifts the later elements.
</details>

<details><summary>3. Why do price levels help so much?</summary>

Many orders share a price, so the number of levels L is much smaller than the number of orders n. Sorting levels instead of orders makes the costs depend on L.
</details>

<details><summary>4. Why a doubly linked list and not a singly linked one?</summary>

To unlink a node in O(1) we must update the node before it. With only `next` pointers the node does not know its predecessor, so we would have to walk from the head.
</details>

<details><summary>5. What does the hash map store, and why does each order keep a pointer to its level?</summary>

It stores a pointer to the order's node in its list, so cancel jumps straight to it. The level pointer lets the engine fix the level's head or tail after unlinking, and remove the level if it becomes empty.
</details>

<details><summary>6. A resting order is partly filled. Does it lose its place in the line?</summary>

No. Only its quantity changes; it stays at the head with its time priority.
</details>

### Topics 2 to 5

To be added as they are worked through.

## Decisions

- None yet. The design above is the shape of the order book; the concrete container for price levels is chosen in step 07 and measured in v3.

## Open questions

- Which container holds the price levels: sorted array, balanced tree (`std::map`), or an array indexed directly by price? Decided in step 07, measured in v3.

## Next step

→ Topic 2 of this step: latency.
