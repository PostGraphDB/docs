# OPTIONAL MATCH

### Introduction <a href="#_introduction" id="_introduction"></a>

`OPTIONAL MATCH` matches patterns against a graph database, just as `MATCH` does. The difference is that if no matches are found, `OPTIONAL MATCH` will use a `null` for missing parts of the pattern. `OPTIONAL MATCH` could therefore be considered the Cypher equivalent of an outer join in SQL.

When using `OPTIONAL MATCH`, either the whole pattern is matched, or nothing is matched. The `WHERE` clause is part of the pattern description, and its predicates will be considered while looking for matches, not after. This matters especially in the case of multiple (`OPTIONAL`) `MATCH` clauses, where it is crucial to put `WHERE` together with the `MATCH` it belongs to.

| To understand the patterns used in the `OPTIONAL MATCH` clause, read [Patterns](https://neo4j.com/docs/cypher-manual/current/patterns/). |
| ---------------------------------------------------------------------------------------------------------------------------------------- |

### Example graph

The following graph is used for the examples below:

![graph optional match clause](https://neo4j.com/docs/cypher-manual/current/\_images/graph\_optional\_match\_clause.svg)

To recreate the graph, run the following query in an empty Neo4j database:

```cypher
CREATE
  (charlie:Person {name: 'Charlie Sheen'}),
  (martin:Person {name: 'Martin Sheen'}),
  (michael:Person {name: 'Michael Douglas'}),
  (oliver:Person {name: 'Oliver Stone'}),
  (rob:Person {name: 'Rob Reiner'}),
  (wallStreet:Movie {title: 'Wall Street'}),
  (charlie)-[:ACTED_IN]->(wallStreet),
  (martin)-[:ACTED_IN]->(wallStreet),
  (michael)-[:ACTED_IN]->(wallStreet),
  (oliver)-[:DIRECTED]->(wallStreet),
  (thePresident:Movie {title: 'The American President'}),
  (martin)-[:ACTED_IN]->(thePresident),
  (michael)-[:ACTED_IN]->(thePresident),
  (rob)-[:DIRECTED]->(thePresident),
  (martin)-[:FATHER_OF]->(charlie)
```

For example, the following query returns no results:

```cypher
MATCH (a:Person {name: 'Martin Sheen'})
MATCH (a)-[r:DIRECTED]->()
RETURN a.name, r
```

```result
(no changes, no records)
```

This is because the second `MATCH` clause returns no data (there are no `DIRECTED` relationships connected to `Martin Sheen` in the graph) to pass on to the `RETURN` clause.

However, replacing the second `MATCH` clause with `OPTIONAL MATCH` does return results. This is because, unlike `MATCH`, `OPTIONAL MATCH` enables the value `null` to be passed between clauses.

```cypher
MATCH (p:Person {name: 'Martin Sheen'})
OPTIONAL MATCH (p)-[r:DIRECTED]->()
RETURN p.name, r
```



| p.name           | r        |
| ---------------- | -------- |
| `"Martin Sheen"` | `<null>` |
| Rows: 1          |          |

`OPTIONAL MATCH` can therefore be used to check graphs for missing as well as existing values, and to pass on rows without any data to subsequent clauses in a query.

### Optional relationships

If the existence of a relationship is optional, use the `OPTIONAL MATCH` clause. If the relationship exists, it is returned. If it does not, `null` is returned in its place.

```cypher
MATCH (a:Movie {title: 'Wall Street'})
OPTIONAL MATCH (a)-->(x)
RETURN x
```

Returns `null`, since the `Movie` node `Wall Street` has no outgoing relationships.

| x        |
| -------- |
| `<null>` |
| Rows: 1  |

On the other hand, the following query does not return `null` since the `Person` node `Charlie Sheen` has one outgoing relationship.

```cypher
MATCH (a:Person {name: 'Charlie Sheen'})
OPTIONAL MATCH (a)-->(x)
RETURN x
```

| x                         |
| ------------------------- |
| `{"title":"Wall Street"}` |
| Rows: 2                   |

### Properties on optional elements

If the existence of a property is optional, use the `OPTIONAL MATCH` clause. `null` will be returned if the specified property does not exist.

```cypher
MATCH (a:Movie {title: 'Wall Street'})
OPTIONAL MATCH (a)-->(x)
RETURN x, x.name
```

Returns the element `x` (`null` in this query), and `null` for its `name` property, because the `Movie` node `Wall Street` has no outgoing relationships.

| x        | x.name   |
| -------- | -------- |
| `<null>` | `<null>` |
| Rows: 1  |          |

The following query only returns `null` for the nodes which lack a `name` property.

```cypher
MATCH (a:Person {name: 'Martin Sheen'})
OPTIONAL MATCH (a)-->(x)
RETURN x, x.name
```

| x                                    | x.name            |
| ------------------------------------ | ----------------- |
| `{"title":"Wall Street"}`            | `<null>`          |
| `{"name":"Charlie Sheen"}`           | `"Charlie Sheen"` |
| `{"title":"The American President"}` | `<null>`          |
| Rows: 3                              |                   |

### Optional typed and named relationship

It is also possible to look for specific relationship types when using `OPTIONAL MATCH`:

```cypher
MATCH (a:Movie {title: 'Wall Street'})
OPTIONAL MATCH (a)-[r:ACTED_IN]->()
RETURN a.title, r
```

This returns the title of the `Movie` node `Wall Street`, and since this node has no outgoing `ACTED_IN` relationships, `null` is returned for the relationship denoted by the variable `r`.

| a.title         | r        |
| --------------- | -------- |
| `"Wall Street"` | `<null>` |
| Rows: 1         |          |

However, the following query does not return `null` since it is looking for incoming relationships of the type `ACTED_IN` to the `Movie` node `Wall Street`.

```cypher
MATCH (a:Movie {title: 'Wall Street'})
OPTIONAL MATCH (x)-[r:ACTED_IN]->(a)
RETURN a.title, x.name, type(r)
```

| a.title         | x.name              | type(r)      |
| --------------- | ------------------- | ------------ |
| `"Wall Street"` | `"Michael Douglas"` | `"ACTED_IN"` |
| `"Wall Street"` | `"Martin Sheen"`    | `"ACTED_IN"` |
| `"Wall Street"` | `"Charlie Sheen"`   | `"ACTED_IN"` |
| Rows: 3         |                     |              |
