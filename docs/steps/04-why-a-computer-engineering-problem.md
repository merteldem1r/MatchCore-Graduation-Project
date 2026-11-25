# 04 · Why This Is a Computer Engineering Problem

> **Status:** in progress (topics 1 and 2 of 5 done) · **Started:** 2026-11-23 · **Interactive version (with an array comparison, a live order book, a percentile explorer and a queue simulator):** [interactive/04-why-a-computer-engineering-problem.html](interactive/04-why-a-computer-engineering-problem.html)

## Goal of this step

Understand what makes a matching engine hard, and which computer engineering topic each difficulty belongs to. This is the "why it matters" part: the advisor and the jury will ask it, and it becomes the background chapter of the thesis.

The step is split into five topics, worked through one at a time:

| # | Topic | Question | Status |
| --- | --- | --- | --- |
| 1 | Data structures | How does the engine keep the order book so that every operation is fast? | Done |
| 2 | Latency | What does "fast" mean here, and where does the time actually go? | Done |
| 3 | Determinism | Why must the same input always give the same trades, and what can quietly break that? | Next |
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

### Topic 2: Latency

In topic 1 we counted **steps** (O(1), O(n), "looked at 4, shifted 3"). But the advisor, the jury and the benchmarks will ask how much **time** something takes. This topic connects the two, and shows why they don't always agree.

#### 2.0 Start here: the words this topic uses

Read this table first; every later section uses these words.

**Time**

| Word | Meaning in plain words |
| --- | --- |
| **Latency** | How long **one** order takes: from the moment it reaches the engine until the engine has finished reacting to it (trades found, book updated, events written out). |
| **Throughput** | How **many** orders the engine handles per second. |
| **ms, µs, ns** | Millisecond = 1/1,000 s. Microsecond = 1/1,000,000 s. Nanosecond = 1/1,000,000,000 s. A blink is about 100 ms; a web request is tens of ms; a matching engine works in µs; one memory read costs ns. |

**Describing many measurements**

| Word | Meaning in plain words |
| --- | --- |
| **Sample** | One measurement, for example "order 17 took 2 µs". A benchmark collects millions of samples. |
| **Average (mean)** | Add all samples and divide by how many there are. Easily pulled up by a few very slow samples. |
| **Percentile** | Sort all samples from fastest to slowest. The **p-th percentile** is the value that p% of the samples are at or below. |
| **p50 (median)** | Half of the orders took this long or less. The "normal" case. |
| **p90, p99, p99.9** | 90%, 99% or 99.9% of the orders took this long or less. So p99 = 3 µs means "99 out of every 100 orders took 3 µs or less; 1 in 100 took longer". |
| **max** | The single slowest order. |
| **Tail latency** | The slow end of the sorted list (p99, p99.9, max): the rare slow orders. |

**Waiting**

| Word | Meaning in plain words |
| --- | --- |
| **Queue** | Orders waiting because the engine is still busy with an earlier one. |
| **Burst** | Many orders arriving at almost the same moment (market open, news). |
| **Utilisation** | How busy the engine is: 100% means it never has a free moment. |
| **Headroom** | Spare capacity: the difference between the engine's maximum throughput and the real traffic. |

**Hardware**

| Word | Meaning in plain words |
| --- | --- |
| **RAM (main memory)** | Where the program's data lives. Large, but slow to reach from the CPU (around 100 ns). |
| **Cache (L1, L2)** | Small, fast memory inside the CPU that keeps copies of recently used data. L1 is the smallest and fastest. |
| **Cache hit / miss** | Hit: the data was already in the cache (fast). Miss: it had to come from RAM (slow). |
| **Cache line** | The CPU never fetches one value; it fetches a whole block around it. That block is a cache line (128 bytes on Apple M2, 64 bytes on most other CPUs). |
| **Prefetching** | The CPU notices that memory is read in order and fetches the next cache lines before they are asked for. It helps arrays, not linked lists. |

**Hidden costs and measuring**

