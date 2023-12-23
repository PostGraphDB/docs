# Characteristic Path Length

average\_shortest\_path\_length(_G_, _weight=None_, _method=None_)[\[source\]](https://networkx.org/documentation/stable/\_modules/networkx/algorithms/shortest\_paths/generic.html#average\_shortest\_path\_length)

Returns the average shortest path length.

The average shortest path length is

where `V` is the set of nodes in `G`, `d(s, t)` is the shortest path from `s` to `t`, and `n` is the number of nodes in `G`.

Changed in version 3.0: An exception is raised for directed graphs that are not strongly connected.

Parameters:



| Parameter Name       | Data Type                                               | Description                                                                                                                                                                                                                                                                                                                                                                                                                  |
| -------------------- | ------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **G**                | NetworkX graph                                          |                                                                                                                                                                                                                                                                                                                                                                                                                              |
| **weight\_function** | None, string or function, optional (default = None)     | If None, every edge has weight/distance/cost 1. If a string, use this edge attribute as the edge weight. Any edge attribute not present defaults to 1. If this is a function, the weight of an edge is the value returned by the function. The function must accept exactly three positional arguments: the two endpoints of an edge and the dictionary of edge attributes for that edge. The function must return a number. |
| **method**           | string, optional (default = ‘unweighted’ or ‘dijkstra’) | The algorithm to use to compute the path lengths. Supported options are ‘unweighted’, ‘dijkstra’, ‘bellman-ford’, and ‘floyd-warshall’. Other method values produce a ValueError. The default method is ‘unweighted’ if `weight` is None, otherwise the default method is ‘dijkstra’.                                                                                                                                        |



Raises:NetworkXPointlessConcept

If `G` is the null graph (that is, the graph on zero nodes).

return null instead

NetworkXError

If `G` is not connected (or not strongly connected, in the case of a directed graph).

ValueError

If `method` is not among the supported options.

Examples

```
G = nx.path_graph(5)
```

```
nx.average_shortest_path_length(G)
2.0
```

For disconnected graphs, you can compute the average shortest path length for each component

```
G = nx.Graph([(1, 2), (3, 4)])
```

```
for C in (G.subgraph(c).copy() for c in nx.connected_components(G)):
```

```
    print(nx.average_shortest_path_length(C))
1.0
1.0
```
