# HyperGraphs

Hypergraphs allow a more complex system of definition relationships between sets of vertices.







```
MATCH (n) YIELD collect(n) as n_lst GROUP BY n.prop1
MATCH (m) YIELD collect(m) as m_lst GROUP BY n.prop2
CREATE (n_lst)-[:REL_TYPE]->(m_lst)
```

