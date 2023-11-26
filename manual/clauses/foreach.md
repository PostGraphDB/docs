# FOREACH

Lists, Arrays and traversals are key concepts in Cypher. The `FOREACH` clause can be used to update data, such as executing update commands on elements in a path, or on a list created by aggregation.

The variable context within the `FOREACH` parenthesis is separate from the one outside it. This means that if you `CREATE` a node variable within a `FOREACH`, you will _not_ be able to use it outside of the foreach statement, unless you match to find it.

Within the `FOREACH` parentheses, you can do any of the updating commands — `SET`, `REMOVE`, `CREATE`, `MERGE`, `DELETE`, and `FOREACH`.

| If you want to execute an additional `MATCH` for each element in a list then the [`UNWIND`](https://neo4j.com/docs/cypher-manual/current/clauses/unwind/) clause would be a more appropriate command. |
| ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |

![graph foreach clause](https://neo4j.com/docs/cypher-manual/current/\_images/graph\_foreach\_clause.svg)

### Mark all nodes along a path

This query will set the property `marked` to `true` on all nodes along a path.

Query

```cypher
MATCH p=(start)-[*]->(finish)
WHERE start.name = 'A' AND finish.name = 'D'
FOREACH (n IN nodes(p) | SET n.marked = true)
```

| `(empty result)`                    |
| ----------------------------------- |
| <p>Rows: 0<br>Properties set: 4</p> |
