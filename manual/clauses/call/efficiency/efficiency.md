# Efficiency

efficiency(_u_, _v, undirected_)[\[source\]](https://networkx.org/documentation/stable/\_modules/networkx/algorithms/efficiency\_measures.html#efficiency)

Returns the efficiency of a pair of nodes in a graph.

The _efficiency_ of a pair of nodes is the multiplicative inverse of the shortest path distance between the nodes [\[1\]](https://networkx.org/documentation/stable/reference/algorithms/generated/networkx.algorithms.efficiency\_measures.efficiency.html#r043892b9376c-1). Returns 0 if no path between nodes.

Parameters:**G**[`networkx.Graph`](https://networkx.org/documentation/stable/reference/classes/graph.html#networkx.Graph)

An undirected graph for which to compute the average local efficiency.

**u, v**node

Nodes in the graph `G`.

Returns:float

Multiplicative inverse of the shortest path distance between the nodes.

See also

[`local_efficiency`](https://networkx.org/documentation/stable/reference/algorithms/generated/networkx.algorithms.efficiency\_measures.local\_efficiency.html#networkx.algorithms.efficiency\_measures.local\_efficiency)[`global_efficiency`](https://networkx.org/documentation/stable/reference/algorithms/generated/networkx.algorithms.efficiency\_measures.global\_efficiency.html#networkx.algorithms.efficiency\_measures.global\_efficiency)

Notes

Edge weights are ignored when computing the shortest path distances.

References

\[[1](https://networkx.org/documentation/stable/reference/algorithms/generated/networkx.algorithms.efficiency\_measures.efficiency.html#id1)]

Latora, Vito, and Massimo Marchiori. “Efficient behavior of small-world networks.” _Physical Review Letters_ 87.19 (2001): 198701. <[https://doi.org/10.1103/PhysRevLett.87.198701](https://doi.org/10.1103/PhysRevLett.87.198701)>

Examples

```
G = nx.Graph([(0, 1), (0, 2), (0, 3), (1, 2), (1, 3)])
```

```
nx.efficiency(G, 2, 3)  # this gives efficiency for node 2 and 3
0.5
```

***

Additional backends implement this function

[graphblas](https://github.com/python-graphblas/graphblas-algorithms) : OpenMP-enabled sparse linear algebra backend.
