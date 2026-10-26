# Resources

> The core references, kept short on purpose: only sources that match the project closely. They are cited in the proposal form and will be cited again in the interim report and thesis, as [1], [2], [3], following the thesis writing guide. Add new sources only when a document actually uses them.

| # | Reference | Why it matters for MatchCore | Status |
| --- | --- | --- | --- |
| [1] | Gould, M. D., Porter, M. A., Williams, S., McDonald, M., Fenn, D. J. and Howison, S. D. (2013). Limit order books. *Quantitative Finance*, 13(11), 1709–1742. Preprint: <https://arxiv.org/abs/1012.0349> | Academic survey of limit order books and price-time priority: the model our engine implements | Cited in proposal; to read |
| [2] | Fowler, M. (2011). The LMAX Architecture. <https://martinfowler.com/articles/lmax.html> | A real exchange built as a single-threaded, in-memory engine; every input is logged and replayed to recover after a crash, which is our design for determinism and recovery | Cited in proposal; to read |
| [3] | Liquibook: open-source C++ order matching engine. GitHub repository, <https://github.com/enewhuis/liquibook> | A working C++ matching engine (limit, market, cancel, cancel-replace, depth book) to compare our design and benchmarks against. Header-only; "liberal license" per its README, exact license to be checked before reusing any code | Cited in proposal; to review |
