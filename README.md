# Exact Vector Search

A standalone project exploring exact vector similarity search with Rust, PTX, and GPU performance analysis.

**Status: project definition.** This repository contains the project brief. There is no runnable implementation or benchmark result yet.

## What this project will produce

A command line program that loads a collection of vectors, accepts query vectors, and returns the IDs and distances of the nearest matches.

For semantic search, each vector represents a document or text passage. An embedding model produces these vectors outside the search engine. The first version will use precomputed embeddings and will not train or serve an embedding model.

## First version

* Search every database vector and return the nearest `k` results.
* Use squared Euclidean distance: `distance(q, x) = sum((q[i] - x[i]) * (q[i] - x[i]))`.
* Support one query and batches of queries.
* Validate vector dimensions, finite values, and `k` before searching.
* Return stable vector IDs and distances, with a deterministic tie rule.
* Provide a Rust CPU implementation before attempting GPU acceleration.

Exact search means an exhaustive search without an approximate index. It does not mean exact real number arithmetic: floating point tolerances and near ties will be documented and checked.

## Why Rust, PTX, and SASS

| Layer | Role |
| --- | --- |
| Rust on the CPU | Load vectors, validate inputs, provide a reference implementation, manage resources, and report results. |
| PTX on the GPU | Implement a selected distance computation over many vectors and queries in parallel. |
| Generated SASS | Inspect the native instructions when profiling indicates a question about memory access, register use, or instruction selection. |

The first GPU experiment can accelerate distance computation while keeping result selection on the CPU. Those costs must be reported separately and together. A later GPU selection kernel will only be considered if measurement justifies it.

Rust will launch GPU work through the CUDA driver. The Rust compiler does not prove the correctness or memory safety of a handwritten PTX kernel.

## What the benchmarks must show

* Correctness against a trusted CPU reference, including distance tolerances and behavior around ties.
* Database size, vector dimension, query batch size, and `k`.
* Hardware, operating system, compiler, driver, and build settings.
* Latency, throughput, memory use, warmup procedure, and repeated measurements.
* End to end timings that include allocation, data transfer, launch, synchronization, and result selection.
* Separate timings for repeated searches with database vectors already resident on the GPU.
* Comparison with an established implementation such as NVIDIA cuVS where the same workload and configuration are supported.

GPU acceleration is a hypothesis to test. Small collections or single queries may favor the CPU. All reported comparisons must state the metric, data type, residency assumptions, and measurement boundaries.

## Planned milestones

1. Define the input format, output format, and deterministic result ordering.
2. Implement and verify the Rust CPU search.
3. Add a reproducible benchmark dataset and measurement harness.
4. Implement one PTX distance kernel and verify its numerical results.
5. Compare CPU and GPU behavior across representative workloads.
6. Use profiling and SASS inspection to investigate observed bottlenecks.
7. Publish reproducible commands, measured results, and limitations.

Only the project definition is complete. Implementation milestones will be scoped and reviewed one at a time.

## Portfolio

This project belongs to [AI Systems and HPC](https://github.com/AG4MA/ai-research-portfolio/tree/main/AI%20Systems%20and%20HPC) in the [AG4MA AI Research Portfolio](https://github.com/AG4MA/ai-research-portfolio).

## References

* [NVIDIA cuVS: vector search](https://docs.nvidia.com/cuvs/getting-started/introduction/vector-search)
* [NVIDIA cuVS: exact brute force search](https://docs.nvidia.com/cuvs/user-guide/api-guides/indexing-guide/brute-force)
* [NVIDIA PTX ISA](https://docs.nvidia.com/cuda/parallel-thread-execution/index.html)
* [cudarc: CUDA driver integration in Rust](https://docs.rs/cudarc/latest/cudarc/driver/index.html)
