# 02 · How an Exchange Works

> **Status:** done · **Date:** 2026-10-17 · **Interactive version (with an order-book simulator):** [interactive/02-how-an-exchange-works.html](interactive/02-how-an-exchange-works.html)

## Goal of this step

Build a clear picture of the problem before any code or tech stack: what an order is, how an order book is arranged, which rules decide who trades with whom, and where a matching engine fits in a real exchange.

## Content

### 1. What is an exchange?

An **exchange** is a marketplace where buyers and sellers of the same thing meet: shares of a company (e.g. `THYAO` on Borsa İstanbul, `AAPL` on NASDAQ), currencies, futures, crypto. Each tradeable thing is called an **instrument** or **symbol**.

People don't negotiate with each other directly. Everybody sends their wishes to the exchange ("I'll buy at this price", "I'll sell at that price"). The exchange keeps a list of all the wishes that haven't been satisfied yet, and when a buyer and a seller agree on price, it makes them trade.

The program at the center of the exchange that does this pairing is the **matching engine**. Everything else (websites, broker apps, charts) is built around it.

- **Who sends orders?** Ordinary investors go through a **broker** (a bank or brokerage app), which forwards their orders to the exchange. Large firms (funds, market makers, HFT firms) connect directly, over low-latency connections.
- **Market makers** always keep both a buy and a sell order in the book, so anyone who wants to trade has someone to trade with. They earn the small gap between the two prices, which is why speed matters so much to them.

> **Key idea:** there is one order book per symbol. A real engine handles thousands of symbols, but each symbol's book is independent. Our v1 can start with **a single symbol**.

### 2. What is an order?

```text
order_id   1042
symbol     THYAO
side       BUY
type       LIMIT
price      50.00
quantity   100
timestamp  09:00:01.000123
```

This means: *"Buy 100 THYAO, and pay **at most** ₺50.00 per share."*

- **side**: buy or sell.
- **price**: for a buy, the *maximum* the trader will pay; for a sell, the *minimum* the trader will accept.
- **quantity**: how many shares.
- **timestamp / sequence number**: when the exchange received the order. It decides who goes first when two orders have the same price.

#### The messages a trader can send

| Message | Meaning | In v1? |
| --- | --- | --- |
| **Limit order** | "Trade at this price or better." If it can't trade right away, it **waits in the book** (it *rests*). | ✅ v1 |
| **Market order** | "Trade right now at whatever price is available." No price given. It takes the best available prices until it is filled. Whatever can't be filled is cancelled; it never rests. | ✅ v1 |
| **Cancel** | "Remove my resting order #1042 from the book." | ✅ v1 |
| **Modify / cancel-replace** | "Change my order's price or quantity." Usually treated as cancel + new order, so the order **loses its place in the line**. (Many exchanges let a pure quantity *decrease* keep its place.) | Soon after v1 |
| **Time-in-force** (IOC, FOK, GTC…) | Extra rules about how long an order lives. IOC = fill what you can immediately, cancel the rest. FOK = fill everything immediately or nothing. GTC = stays until cancelled. | Later |
| Stop orders, icebergs, auctions | Advanced features of real exchanges. | Out of scope |

### 3. The order book

Example book (the same one used in every example below):

| Side | Price | Qty | Order (arrival order) |
| --- | ---: | ---: | --- |
| Ask | 50.30 | 80 | #6 (4th) |
| Ask | 50.20 | 300 | #4 (1st) |
| Ask | **50.10** | 50 | #5 (2nd) ← **best ask** |
| | | | *spread = 0.10* |
| Bid | **50.00** | 100 | #1 (1st) ← **best bid** |
| Bid | 50.00 | 40 | #3 (3rd) |
| Bid | 49.90 | 200 | #2 (2nd) |

- **Bids** (buyers) are sorted from the **highest** price down. The top one is the **best bid**.
- **Asks** (sellers) are sorted from the **lowest** price up. The top one is the **best ask**.
- The gap between best bid and best ask is the **spread**. In a normal resting book, best bid < best ask. If they overlapped, those orders would already have traded.
- All orders at the same price form a **price level**. Inside a level, orders wait in a **FIFO queue** (first in, first out), like a line at a bank. Level 50.00 holds #1, then #3.

