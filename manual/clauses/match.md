# MATCH

### Introduction <a href="#match-introduction" id="match-introduction"></a>

The `MATCH` clause allows you to specify the patterns PostGraph will search for in the database. This is the primary way of getting data into the current set of bindings.&#x20;

`MATCH` is often coupled to a `WHERE` part which adds restrictions, or predicates, to the `MATCH` patterns, making them more specific.&#x20;

`MATCH` can occur at the beginning of the query or later, possibly after a `WITH`. If it is the first clause, nothing will have been bound yet, and PostGraph will design a search to find the results matching the clause and any associated predicates specified in any `WHERE` part. This could involve a scan of the database, a search for nodes having a certain label, or a search of an index to find starting points for the pattern matching. Nodes and relationships found by this search are available as _bound pattern elements,_ and can be used for pattern matching of paths. They can also be used in any further `MATCH` clauses, where PostGraph will use the known elements, and from there find further unknown elements.

Cypher is declarative, and so usually the query itself does not specify the algorithm to use to perform the search. PostGraph will automatically work out the best approach to finding start nodes and matching patterns. Predicates in `WHERE` parts can be evaluated before pattern matching, during pattern matching, or after finding matches.&#x20;

### Example graph

The following graph is used for the examples below:

![graph match clause](https://neo4j.com/docs/cypher-manual/current/\_images/graph\_match\_clause.svg)

To recreate the graph, run the following query against an empty Neo4j database:

```cypher
CREATE
  (charlie:Person {name: 'Charlie Sheen'}),
  (martin:Person {name: 'Martin Sheen'}),
  (michael:Person {name: 'Michael Douglas'}),
  (oliver:Person {name: 'Oliver Stone'}),
  (rob:Person {name: 'Rob Reiner'}),
  (wallStreet:Movie {title: 'Wall Street'}),
  (charlie)-[:ACTED_IN {role: 'Bud Fox'}]->(wallStreet),
  (martin)-[:ACTED_IN {role: 'Carl Fox'}]->(wallStreet),
  (michael)-[:ACTED_IN {role: 'Gordon Gekko'}]->(wallStreet),
  (oliver)-[:DIRECTED]->(wallStreet),
  (thePresident:Movie {title: 'The American President'}),
  (martin)-[:ACTED_IN {role: 'A.J. MacInerney'}]->(thePresident),
  (michael)-[:ACTED_IN {role: 'President Andrew Shepherd'}]->(thePresident),
  (rob)-[:DIRECTED]->(thePresident),
  (martin)-[:FATHER_OF]->(charlie)
```

### Basic vertex finding

#### Get all vertices

By specifying a pattern with a single node and no labels, all vertices in the graph will be returned.

Query

```cypher
MATCH (n)
RETURN n
```

Returns all the nodes in the database.

| n                                             |
| --------------------------------------------- |
| `(:Person {"name":"Charlie Sheen"})`          |
| `(:Person {"name":"Martin Sheen"})`           |
| `(:Person {"name":"Michael Douglas"})`        |
| `(:Person {"name":"Oliver Stone"})`           |
| `(:Person {"name":"Rob Reiner"})`             |
| `(:Movie {"title":"Wall Street"})`            |
| `(:Movie {"title":"The American President"})` |
| `Rows: 7`                                     |

#### Get all nodes with a label

Find all nodes with a specific label:

Query

```cypher
MATCH (movie:Movie)
RETURN movie.title
```

Returns all the nodes with the `Movie` label in the database.

| movie.title                |
| -------------------------- |
| `"Wall Street"`            |
| `"The American President"` |
| `Rows: 2`                  |

#### Related nodes

The symbol `--` means _related to,_ without regard to type or direction of the edge.

Query

```cypher
MATCH (director {name: 'Oliver Stone'})--(movie)
RETURN movie.title
```

Returns all the movies directed by `Oliver Stone`.

| movie.title     |
| --------------- |
| `"Wall Street"` |
| `Rows: 1`       |

#### Match with labels

To constrain a pattern with labels on vertices, add the labels to the vertices in the pattern.

Query

```cypher
MATCH (:Person {name: 'Oliver Stone'})--(movie:Movie)
RETURN movie.title
```

Returns any nodes with the `Movie` label connected to `Oliver Stone`.

| movie.title     |
| --------------- |
| `"Wall Street"` |
| `Rows: 1`       |

#### Match with a label expression for the edge labels

A match with an `OR` expression for the edge label returns the vertices that has either of the specified labels.

Query

```cypher
MATCH (n:Movie|Person)
RETURN n.name AS name, n.title AS title
```

| name                | title                      |
| ------------------- | -------------------------- |
| `"Charlie Sheen"`   | `<null>`                   |
| `"Martin Sheen"`    | `<null>`                   |
| `"Michael Douglas"` | `<null>`                   |
| `"Oliver Stone"`    | `<null>`                   |
| `"Rob Reiner"`      | `<null>`                   |
| `<null>`            | `"Wall Street"`            |
| `<null>`            | `"The American President"` |
| `Rows: 7`           |                            |

### Edges basics

#### Outgoing edges

When the direction of a relationship is of interest, it is shown by using `-→` or `←-`. For example:

Query

```cypher
MATCH (:Person {name: 'Oliver Stone'})-->(movie)
RETURN movie.title
```

Returns any nodes connected by an outgoing relationship to the `Person` node with the `name` property set to `Oliver Stone`.

| movie.title     |
| --------------- |
| `"Wall Street"` |
| `Rows: 1`       |

#### edge variables

It is possible to introduce a variable to a pattern, either for filtering on edge properties or to return an edge. For example:

Query

```cypher
MATCH (:Person {name: 'Oliver Stone'})-[r]->(movie)
RETURN type(r)
```

Returns the type of each outgoing edge from `Oliver Stone`.

| type(r)      |
| ------------ |
| `"DIRECTED"` |
| `Rows: 1`    |

#### Match on an undirected edge

When a pattern contains a bound relationship, and that relationship pattern does not specify direction, Cypher will try to match the relationship in both directions.

Query

```cypher
MATCH (a)-[:ACTED_IN {role: 'Bud Fox'}]-(b)
RETURN a, b
```

| a                                    | b                                    |
| ------------------------------------ | ------------------------------------ |
| `(:Movie {"title":"Wall Street"})`   | `(:Person {"name":"Charlie Sheen"})` |
| `(:Person {"name":"Charlie Sheen"})` | `(:Movie {"title":"Wall Street"})`   |
| `Rows: 2`                            |                                      |

#### Match on edge type

When the edge type to match on is known, it is possible to specify it by using a colon (`:`) before the edge type.

Query

```cypher
MATCH (wallstreet:Movie {title: 'Wall Street'})<-[:ACTED_IN]-(actor)
RETURN actor.name
```

Returns all actors who `ACTED_IN` the movie `Wall Street`.

| actor.name          |
| ------------------- |
| `"Michael Douglas"` |
| `"Martin Sheen"`    |
| `"Charlie Sheen"`   |
| `Rows: 3`           |

#### Match on multiple edge types

It is possible to match on multiple edge types by using the pipe symbol (`|`). For example:

Query

```cypher
MATCH (wallstreet {title: 'Wall Street'})<-[:ACTED_IN|DIRECTED]-(person)
RETURN person.name
```

Returns nodes with an `ACTED_IN` or `DIRECTED` relationship to the movie `Wall Street`.

| person.name         |
| ------------------- |
| `"Oliver Stone"`    |
| `"Michael Douglas"` |
| `"Martin Sheen"`    |
| `"Charlie Sheen"`   |
| `Rows: 4`           |

#### Match on edge type and use a variable

Variables and specific relationship types can be included in the same pattern. For example:

Query

```cypher
MATCH (wallstreet {title: 'Wall Street'})<-[r:ACTED_IN]-(actor)
RETURN r.role
```

Returns the `ACTED_IN` roles for the movie `Wall Street`.

| r.role           |
| ---------------- |
| `"Gordon Gekko"` |
| `"Carl Fox"`     |
| `"Bud Fox"`      |
| `Rows: 3`        |

### Relationships in depth

| Relationships will only be matched once inside a single pattern. Read more about this in the section on [relationship uniqueness](https://neo4j.com/docs/cypher-manual/current/patterns/reference/#graph-patterns-rules-relationship-uniqueness). |
| ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |

#### Relationship types with uncommon characters

Databases occasionally contain relationship types including non-alphanumerical characters, or with spaces in them. These are created using backticks (`` ` ``).

For example, the following query creates a relationship which contains a space (`OLD FRIENDS`) between `Martin Sheen` and `Rob Reiner`.

Query

```cypher
MATCH
  (martin:Person {name: 'Martin Sheen'}),
  (rob:Person {name: 'Rob Reiner'})
CREATE (rob)-[:`OLD FRIENDS`]->(martin)
```

This leads to the following graph:

![graph match clause backtick](https://neo4j.com/docs/cypher-manual/current/\_images/graph\_match\_clause\_backtick.svg)Query

```cypher
MATCH (n {name: 'Rob Reiner'})-[r:`OLD FRIENDS`]->()
RETURN type(r)
```



| type(r)         |
| --------------- |
| `"OLD FRIENDS"` |
| `Rows: 1`       |

#### Multiple edges

Relationships can be expressed by using multiple statements in the form of `()--()`, or they can be strung together. For example:

Query

```cypher
MATCH (charlie {name: 'Charlie Sheen'})-[:ACTED_IN]->(movie)<-[:DIRECTED]-(director)
RETURN movie.title, director.name
```

Returns the movie in which `Charlie Sheen` acted and its director.

| movie.title     | director.name    |
| --------------- | ---------------- |
| `"Wall Street"` | `"Oliver Stone"` |
| `Rows: 1`       |                  |

