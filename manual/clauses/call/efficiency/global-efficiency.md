# Global Efficiency

global\_efficiency(_G_)[\[source\]](https://networkx.org/documentation/stable/\_modules/networkx/algorithms/efficiency\_measures.html#global\_efficiency)

Returns the average global efficiency of the graph.

The _efficiency_ of a pair of nodes in a graph is the multiplicative inverse of the shortest path distance between the nodes. The _average global efficiency_ of a graph is the average efficiency of all pairs of nodes [\[1\]](https://networkx.org/documentation/stable/reference/algorithms/generated/networkx.algorithms.efficiency\_measures.global\_efficiency.html#r9f26066558a1-1).

Parameters:**G**[`networkx.Graph`](https://networkx.org/documentation/stable/reference/classes/graph.html#networkx.Graph)

An undirected graph for which to compute the average global efficiency.

Returns:float

The average global efficiency of the graph.

See also

[`local_efficiency`](https://networkx.org/documentation/stable/reference/algorithms/generated/networkx.algorithms.efficiency\_measures.local\_efficiency.html#networkx.algorithms.efficiency\_measures.local\_efficiency)

Notes

Edge weights are ignored when computing the shortest path distances.

References

\[[1](https://networkx.org/documentation/stable/reference/algorithms/generated/networkx.algorithms.efficiency\_measures.global\_efficiency.html#id1)]

Latora, Vito, and Massimo Marchiori. “Efficient behavior of small-world networks.” _Physical Review Letters_ 87.19 (2001): 198701. <[https://doi.org/10.1103/PhysRevLett.87.198701](https://doi.org/10.1103/PhysRevLett.87.198701)>

Examples

```
```

```
G = nx.Graph([(0, 1), (0, 2), (0, 3), (1, 2), (1, 3)])
```

```
round(nx.global_efficiency(G), 12)
0.916666666667
```
