---
description: Returns the average local efficiency of the graph.
---

# Local Efficiency

The _efficiency_ of a pair of nodes in a graph is the multiplicative inverse of the shortest path distance between the nodes. The _local efficiency_ of a node in the graph is the average global efficiency of the subgraph induced by the neighbors of the node. The _average local efficiency_ is the average of the local efficiencies of each node [\[1\]](https://networkx.org/documentation/stable/reference/algorithms/generated/networkx.algorithms.efficiency\_measures.local\_efficiency.html#re784e5fec883-1).

Parameters: None

Returns:float

The average local efficiency of the graph.

See also

[`global_efficiency`](https://networkx.org/documentation/stable/reference/algorithms/generated/networkx.algorithms.efficiency\_measures.global\_efficiency.html#networkx.algorithms.efficiency\_measures.global\_efficiency)

Notes

Edge weights are ignored when computing the shortest path distances.

References

\[[1](https://networkx.org/documentation/stable/reference/algorithms/generated/networkx.algorithms.efficiency\_measures.local\_efficiency.html#id1)]

Latora, Vito, and Massimo Marchiori. “Efficient behavior of small-world networks.” _Physical Review Letters_ 87.19 (2001): 198701. <[https://doi.org/10.1103/PhysRevLett.87.198701](https://doi.org/10.1103/PhysRevLett.87.198701)>