> **Key idea:** this is where data structures come in. We need
> 1. both sides **sorted by price**, with fast access to the best price;
> 2. a **FIFO queue** inside each price level;
> 3. a fast way to **find an order by its id** when it is cancelled.
>
> These are exactly the three parts the advisor wants compared.

**Tick size.** Prices can't take any value. They move in fixed steps called **ticks** (e.g. ₺0.01). Engines therefore store a price as an **integer number of ticks** (50.00 → 5000) instead of a floating-point number. That avoids rounding bugs, and it is what makes the "array of price levels" data structure possible later.

### 4. The matching rules

1. **When do two orders trade?** When a buyer's price ≥ a seller's price. The orders are said to **cross**. A new buy at 50.10 crosses the ask at 50.10; a buy at 50.05 does not.
2. **Price priority.** A new order always trades against the **best** price on the other side first. A buyer gets the cheapest seller; a seller gets the highest buyer.
3. **Time priority.** At the same price, the order that **arrived first** trades first (FIFO). Rules 2 + 3 together = **price-time priority**.
4. **Trade price.** The trade happens at the price of the order that was **already resting** in the book (the **maker**). The newcomer (the **taker**) may get a better price than it asked for.
5. **Partial fills.** Quantities don't have to match. Buying 120 when the best seller has 50 means trading 50, then continuing with the next seller. Whatever is left of a limit order rests in the book.
6. **Determinism.** Given the same orders in the same sequence, the engine must produce **exactly** the same trades every time. No randomness and no dependence on timing. This is what makes replay-based recovery possible.

### 5. Worked examples

All examples start from the book in section 3.

#### Example A: no trade

`BUY LIMIT 30 @ 50.05`. Since 50.05 < best ask 50.10, nothing crosses. The order **rests** as the new best bid at 50.05, and the spread shrinks to 0.05. No trade happens, but the order is still useful because it adds **liquidity**.

#### Example B: time priority inside a level

`SELL LIMIT 120 @ 49.90`

1. Best bid 50.00 ≥ 49.90 → cross. At 50.00, #1 arrived first → **TRADE 100 @ 50.00** with #1 (#1 fully filled).
2. 20 left. Next in line at 50.00 is #3 → **TRADE 20 @ 50.00**. #3 keeps 20 shares.
3. The seller is fully filled. It asked for ≥ 49.90 but got 50.00: **price improvement**, because trades happen at the resting order's price.

| Price | Qty | Order |
| ---: | --- | --- |
| 50.00 | ~~100~~ → 0 | #1 fully filled, removed |
| 50.00 | 40 → 20 | #3 partially filled |
| 49.90 | 200 | #2 untouched |

#### Example C: a market order walks the book

`BUY MARKET 200`

1. Best ask 50.10 → **TRADE 50 @ 50.10** with #5.
2. Next level 50.20 → **TRADE 150 @ 50.20** with #4 (150 left on #4).
3. Average price paid ≈ 50.175. A large market order **moves the price**, which is why traders worry about *market impact*.

#### Example D: cancel and modify

- **CANCEL #1**: the engine must find #1 quickly and remove it from the *middle* of the 50.00 queue; #3 becomes first in line. Doing this in O(1) is why an *intrusive list* plus an *order-id hash map* matter.
- **MODIFY #3 → price 50.10**: treated as cancel #3 + new order at 50.10, so it goes to the **back of the line** at its new price. In this book it would actually cross the ask at 50.10 and trade.

### 6. What the engine outputs: events

For every input, the engine produces a list of **events**:

```text
IN  NEW #7 SELL LIMIT 120 @ 49.90
OUT ACCEPTED #7
OUT TRADE 100 @ 50.00  buyer=#1 seller=#7
OUT TRADE 20 @ 50.00   buyer=#3 seller=#7
OUT FILLED #7
```

If every **input** is written down in order (the **event log**), the whole book can be rebuilt later by feeding the same inputs into the engine again. That is **deterministic replay**, the fault-tolerance part of the project. Saving the whole book every now and then (a **snapshot**) means only the inputs after the last snapshot have to be replayed, not everything from the beginning.

### 7. Where the matching engine sits in a real exchange

