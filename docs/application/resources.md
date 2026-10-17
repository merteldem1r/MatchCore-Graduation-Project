# Resources

> Research material, similar projects and references. Status: **to evaluate in step 05**. Nothing here has been reviewed yet. When a source is used in a document, cite it with a number like [1], following the thesis writing guide.

## Similar projects / architectures

| Resource | Why it's relevant | Status |
| --- | --- | --- |
| LMAX Architecture: Martin Fowler, 2011, <https://martinfowler.com/articles/lmax.html> | Famous single-threaded, event-sourced exchange design | To read |
| LMAX Disruptor: <https://github.com/LMAX-Exchange/disruptor> | Low-latency inter-thread messaging; technical paper included | To read |
| liquibook: open-source C++ order book library | A real C++ matching implementation to compare against | To find & review |
| `matchcore`: Rust crate, <https://docs.rs/matchcore> | Same name as ours; deterministic, single-threaded, LMAX-inspired price-time engine. Compare and cite | To review |
| "How to Build a Fast Limit Order Book": WK Selph, 2011 (blog post) | Widely cited write-up on order book data structures | To find |

## Specifications & data

| Resource | Why it's relevant | Status |
| --- | --- | --- |
| NASDAQ TotalView-ITCH 5.0 specification | Real market-data message format (add/cancel/execute) | To read |
| Public ITCH sample files (license to be checked) | Realistic workloads, if their license allows use | To check |

## Books & papers

| Resource | Why it's relevant | Status |
| --- | --- | --- |
| Harris, L., 2003, *Trading and Exchanges: Market Microstructure for Practitioners*, Oxford University Press | How exchanges and order types work | To read |
| Gould, M. D. et al., 2013, "Limit order books", *Quantitative Finance* | Academic survey of limit order books | To read |
| Kleppmann, M., 2017, *Designing Data-Intensive Applications*, O'Reilly | Event logs, replication, fault tolerance | To read |
| Ongaro, D. and Ousterhout, J., 2014, "In Search of an Understandable Consensus Algorithm (Raft)", USENIX ATC, <https://raft.github.io> | Raft (optional part) | Later |
| Cormen, T. H. et al., 2009, *Introduction to Algorithms*, MIT Press | Trees, hashing, skip lists background | Reference |

## Measurement

| Resource | Why it's relevant | Status |
| --- | --- | --- |
| HdrHistogram: <https://github.com/HdrHistogram/HdrHistogram> | Accurate latency percentiles | To read |
| Gil Tene, "How NOT to Measure Latency" (talk) | Coordinated omission, measuring percentiles correctly | To watch |