| Word | Meaning in plain words |
| --- | --- |
| **Critical path** | The code that runs for every order. Anything slow here slows every order. |
| **Allocation** | Asking for new memory (`new` in C++). Usually fast, sometimes very slow. |
| **Memory pool** | A big block of memory taken once at startup and handed out piece by piece, so the engine never has to allocate during trading. |
| **Garbage collection (GC)** | In languages such as Go and Java, the runtime sometimes pauses the program to clean up unused memory. C++ has no GC. |
| **Warm-up** | Running the workload for a while before measuring, so caches and the CPU have settled. |
| **Release / debug build** | Release = compiled with optimisation (`-O2`), the real speed. Debug = no optimisation, much slower; useless for benchmarks. |
| **Histogram** | Counters for ranges of values ("how many orders took 0 to 50 ns, 50 to 100 ns, ..."). Percentiles can be read from it at the end. |
| **Workload** | The list of test orders fed to the engine in a benchmark. |
| **Seed** | The starting number of a random generator. The same seed always produces the same "random" workload. |

#### 2.1 Latency and throughput

```text
order arrives --> [ engine works ] --> events out
      t0                                   t1
               latency = t1 - t0
```

An analogy: a supermarket with **one cashier**. Latency is how long it takes to serve one customer; throughput is how many customers are served per hour. Our engine is that single cashier: it handles one order at a time on one thread (topic 5 explains why).

With one cashier the two are tied together. If the engine needs **2 µs per order**, its maximum throughput is 1,000,000 / 2 = **500,000 orders per second**.

But a customer's real waiting time is their own service time **plus the time spent behind others in the queue**. Suppose orders arrive at exactly 500,000 per second (100% busy) and then a **burst of 10 extra orders** arrives at once:

- the 10th order of the burst first waits for the 9 in front of it: 9 x 2 µs = 18 µs, then needs its own 2 µs;
- the engine has no free moment to catch up, so the queue **never shrinks again**, and every later order inherits the delay.

If the engine were only 50% busy, the same burst would be cleared within a few tens of microseconds. **Lesson:** an engine running close to its limit has terrible latency as soon as traffic is bursty, and real markets are bursty. A faster engine is valuable even when it is "fast enough on average", because it leaves **headroom**.

#### 2.2 Why an average lies: percentiles and tail latency

Ten orders were measured, in µs:

```text
2   2   3   2   2   3   2   2   2   180
```

The average is 200 / 10 = **20 µs**, yet **no order took 20 µs**: nine took 2 to 3 µs and one took 180 µs. The average makes the normal case look 10 times worse than it is, and it hides the slow order.

Sorting the samples and reading off positions gives an honest picture:

```text
sorted:  2  2  2  2  2  2  2  3  3  180
         |-------- 90% --------|    |-- slowest 10%
```

| Measure | Meaning | Value |
| --- | --- | --- |
| p50 (median) | half of the orders took this long or less | 2 µs |
| p90 | 90% took this long or less | 3 µs |
| max | the slowest order | 180 µs |

"Normally 2 µs, but sometimes up to 180 µs." The slow end is the **tail**. (Here the percentile is the value at position *p% x N*, rounded up, in the sorted list: p90 of 10 samples is the 9th value.)

Why the tail matters for a matching engine:

- **Volume.** With 1,000,000 orders a day, p99 describes the slowest 10,000 orders and p99.9 the slowest 1,000. "Rare" still means thousands of real orders.
- **Who suffers.** In a market, being slow means someone else trades first.
- **When.** Slow orders tend to happen during bursts, exactly when the market is busiest.

That is why the advisor asked for **p50, p99, p99.9 and max** instead of an average, and why the v2 benchmarks report exactly those.

#### 2.3 Where the time goes: memory

Why would one order take 180 µs when the others take 2 µs? There are three kinds of "slow":

1. **Outside the engine: the network.** Real, but not what we measure. Our latency starts when the order reaches the engine; in benchmarks the orders come straight from memory.
2. **More work: the algorithm.** A big order that sweeps many price levels really does more trades. This is expected and easy to explain; the advisor's "aggressive market orders" workload measures it.
3. **The same work, but the machine was slow.** Things you cannot see in the code. The biggest one is memory.

The CPU keeps small, fast copies of data close to itself:

| Where the data is | Rough time to read it | On the student's Apple M2 (read with `sysctl`) |
| --- | --- | --- |
| CPU register | under 1 ns | |
| L1 cache | about 1 ns | 128 KB per performance core |
| L2 cache | about 5 ns | 16 MB |
| RAM | **about 100 ns** | 16 GB |

