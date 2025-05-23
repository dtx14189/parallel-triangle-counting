# Shared Memory Parallel Triangle Counting
This project presents a shared-memory parallel algorithm for triangle counting in undirected graphs. Triangle counting is a fundamental graph operation with applications in social network analysis, spam detection, and recommendation systems. As graph sizes and densities continue to increase, traditional triangle counting approaches can become performance bottlenecks. While distributed frameworks like MPC offer scalability, they also introduce infrastructure complexity.

Our goal was to explore how far we could optimize triangle counting within a shared-memory setting, focusing on both theoretical efficiency and practical runtime. Drawing inspiration from a distributed-memory algorithm by [Liu et. al.](https://arxiv.org/abs/2405.00262), we developed a parallel implementation using C++ and ParlayLib. The final algorithm was benchmarked on a diverse set of real-world graphs from Stanford’s SNAP dataset and achieved favorable performance compared to GBBS, a well-established baseline for graph processing.

## Brief Overview of Algorithm Design
The algorithm iterates over all edges in the graph in parallel. For each edge {u, v}, it computes the number of common neighbors between u and v. Since every triangle is counted once per edge, the final count is divided by 3 in the baseline. In our final optimized version, we use a forward adjacency list to avoid duplicate triangle counts entirely.

To compute the intersection of neighbor lists, we implemented two main strategies:

- **Binary Search:** For edges where one endpoint has significantly higher degree, we iterate through the smaller list and binary search into the larger one.
- **Two-Pointer Traversal:** For balanced-degree pairs, we linearly scan both sorted neighbor lists.

Our algorithm chooses between the two strategies dynamically at runtime.

All intersection results are stored in a local array and aggregated via parallel reduction to avoid contention on shared variables.

## Key Results
We tested our implementation on 49 real-world graphs from the SNAP dataset. On average, our optimized hybrid algorithm achieved a **1.75× speedup** over GBBS on small and medium-sized graphs (under ~4 million edges). While GBBS begins to outperform our algorithm on extremely large graphs, we confirmed through statistical testing that our speedup is significant for the majority of practical cases.

We also analyzed the effect of graph density (arboricity) on runtime. Our approach performs better on sparse to moderately dense graphs, and the performance advantage decreases more gradually on high-arboricity graphs.


## How to Run
To try out our algorithm, we recommend you follow the two-line tutorial below. Run both commands while you are in the main project folder. Note that our source code for the ./final executable is in main.cpp.

```
$ make final
$ ./final test_graphs/rbl_email_enron.adj
```

To run the corresponding test for the GBBS benchmark, you can run the following commands. Run both commands while you are in the main project folder. *Note that if you are on the Yale Zoo, you do not need to run the first command, as we include the precompiled ./TriangleCount executable in the repository.*

```
$ g++ -std=c++17 -O3 -pthread \
    benchmarks/TriangleCounting/ShunTangwongsan15/Triangle.cc \
    gbbs/graph_io.cc gbbs/io.cc gbbs/helpers/parse_command_line.cc \
    gbbs/encodings/byte.cc \
    gbbs/encodings/byte_pd.cc \
    gbbs/encodings/byte_pd_amortized.cc \
    -I. -Igbbs -Iparlaylib/include \
    -o TriangleCount
$ ./TriangleCount -s test_graphs/rbl_email_enron.adj
```
## More Details
For more details about our algorithm or our results, please refer to the report in this repository.