```text
Traders / brokers → Gateway → Risk checks → Sequencer → [ MATCHING ENGINE ] → Outputs
 send orders        network,   enough money?  one strict    order book +         trade reports,
                    protocols  sane price?    order of all  matching             market data feed,
                    (FIX,OUCH)                inputs                             dashboards
```

A real exchange is a whole system. **Our project is the matching-engine box**, plus a simple way to feed it orders (a file or a generator), plus the event log for recovery, plus a separate dashboard that reads the outputs.

The **sequencer** is worth noticing: the engine is usually **single-threaded per order book**. Orders are processed one at a time, in one fixed order. That sounds slow, but it is simple and deterministic, and with good data structures it can process millions of orders per second. The well-known LMAX architecture is built on this idea (to be read in step 05).

### 8. What this means for our version 1 (a sketch, not final)

- One symbol, one order book, single-threaded.
- Limit orders, market orders, cancel.
- Price-time priority with partial fills.
- Prices as integer ticks.
- Input: a simple text file of orders. Output: printed events.
- The simplest data structures first (e.g. `std::map` + `std::list` + `std::unordered_map` in C++), just to be correct.

Every later feature (faster data structures, benchmarks, event log, snapshots, dashboard, maybe Raft) is an **upgrade on top of this**, and each upgrade is measured against the previous version. That "v1 → v2 → v3, measured every time" story is also a good thesis structure.

### Glossary

| Term | Meaning |
| --- | --- |
| Instrument / symbol | The thing being traded, e.g. THYAO. |
| Bid / ask | A buy order / a sell order. "Ask" is also called "offer". |
| Best bid / best ask | Highest buy price / lowest sell price in the book. Together: the *top of book*. |
| Spread | Best ask − best bid. |
| Price level | All resting orders at one price, kept in a FIFO queue. |
| Depth | How much quantity sits at each level, e.g. "top 5 levels". Dashboards show this. |
| Tick | Smallest allowed price step. |
| Resting order | An order waiting in the book. |
| Maker / taker | Maker = the resting order that provided liquidity. Taker = the incoming order that removed it. |
| Cross | When a buy price ≥ a sell price, so they can trade. |
| Fill / partial fill | An order fully / partly executed. |
| Liquidity | How much can be traded without moving the price a lot. |
| Price-time priority | Best price first; at the same price, earliest first. Also called FIFO matching. |
| Event log | The ordered record of all inputs, used to replay and recover. |
| Deterministic | Same inputs in the same sequence → identical outputs, always. |

### Check yourself

<details><summary>1. Best bid 50.00, best ask 50.10. A BUY LIMIT 100 @ 50.05 arrives. What happens?</summary>

No trade (50.05 < 50.10). It rests as the new best bid; the spread becomes 0.05.
</details>

<details><summary>2. Two buys at 50.00: #1 (first) for 100, #3 (later) for 40. A SELL 60 @ 50.00 arrives. Who trades?</summary>

Only #1, for 60 shares (time priority). #1 keeps 40; #3 is untouched.
</details>

<details><summary>3. A BUY LIMIT 50 @ 51.00 arrives; the best ask is 50.10 with 50 shares. At what price is the trade?</summary>

50.10, the resting (maker) order's price. The buyer was willing to pay 51.00 but gets price improvement.
</details>

<details><summary>4. A market order can't be fully filled because the book runs out. What happens to the rest?</summary>

It is cancelled. Market orders never rest, because they have no price to rest at.
</details>

<details><summary>5. Why store prices as integers instead of floats?</summary>

Floats can't represent many decimals exactly (0.1 + 0.2 ≠ 0.3), which breaks price comparisons. Integer ticks are exact and also allow indexing price levels in an array.
</details>

<details><summary>6. Why must the engine be deterministic?</summary>

So that replaying the event log after a crash rebuilds exactly the same book, and so that tests and benchmarks are reproducible.
</details>

## Decisions

- v1 handles **one symbol**, **limit + market + cancel**, **price-time priority**, **integer tick prices**, **single-threaded**. (To be confirmed in step 07.)

## Open questions

- Which time-in-force types (IOC / FOK / GTC) to support, and when?
- Should *modify* that only reduces quantity keep time priority (like many real exchanges)?

## Next step

→ [03 · Project name & repository structure](03-name-and-repo-structure.md)