So a read from RAM is about **100 times slower** than a read from L1. An order that needs 20 memory reads takes about 20 ns if everything is in L1, and about 2 µs if everything comes from RAM: same code, same work, 100 times different.

**Cache lines favour arrays.** The CPU fetches a whole cache line (128 bytes on the M2). If an order takes 32 bytes, one line holds 4 orders:

```text
ARRAY: orders next to each other
  [ A | D | B | C ][ E | F | G | H ]      <- 2 cache lines
  reading A also brings D, B, C for free: 8 orders = 2 trips to RAM

LINKED LIST: nodes scattered in memory
  [A] ....... [D] ................ [B] ...... [C] ...
  every "next" may land somewhere new: 8 orders = up to 8 trips to RAM
```

This is the second catch of topic 1, now with numbers. Compare cancelling an order in a price level, once the hash map has said which level it is in:

| Level size | Sorted array (scan + shift) | Doubly linked list (unlink) | Likely faster |
| --- | --- | --- | --- |
| 5 orders | 160 bytes: 2 neighbouring cache lines | 3 scattered cache lines (the node and both neighbours) | probably the array |
| 5,000 orders | 160 KB: about 1,250 cache lines, thousands of moves | still 3 cache lines | the list, easily |

**Big-O tells who wins when things get big; only measurement tells where "big" starts.** The crossover depends on the CPU, the order size and how many orders sit at one price. That connects to the advisor's **narrow vs wide price range** workloads: a narrow range puts many orders on each level (favours the list), a wide range puts few (may favour the array). The v3 design also tries to get both benefits, for example by taking all list nodes from one contiguous memory pool.

#### 2.4 Hidden costs

| Hidden cause | What happens | Size | Fix |
| --- | --- | --- | --- |
| Cache misses | data has to come from RAM | about 100x an L1 read | compact data, arrays where they win, pools |
| Hash map growth | when the map is full, one insert moves every entry into a bigger table | one insert becomes O(n) | reserve the full size at startup (`reserve`) |
| Allocation | `new` is usually fast but sometimes asks the operating system for memory | ns, suddenly µs | allocate once at startup (memory pool) |
| The operating system | our thread is paused to run something else | µs to ms | fewer background programs; measure many samples |
| Garbage collection | Go or Java pause the program to clean memory | µs to ms | a language without GC (C++) for the engine, weighed in step 06 |
| I/O on the critical path | printing or writing a log inside the matching code | µs to ms | keep logging and the dashboard off the critical path |

The general rule: **pay every expensive cost up front**, before the market opens, when nobody is waiting.

A related option for the order-ID index: if the engine gives each order a simple counter as its ID (0, 1, 2, ...), the index can be a plain array where the ID is the position. No hashing, no growth, and nearby IDs sit near each other in memory. This is the advisor's "hash table vs direct index" research question, to be measured in v3.

#### 2.5 Measuring honestly

The basic idea:

```text
for each order in the workload:
    t0 = clock.now()
    engine.process(order)
    t1 = clock.now()
    histogram.record(t1 - t0)      // store it, don't print it here
report p50, p99, p99.9, max
```

In C++ the clock is `std::chrono::steady_clock`. What can make the number wrong was checked with a small throw-away C++ program on the student's Apple M2 (2026-11-25; the program is not part of the repository):

