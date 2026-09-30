# MPI Parallel Programming

Two C++/MPI exercises that demonstrate distributed data processing with different communication patterns:

1. **parallel image histogram aggregation**; and
2. **distributed word-frequency counting**.

The project focuses on splitting work across processes, exchanging local results, and comparing collective communication with an explicit topology.

## Part A — Distributed image histogram

`ImplementationA.cpp` reads a grayscale P2/PGM-style image, partitions its pixels across MPI processes, and computes a 256-level histogram.

An adjacency matrix describes which distributed-system nodes are connected. Each process computes a local histogram for its assigned pixels, while process rank **1** coordinates the final aggregation.

Key concepts demonstrated:

- `MPI_Scatter`
- point-to-point `MPI_Send` / `MPI_Recv`
- adjacency-matrix-based process communication
- local computation + centralized aggregation
- parallel image-data partitioning

### Build

```bash
mpic++ -std=c++11 -O2 ImplementationA.cpp -o part_a
```

### Example

Part A uses rank 1 as the coordinator, so run it with at least two MPI processes:

```bash
mpirun -np 4 ./part_a tc1.pbm DS_matrix.txt histogram.txt
```

Arguments:

```text
<Input image filename> <adjacency-matrix file> <output file>
```

## Part B — Distributed word frequency

`ImplementationB.cpp` counts occurrences of a target word in a large text file after distributing chunks of the input across MPI processes.

It supports two aggregation modes:

### `b1` — MPI_Reduce

Each process counts locally and the results are combined with:

```text
MPI_Reduce(... MPI_SUM ...)
```

### `b2` — ring-style point-to-point aggregation

Processes explicitly pass accumulated counts through a ring-like communication path using `MPI_Send` and `MPI_Recv`.

This makes Part B a useful side-by-side example of a built-in MPI collective versus a manually coordinated topology.

### Build

```bash
mpic++ -std=c++11 -O2 ImplementationB.cpp -o part_b
```

### Examples

```bash
mpirun -np 4 ./part_b PnP.txt Bennet b1
mpirun -np 4 ./part_b PnP.txt Bennet b2
```

Arguments:

```text
<text file> <search word> <b1|b2>
```

## Repository contents

| File | Purpose |
| --- | --- |
| `ImplementationA.cpp` | Parallel image histogram |
| `ImplementationB.cpp` | Parallel word-frequency implementations |
| `DS_matrix.txt` | Example process-connectivity matrix |
| `tc*.pbm` | Sample grayscale image inputs used by the assignment |
| `PnP.txt` | Text corpus for word-frequency testing |
| `Timing.pdf` | Historical timing/results artifact |

## What this project demonstrates

- process-level parallelism with MPI;
- data partitioning;
- collective and point-to-point communication;
- topology-aware message passing;
- reducing local computation into a global result.
