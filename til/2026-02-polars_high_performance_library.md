## TIL: Polars — a High-Performance DataFrame Library

**Polars** is a DataFrame library designed for high-performance data processing, built to take full advantage of modern hardware architectures.

Implemented in **Rust** and based on **Apache Arrow**, Polars combines memory efficiency, safety, and speed while providing a familiar DataFrame-style API.

### Key design principles

- **Columnar data layout**  
  Data is stored by column rather than by row, enabling efficient analytical operations such as filtering, aggregation, and grouping. This layout improves cache locality and reduces unnecessary memory access.

- **Vectorized execution**  
  Operations are applied to entire arrays at once, allowing the use of SIMD instructions and reducing per-element overhead.

- **Cache-efficient algorithms**  
  By operating on contiguous blocks of homogeneous data, Polars minimizes cache misses and aligns well with CPU cache hierarchies.

- **Lazy execution engine**  
  Polars supports lazy evaluation, allowing query plans to be optimized before execution through techniques such as predicate pushdown and projection pruning.

  obs.:
  In Shiny: "Lazy” means deferring execution. The focus is on UX and responsiveness
  
  In Polars / DuckDB: “Lazy” means building an execution plan. The focus is on optimization and performance

- **Automatic parallelism**  
  Many operations are executed in parallel by default, without requiring explicit configuration.

### Practical advantages

Compared to traditional eager, row-oriented DataFrame implementations, Polars typically offers:
- lower memory usage,
- faster aggregations and joins,
- more predictable performance on large datasets.

Polars integrates well with modern data formats, including **Parquet**, **Arrow IPC**, and **CSV**, and is available for both **Python** and **Rust**.

### Summary

Polars aligns data layout, execution strategy, and hardware characteristics, making it a strong choice for analytical workloads where performance and memory efficiency are critical.