| # | Problem | Measured on the M2 | Fix |
| --- | --- | --- | --- |
| 1 | The clock is coarser than what you measure | the timer moves in steps of **41.67 ns**; one clock read costs about **18 ns** | time whole operations; repeat tiny ones 1,000 times and divide |
| 2 | Even "nothing" has a tail | timing an empty region 1,000,000 times: p50 = 0 ns, p99 = 42 ns, p99.9 = 83 ns, **max = 1,459 ns** | millions of samples, report percentiles, close other programs, record which machine |
| 3 | The first run is slow | summing a 64 MB array: run 1 = **6,579 µs**, run 2 = 1,244 µs, run 3 = 1,258 µs | warm up before measuring |
| 4 | Measuring the wrong build | the same loop without optimisation (`-O0`): about **66,000 µs**, about 50 times slower | always benchmark a release build (`-O2`); make sure the compiler does not delete unused work (Google Benchmark's `DoNotOptimize`) |
| 5 | The measurement disturbs the result | printing each sample puts I/O on the critical path | record into an in-memory histogram (HdrHistogram) |
| 6 | Hiding the queue | if the next order is sent only after the previous one finished, queueing never shows up ("coordinated omission") | also test orders arriving on a schedule or in bursts, measured from when they should have arrived |
| 7 | Different input every run | two runs are not comparable | generate the workload from a fixed seed; requires a deterministic engine (topic 3) |

On problem 2: the empty region sometimes took 1.5 µs because the operating system and the hardware also need the CPU. Every measurement carries this background noise. The M2 also has two kinds of cores (performance and efficiency), so the same code can run at different speeds depending on where the operating system places it.

These seven fixes become the benchmark rules for v2:

| # | Rule |
| --- | --- |
| 1 | Time whole operations; repeat tiny ones and divide |
| 2 | Millions of samples; report p50 / p99 / p99.9 / max, never just the average |
| 3 | Warm up before measuring |
| 4 | Release build, and make sure the work is not optimised away |
| 5 | Record into an in-memory histogram; no I/O inside the measured loop |
| 6 | Test back-to-back and scheduled or bursty arrivals |
| 7 | The same seeded workload for every run and every version; record the machine |

#### 2.6 Summary of topic 2

- **Latency** is the time for one order; **throughput** is orders per second. A busy engine needs **headroom** for bursts.
- **Averages lie**: report **percentiles** and watch the **tail**.
- Most of the time goes to **memory**: a cache miss costs about 100 times an L1 read, and **cache lines** favour arrays. Big-O alone does not decide the winner.
- **Hidden costs** (hash map growth, allocation, the OS, GC, I/O) are fixed by **paying up front** and keeping the critical path clean.
- **Measuring honestly** follows the seven rules above.

#### Check yourself (topic 2)

<details><summary>1. An engine needs 2 µs per order on one thread. What is its maximum throughput?</summary>

1,000,000 / 2 = 500,000 orders per second.
</details>

<details><summary>2. Why does an engine that is 100% busy behave badly during a burst?</summary>

It has no free moment to work through the extra orders, so the queue never shrinks and every later order inherits the delay.
</details>

<details><summary>3. Latencies 2, 2, 3, 2, 2, 3, 2, 2, 2, 180 µs: what are the average, the p50 and the max?</summary>

Average 20 µs, p50 2 µs, max 180 µs. The average describes none of the orders.
</details>

<details><summary>4. Out of 1,000,000 orders, p99.9 = 8 µs. What does that tell you?</summary>

999,000 orders took 8 µs or less; the slowest 1,000 took longer than 8 µs.
</details>

<details><summary>5. Why can a linked list be slower than an array, even though it does fewer steps?</summary>

Its nodes are scattered in memory, so each step can be a cache miss of about 100 ns. An array's elements share cache lines and the CPU prefetches them.
</details>

<details><summary>6. How do we avoid a slow hash map growth while the engine is trading?</summary>

Reserve the full size at startup, or use a direct-index array if the engine assigns sequential IDs.
</details>

<details><summary>7. Why can a single hash map lookup not be timed on its own on the M2?</summary>

The timer moves in steps of 41.67 ns and reading it costs about 18 ns, while the lookup takes only a few ns. Time many lookups and divide.
</details>

<details><summary>8. Why warm up, and why never benchmark a debug build?</summary>

The first run is slower (caches and CPU not settled; 5 times slower in our test). A debug build was about 50 times slower than the release build, so it says nothing about the real engine.
</details>

### Topics 3 to 5

To be added as they are worked through.

## Decisions

- None yet. The design above is the shape of the order book; the concrete container for price levels is chosen in step 07 and measured in v3.

## Open questions

- Which container holds the price levels: sorted array, balanced tree (`std::map`), or an array indexed directly by price? Decided in step 07, measured in v3.
- Order-ID index: a hash map (works with any ID) or a direct-index array (needs engine-assigned sequential IDs)? Decided in step 07, measured in v3.
- The seven measuring rules in 2.5 are the proposed benchmark rules; they are confirmed when v2 is designed.

## Next step

→ Topic 3 of this step: determinism.
