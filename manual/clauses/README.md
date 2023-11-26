# Clauses

This section contains information on all the clauses in the Cypher query language.

### Reading clauses

These comprise clauses that read data from the database.

The flow of data within a Cypher query is an unordered sequence of maps with key-value pairs — a set of possible bindings between the variables in the query and values derived from the database. This set is refined and augmented by subsequent parts of the query.

| Clause                                  | Description                                                                                              |
| --------------------------------------- | -------------------------------------------------------------------------------------------------------- |
| [`MATCH`](match.md)                     | Specify the patterns to search for in the database.                                                      |
| [`OPTIONAL MATCH`](optional-match.md)   | Specify the patterns to search for in the database while using `nulls` for missing parts of the pattern. |
| [`MANDATORY MATCH`](mandatory-match.md) |                                                                                                          |

### Projecting clauses

These comprise clauses that define which expressions to return in the result set. The returned expressions may all be aliased using `AS`.

| Clause                          | Description                                                                                                                   |
| ------------------------------- | ----------------------------------------------------------------------------------------------------------------------------- |
| [`RETURN …​ [AS`](return.md)`]` | Defines what to include in the query result set.                                                                              |
| [`WITH …​ [AS`](with.md)`]`     | Allows query parts to be chained together, piping the results from one to be used as starting points or criteria in the next. |
| [`UNWIND …​ [AS`](unwind.md)`]` | Expands a list into a sequence of rows.                                                                                       |

### Reading sub-clauses

These comprise sub-clauses that must operate as part of reading clauses.

| Sub-clause                                              | Description                                                                                                                                   |
| ------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------- |
| [`WHERE`](where.md)                                     | Adds constraints to the patterns in a `MATCH` or `OPTIONAL MATCH` clause or filters the results of a `WITH` clause.                           |
| [`ORDER BY [ASC[ENDING] \| DESC[ENDING]]`](order-by.md) | A sub-clause following `RETURN` or `WITH`, specifying that the output should be sorted in either ascending (the default) or descending order. |
| [`SKIP`](skip-limit-offset.md)                          | Defines from which row to start including the rows in the output.                                                                             |
| [`LIMIT`](skip-limit-offset.md)                         | Constrains the number of rows in the output.                                                                                                  |
| [`OFFSET`](skip-limit-offset.md)                        |                                                                                                                                               |

### Writing clauses

These comprise clauses that write the data to the database.

| Clause                       | Description                                                                                                                  |
| ---------------------------- | ---------------------------------------------------------------------------------------------------------------------------- |
| [`CREATE`](create.md)        | Create nodes and relationships.                                                                                              |
| [`DELETE`](delete.md)        | Delete nodes, relationships or paths. Any node to be deleted must also have all associated relationships explicitly deleted. |
| [`DETACH DELETE`](delete.md) | Delete a node or set of nodes. All associated relationships will automatically be deleted.                                   |
| [`SET`](set.md)              | Update labels on nodes and properties on nodes and relationships.                                                            |
| [`REMOVE`](remove.md)        | Remove properties and labels from nodes and relationships.                                                                   |
| [`FOREACH`](foreach.md)      | Update data within a list, whether components of a path, or the result of aggregation.                                       |

### Reading/Writing clauses

These comprise clauses that both read data from and write data to the database.

| Clause                             | Description                                                                                                               |
| ---------------------------------- | ------------------------------------------------------------------------------------------------------------------------- |
| [`MERGE`](merge.md)                | Ensures that a pattern exists in the graph. Either the pattern already exists, or it needs to be created.                 |
| `---` [`ON CREATE`](merge.md)      | Used in conjunction with `MERGE`, this write sub-clause specifies the actions to take if the pattern needs to be created. |
| `---` [`ON MATCH`](merge.md)       | Used in conjunction with `MERGE`, this write sub-clause specifies the actions to take if the pattern already exists.      |
| [`CALL …​ [YIELD …​` ](call.md)`]` | Invokes a procedure deployed in the database and return any results.                                                      |

### Subquery clauses

| Clause                   | Description                                                                     |
| ------------------------ | ------------------------------------------------------------------------------- |
| [`CALL { …​ }`](call.md) | Evaluates a subquery, typically used for post-union processing or aggregations. |

### Set operations

| Clause                                        | Description                                                                                |
| --------------------------------------------- | ------------------------------------------------------------------------------------------ |
| [`UNION`](set-operators/union.md)             | Combines the result of multiple queries into a single result set. Duplicates are removed.  |
| [`UNION ALL`](set-operators/union.md)         | Combines the result of multiple queries into a single result set. Duplicates are retained. |
| [`EXCEPT`](set-operators/except.md)           |                                                                                            |
| [`EXCEPT ALL`](set-operators/except.md)       |                                                                                            |
| [`INTERSECT`](set-operators/intersect.md)     |                                                                                            |
| [`INTERSECT ALL`](set-operators/intersect.md) |                                                                                            |

