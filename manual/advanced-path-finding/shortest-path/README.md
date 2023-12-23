# Shortest Path

```plsql
[ {MANDATORY | OPTIONAL} ] MATCH 
[ ALL | DISTINCT [ ON ( expression [, ...] ) ] ]
[SHORTEST (USING shortest_path_method) [WITH traversal_option AS config [, ...]]]
traversal_patterns
[ WHERE condition ]
[ YIELDING {* | expression [AS output_name] [, ...]} ]
[ GROUP BY expression [,...] [HAVING expresion [,...]] ]
[ WINDOW window_name AS ( window_definition ) [, ...] ]
[ ORDER BY expression [ ASC | DESC | USING operator ] [ NULLS { FIRST | LAST } ] [, ...] ]
[ LIMIT { count | ALL } ]
[ OFFSET start [ ROW | ROWS ] ]
[ FETCH { FIRST | NEXT } [ count ] { ROW | ROWS } { ONLY | WITH TIES } ]
```

```plsql
SELECT *
FROM cypher('graph_name', $$
    MATCH t=()-[*]->()
    RETURN t
$$) as (t traversal);
```



```plsql
SELECT *
FROM cypher('graph_name', $$
    MATCH SHORTEST t=()-[*]->()
    RETURN t
$$) as (t traversal);
```

```
FROM cypher('graph_name', $$
    MATCH SHORTEST USING dijkstra WITH (edge_weights AS k, and node_weight as i, directions as BIDIRECTIONAL)
     t=()-[*]->()
    RETURN t
$$) as (t traversal);
```

Available Algorithms (from NetworkX)

* unweighted
* dijkstra
* bellman-ford
* floyd-warshall

Available Algorithms (from PGRouting)

* All Pairs Shortest Path, Johnson’s Algorithm
* All Pairs Shortest Path, Floyd-Warshall Algorithm
* Shortest Path A\*
* Bi-directional Dijkstra Shortest Path
* Bi-directional A\* Shortest Path
* Shortest Path Dijkstra
* Driving Distance
* K-Shortest Path, Multiple Alternative Paths
* K-Dijkstra, One to Many Shortest Path
* Traveling Sales Person
* Turn Restriction Shortest Path (TRSP)



