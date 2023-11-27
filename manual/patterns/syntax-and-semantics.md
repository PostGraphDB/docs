---
description: >-
  This section contains reference material for looking up the syntax and
  semantics of specific elements of graph pattern matching.
---

# Syntax and semantics

### Node patterns

A node pattern is a pattern that matches a single node. It can be used on its own in a clause such as `MATCH` or `EXIST`, or form part of a [path pattern](https://neo4j.com/docs/cypher-manual/current/patterns/reference/#path-patterns).

See also [node pattern concepts](https://neo4j.com/docs/cypher-manual/current/patterns/concepts/#node-patterns).

#### Syntax

```syntax
nodePattern ::= "(" [ nodeVariable ] [ labelExpression ]
    [ propertyKeyValueExpression ] [ "WHERE" booleanExpression ] ")"
```

For rules on valid node variable names, see the [Cypher® naming rules](https://neo4j.com/docs/cypher-manual/current/syntax/naming/).

#### Rules

**Predicates**

Three types of predicate can be specified inside a node pattern:

* [label expressions](https://neo4j.com/docs/cypher-manual/current/patterns/reference/#label-expressions)
* [property key-value expressions](https://neo4j.com/docs/cypher-manual/current/patterns/reference/#property-key-value-expressions)
* [WHERE clauses](https://neo4j.com/docs/cypher-manual/current/clauses/where/)

The boolean expression of the `WHERE` clause can reference any variables within scope of the node pattern. A node variable needs to be declared in the node pattern in order to reference it in the boolean expression.

If no predicates are specified, then the node pattern matches any node.

**Variable binding**

If a variable has not been declared elsewhere in the query, it will become bound to nodes when the matching of its containing path pattern is executed. If it has been bound in a previous clause, then no new nodes will be bound to the variable; any previously bound nodes that do not match in the current path pattern will lead to the match being eliminated from the results. See the section on [clause composition](https://neo4j.com/docs/cypher-manual/current/clauses/clause\_composition/) for more details on the passing of results between clauses.

#### Examples

Matches all nodes with the label `A` and binds them to the variable `n`:

```none
(n:A)
```

Matches all nodes with the label `B` and a property `departs` with the `time` value `11:11`:

```none
(:B { departs: time('11:11') })
```

Matches all nodes with the property `departs` with a value equal to the current time plus 30 minutes:

```none
(n WHERE n.departs > time() + duration('PT30M'))
```

### Relationship patterns

A relationship pattern is a pattern that matches a single relationship. It can only be used with node patterns on either side of it.

A relationship pattern followed immediately by a quantifier is an abbreviated [quantified path pattern](https://neo4j.com/docs/cypher-manual/current/patterns/reference/#quantified-path-patterns) called a [quantified relationship](https://neo4j.com/docs/cypher-manual/current/patterns/reference/#quantified-relationships).

See also [relationship pattern concepts](https://neo4j.com/docs/cypher-manual/current/patterns/concepts/#relationship-patterns).

#### Syntax

```syntax
relationshipPattern ::= fullPattern | abbreviatedRelationship

fullPattern ::=
    "<-[" patternFiller "]-"
  | "-[" patternFiller "]->"
  | "-[" patternFiller "]-"

abbreviatedRelationship ::= "<--" | "--" | "-->"

patternFiller ::= [ relationshipVariable ] [ typeExpression ]
    [ propertyKeyValueExpression ] [ "WHERE" booleanExpression ]
```

Note that the syntax for type expressions in relationship patterns is the same as for label expressions in node patterns (although unlike node labels, relationships must have exactly one type).

For rules on valid relationship variable names, see the [Cypher naming rules](https://neo4j.com/docs/cypher-manual/current/syntax/naming/).

#### Rules

**Predicates**

The following three types of predicate can be specified in the pattern filler of a full relationship pattern (i.e. a pattern with the square brackets):

* [label expressions](https://neo4j.com/docs/cypher-manual/current/patterns/reference/#label-expressions)
* [property key-value expressions](https://neo4j.com/docs/cypher-manual/current/patterns/reference/#property-key-value-expressions)
* [WHERE clauses](https://neo4j.com/docs/cypher-manual/current/clauses/where/)

A fourth type of predicate specifies the directionality of the relationship with respect to the overall path pattern, using the less-than or greater-than symbols to form arrows (`<` and `>`). If a relationship pattern has no arrows, it will match relationships of any direction.

The boolean expression of the `WHERE` clause can reference any variables within scope of the relationship pattern. A relationship variable needs to be declared in the pattern in order to reference it in the boolean expression.

If no predicates are specified then the pattern matches all relationships.

**Variable binding**

If the variable has not been declared elsewhere in the query, it will become bound to relationships when the matching of its containing path pattern is executed. If it has been bound in a previous clause, then no new relationships will be bound to the variable; if any previously bound relationships do not match in the current path pattern, then those matches will be eliminated from the results.

See the chapter on [clause composition](https://neo4j.com/docs/cypher-manual/current/clauses/clause\_composition/) for more details on the passing of results between clauses.

#### Examples

Matches all relationships with the type `R` and binds them to the variable `r`:

```none
()-[r:R]->()
```

Matches all relationships with type `R` and property `distance` equal to `100`:

```none
()-[:R {distance: 100}]->()
```

Matches all relationships where property `distance` is between `10` and `100`:

```none
()-[r WHERE 10 < r.distance < 100]->()
```

Matches all relationships that connect nodes with label `A` as their source and nodes with label `B` as their target:

```none
(:A)-->(:B)
```

Matches all relationships that connect nodes with label `A` and nodes with label `B`, irrespective of their direction:

```none
(:A)--(:B)
```

### Label expressions

The following applies to both the label expressions of node patterns and the type expressions of relationship patterns.

A label expression is a boolean predicate composed from label names and a wildcard symbol using disjunction, conjunction, negation and grouping. A label expression returns true when it matches the set of labels for a node.

Although relationships have a type rather than labels, the syntax for expressions matching a relationship type is identical to that of label expressions.

#### Syntax

```syntax
labelExpression ::= ":" labelTerm

labelTerm ::=
    labelIdentifier
  | labelTerm "&" labelTerm
  | labelTerm "|" labelTerm
  | "!" labelTerm
  | "%"
  | "(" labelTerm ")"
```

For valid label identifiers, see the [Cypher naming rules](https://neo4j.com/docs/cypher-manual/current/syntax/naming/).

#### Rules

The following table lists the symbols used in label expressions:

| Symbol | Description                                                                                         | Precedence  |
| ------ | --------------------------------------------------------------------------------------------------- | ----------- |
| `%`    | Wildcard. Evaluates to `true` if the label set is non-empty                                         |             |
| `()`   | Contained expression is evaluated before evaluating the outer expression the group is contained in. | 1 (highest) |
| `!`    | Negation                                                                                            | 2           |
| `&`    | Conjunction                                                                                         | 3           |
| `\|`   | Disjunction                                                                                         | 4 (lowest)  |

Associativity is left-to-right.

#### Examples

In the following table, a tick is shown where the label expression matches the node with the labels shown:

|                   | **Node** |        |        |        |          |          |          |            |
| ----------------- | -------- | ------ | ------ | ------ | -------- | -------- | -------- | ---------- |
| **Node pattern**  | `()`     | `(:A)` | `(:B)` | `(:C)` | `(:A:B)` | `(:A:C)` | `(:B:C)` | `(:A:B:C)` |
| `()`              | ✅        | ✅      | ✅      | ✅      | ✅        | ✅        | ✅        | ✅          |
| `(:A)`            |          | ✅      |        |        | ✅        | ✅        |          | ✅          |
| `(:A&B)`          |          |        |        |        | ✅        |          |          | ✅          |
| `(:A\|B)`         |          | ✅      | ✅      |        | ✅        | ✅        | ✅        | ✅          |
| `(:!A)`           | ✅        |        | ✅      | ✅      |          |          | ✅        |            |
| `(:!!A)`          |          | ✅      |        |        | ✅        | ✅        |          | ✅          |
| `(:A&!A)`         |          |        |        |        |          |          |          |            |
| `(:A\|!A)`        | ✅        | ✅      | ✅      | ✅      | ✅        | ✅        | ✅        | ✅          |
| `(:%)`            |          | ✅      | ✅      | ✅      | ✅        | ✅        | ✅        | ✅          |
| `(:!%)`           | ✅        |        |        |        |          |          |          |            |
| `(:%\|!%)`        | ✅        | ✅      | ✅      | ✅      | ✅        | ✅        | ✅        | ✅          |
| `(:%&!%)`         |          |        |        |        |          |          |          |            |
| `(:A&%)`          |          | ✅      |        |        | ✅        | ✅        |          | ✅          |
| `(:A\|%)`         |          | ✅      | ✅      | ✅      | ✅        | ✅        | ✅        | ✅          |
| `(:(A&B)&!(B&C))` |          |        |        |        | ✅        |          |          |            |
| `(:!A&%)`         |          |        | ✅      | ✅      |          |          | ✅        |            |

As relationships have exactly one type each, this expression will never match a relationship:

```none
-[:A&B]->
```

Similarly, the following will always match a relationship:

```none
-[:%]->
```

The use of negation can make the conjunction useful in relationship patterns. The following matches relationships that have a type that is neither `A` nor `B`:

```none
-[:!A&!B]->
```

### Property key-value expressions

#### Syntax

```syntax
propertyKeyValueExpression ::=
  "{" propertyKeyValuePairList "}"

propertyKeyValuePairList ::=
  propertyKeyValuePair [ "," propertyKeyValuePair ]

propertyKeyValuePair ::= propertyName ":" valueExpression
```

#### Rules

The property key-value expression is treated as a conjunction of equalities on the properties of the element that the containing pattern matches.

For example, the following node pattern:

```none
({ p: valueExp1, q: valueExp2 })
```

is equivalent to the following node pattern with a `WHERE` clause:

```none
(n WHERE n.p = valueExp1 AND n.q = valueExp2)
```

The value expression can be any expression as listed in the section on [expressions](https://neo4j.com/docs/cypher-manual/current/queries/expressions/), except for path patterns (which will throw a syntax error) and regular expressions (which will be treated as string literals). An empty property key-value expression matches all elements. Property key-value expressions can be combined with a `WHERE` clause.

#### Examples

Matches all nodes with property `p` = `10`:

```none
({ p: 10 })
```

Matches all relationships with property `p` = `10` and `q` equal to date `2023-02-10`:

```none
()-[{ p: 10, q: date('2023-02-10') }]-()
```

Matches all relationships with its property `p` equal to the property `p` of its source node:

```none
(s)-[{ p: s.p }]-()
```

Matches all nodes with property `p` = `10` and property `q` greater than `100`:

```none
(n { p: 10 } WHERE n.q > 100)
```

### Path patterns

A path pattern is the top level pattern that is matched against paths in a graph.

#### Syntax

```syntax
pathPattern ::= [{ simplePathPattern | quantifiedPathPattern }]+

simplePathPattern ::= nodePattern
  [ { relationshipPattern | quantifiedRelationship } nodePattern ]*
```

#### Rules

The minimum number of elements in the path pattern must be greater than zero. For example, a path pattern that is a quantified path pattern with a quantifier that has a lower bound of zero is not allowed:

```none
((n)-[r]->(m)){0,10}    //this is not allowed
```

A path pattern must always begin and end with a node pattern. The following is not allowed:

```none
(n)-[r]->(m)-[s]-    //this is not allowed
```

A path pattern may be composed of a concatenation of simple and quantified path patterns. Two simple path patterns, however, may not be placed next to each other. For example, the following is not allowed:

```none
(a)<-[s]-(b) (c)-[t]->(d)    //this is not allowed
```

When a path pattern is matched to paths in a graph, nodes can be revisited but relationships cannot.

See [graph patterns](https://neo4j.com/docs/cypher-manual/current/patterns/reference/#graph-patterns) for rules on declaring variables multiple times.

#### Examples

A single node pattern is allowed as it has at least one element:

```none
(n)
```

A simple path pattern with more than one element:

```none
(a:A)<-[{p: 30}]-(b)-[t WHERE t.q > 0]->(c:C)
```

A quantified path pattern can have a lower bound of zero in its quantifier as long as it abuts other patterns that have at least one element:

```none
(:A) ((:X)-[:R]-()){0,10} (:B)
```

A quantified relationship can also have a lower bound of zero as long as the overall path pattern has at least one element:

```none
(:A)-[:R]->{0,10}(:B)
```

A concatenation of simple and quantified path patterns:

```none
(a)<-[s]-(b)-[t]->(c) ((n)-[r]->(m)){0,10} (:X)
```

Referencing non-local node variable in a simple path pattern:

```none
(a)<-[s:X WHERE a.p = s.p]-(b)
```

Referencing a non-local relationship variable within a quantified path pattern:

```none
(:A) ((a)<-[s:X WHERE a.p = s.p]-(b)){,5}
```

A variable that was introduced in a previous clause can be referenced as long as that variable was defined outside of a quantified path pattern:

```none
MATCH (n)
MATCH ()-[r WHERE r.q = n.q]-() (()<-[s:X WHERE n.p = s.p]-()){2,3}
```

### Quantified path patterns

_This feature was introduced in Neo4j 5.9._

A quantified path pattern represents a [path pattern](https://neo4j.com/docs/cypher-manual/current/patterns/reference/#path-patterns) repeated a number of times in a given range. It is composed of a path pattern, representing the path section to be repeated, followed by a [quantifier](https://neo4j.com/docs/cypher-manual/current/patterns/reference/#quantifiers), constraining the number of repetitions between a lower bound and an upper bound.

![patterns qpp reference](https://neo4j.com/docs/cypher-manual/current/\_images/patterns\_qpp\_reference.svg)

For information about an alternative version of patterns for matching paths of variable length, see [variable-length relationships](https://neo4j.com/docs/cypher-manual/current/patterns/reference/#variable-length-relationships).

#### Syntax

```syntax
quantifiedPathPattern ::=
  "(" fixedPath [ "WHERE" booleanExpression ] ")" quantifier

fixedPath ::= nodePattern [ relationshipPattern nodePattern ]+
```

#### Rules

**Minimum pattern length**

The path pattern being quantified must have a length greater than zero. In other words, it must contain at least one relationship. A single node pattern cannot be quantified. For example, this is not allowed:

```none
((x:A)){2,4}    //this is not allowed
```

**Nesting of quantified path patterns**

The nesting of quantified path patterns is not allowed. For example, the following nesting of a quantified relationship in a quantified path pattern is not allowed:

```none
(:A) (()-[:R]->+()){2,3} (:B)    //this is not allowed
```

A quantified path pattern that is part of the boolean expression within a quantified path pattern would not count as nested and is permitted. For example, the following is valid:

```none
MATCH ((n:A)-[:R]->({p: 30}) WHERE EXISTS { (n)-->+(:X) }){2,3}
```

**Group variables**

Variables introduced inside of a quantified path pattern are said to be exposed as _group variables_ outside of the definition of the pattern. As a group variable, they will be bound to either a list of nodes or a list of relationships. By contrast, variables can be treated as singletons inside the quantified path pattern where they are declared. The difference can be seen in the following query:

```none
MATCH ((x)-[r]->(z WHERE z.p > x.p)){2,3}
RETURN [n in x | n.p] AS x_p
```

In the boolean expression `z.p > x.p` both `z` and `x` are singletons; in the `RETURN` clause, `x` is a group variable that can be iterated over like a list. Note that this means that the `WHERE` clause `z.p > x.p` above needs to be inside the quantified path pattern. The following would throw a syntax error because it is treating `z` and `p` as singletons:

```none
MATCH ((x)-[r]->(z)){2,3} WHERE z.p > x.p    //this is not allowed
```

It is possible, however, to position the `WHERE` clause outside of the node pattern:

```none
MATCH ((x)-[r]->(z) WHERE z.p > x.p){2,3}
```

**Matching**

The mechanics of matching a quantified path pattern against paths is best explained with an example. For the example, the following simple graph will be used:

![patterns qpp reference example](https://neo4j.com/docs/cypher-manual/current/\_images/patterns\_qpp\_reference\_example.svg)

First, consider the following simple path pattern:

```none
(x:A)-[:R]->(z:B WHERE z.h > 2)
```

This matches three different paths in the graph above. The resulting bindings for `x` and `z` for each match are the following (the captions `n1` etc indicate the identity of the nodes in the diagram above):

| x    | z    |
| ---- | ---- |
| `n1` | `n2` |
| `n2` | `n3` |
| `n3` | `n5` |

If the quantifier `{2}` is affixed to the simple path pattern, the result is the following quantified path pattern:

```none
((x:A)-[:R]->(z:B WHERE z.h > 2)){2}
```

This is equivalent to chaining together two iterations of the pattern, where the rightmost node of the first iteration is merged with the leftmost node of the second one. (See [node pattern pairs](https://neo4j.com/docs/cypher-manual/current/patterns/reference/#node-pattern-pairs) for more details.)

```none
(x:A)-[:R]->(z:B WHERE z.h > 2) (x:A)-[:R]->(z:B WHERE z.h > 2)
```

To avoid introducing [equijoins](https://neo4j.com/docs/cypher-manual/current/patterns/reference/#equijoins) between the two instances of `x`, and between the two instances of `z`, the variables are replaced with a set of fresh variables inside each iteration:

```none
(x1:A)-[:R]->(z1:B WHERE z1.h > 2) (x2:A)-[:R]->(z2:B WHERE z2.h > 2)
```

Then the node variables in adjoining node patterns are merged:

```none
(x1:A)-[:R]->({z1,x2}:A&B WHERE z1.h > 2)-[:R]->(z2:B WHERE z2.h > 2)
```

The fact that variables `x2` and `z1` are bound to matches of the same node pattern is represented with the notation `{z1,x2}`. Outside of the pattern, the variables `x` and `z` will be group variables that contain lists of nodes.

Consider the quantified path pattern in the following query:

```none
MATCH ((x:A)-[:R]->(z:B WHERE z.h > 2)){2}
RETURN [n in x | n.h] AS x_h, [n in z | n.h] AS z_h
```

This yields the following results:

| x\_h     | z\_h     |
| -------- | -------- |
| `[1, 3]` | `[3, 4]` |
| `[3, 4]` | `[4, 5]` |

Now the quantifier is changed to match lengths from one to five:

```none
MATCH ((x:A)-[:R]->(z:B WHERE z.h > 2)){1,5}
RETURN [n in x | n.h] AS x_h, [n in z | n.h] AS z_h
```

Compared to the fixed length quantifier `{2}`, this also matches paths of length one and three, but no matches exist for length greater than three:

| x\_h        | z\_h        |
| ----------- | ----------- |
| `[1]`       | `[3]`       |
| `[3]`       | `[4]`       |
| `[4]`       | `[5]`       |
| `[1, 3]`    | `[3, 4]`    |
| `[3, 4]`    | `[4, 5]`    |
| `[1, 3, 4]` | `[3, 4, 5]` |

### Quantified relationships

#### Syntax

```syntax
quantifiedRelationship ::= relationshipPattern quantifier
```

#### Rules

A quantified relationship is an abbreviated form of a [quantified path pattern](https://neo4j.com/docs/cypher-manual/current/patterns/reference/#quantified-path-patterns), with only a single relationship pattern specified.

For example, the following quantified relationship:

```none
(:A)-[r:R]->{m,n}(:B)
```

is equivalent to the following quantified path pattern:

```none
(:A) (()-[r:R]->()){m,n} (:B)
```

With the expanded form of a quantified path pattern, it is possible to add predicates inside. Conversely, if the only predicate is a relationship type expression, query syntax can be more concise using a quantified relationship.

Note that, unlike a quantified path pattern, a quantified relationship must always have a node pattern on each side.

#### Examples

Matches paths starting with nodes labeled `A` and ending with nodes labeled `B`, that traverse between two and three relationships of type `R`:

```none
(:A)-[r:R]->{2,3}(:B)
```

This is equivalent to the following:

```none
(:A) (()-[r:R]->()){2,3} (:B)
```

Matches paths with one or more relationships of any direction and any type:

```none
()--+()
```

### Quantifiers

The quantifiers here only refer to those used in [quantified path patterns](https://neo4j.com/docs/cypher-manual/current/patterns/reference/#quantified-path-patterns) and [quantified relationships](https://neo4j.com/docs/cypher-manual/current/patterns/reference/#quantified-relationships).

#### Syntax

```syntax
quantifier ::=
  "*" | "+" | fixedQuantifier | generalQuantifier

fixedQuantifier ::= "{" unsignedInteger "}"

generalQuantifier ::= "{" lowerBound "," upperBound "}"

lowerBound ::= unsignedInteger

upperBound ::= unsignedInteger

unsignedInteger ::= [0-9]+
```

#### Rules

The absence of an upper bound in the general quantifier syntax means there is no upper bound. The following table shows variants of the quantifier syntax and their canonical form:

| **Variant** | **Canonical form** | **Description**             |
| ----------- | ------------------ | --------------------------- |
| `{m,n}`     | `{m,n}`            | Between m and n iterations. |
| `+`         | `{1,}`             | 1 or more iterations.       |
| `*`         | `{0,}`             | 0 or more iterations.       |
| `{n}`       | `{n,n}`            | Exactly n iterations.       |
| `{m,}`      | `{m,}`             | m or more iterations.       |
| `{,n}`      | `{0,n}`            | Between 0 and n iterations. |
| `{,}`       | `{0,}`             | 0 or more iterations.       |

Note that a [quantified path pattern](https://neo4j.com/docs/cypher-manual/current/patterns/reference/#quantified-path-patterns) with the quantifier `{1}` is not equivalent to a fixed-length path pattern. Although the resulting quantified path pattern will match on the same paths the fixed-length path contained in it would without the quantifier, the presence of the quantifier means that all variables within the path pattern will be exposed as group variables.

### Graph patterns

A graph pattern is a comma separated list of one or more path patterns. It is the top level construct provided to `MATCH`.

![patterns graph pattern reference](https://neo4j.com/docs/cypher-manual/current/\_images/patterns\_graph\_pattern\_reference.svg)

#### Syntax

```none
graphPattern ::=
  pathPattern [ "," pathPattern ]* [ "WHERE" booleanExpression ]
```

#### Rules

The rules for [path patterns](https://neo4j.com/docs/cypher-manual/current/patterns/reference/#path-patterns) apply to each constituent path pattern of a graph pattern.

**Variable references**

Any node or relationship variable declared in a graph pattern can be referenced in a `WHERE` clause elsewhere in the graph pattern - unless it is inside a quantified path pattern not containing the variable. For example, this is allowed:

```none
(n)-->(m:A)-->(:B), (m)-[r WHERE r.p <> n.p]->(:C)
```

But this is not allowed:

```none
(n)-->(m:A)-->(:B), (m) (()-[r WHERE r.p <> n.p]->())+ (:C)    //this is not allowed
```

A variable can be referred to inside a quantified path pattern if it has already been bound in a previous `MATCH` clause. If a variable is declared inside a quantified path pattern, then it can be treated as a singleton only from within the quantified path pattern it was declared in. Outside of that quantified path pattern, it must be treated as a group variable. For example, this would be allowed:

```none
((n)-[r]->(m WHERE r.p = m.q))+
```

As would this:

```none
(n)-[r]->+(m WHERE all(rel in r WHERE rel.q > m.q))
```

But this would not be allowed:

```none
(n)-[r]->+(m WHERE r.p = m.q)    //this is not allowed
```

**Relationship uniqueness**

A relationship can only be traversed once in a given match for a graph pattern. The same restriction doesn’t hold for nodes, which may be re-traversed any number of times in a match.

**Equijoin**

If a node variable is declared more than once in a path pattern, it is expressing an equijoin. This is an operation that requires that each node pattern with the same node variable be bound to the same node. For example, the following pattern refers to the same node twice with the variable `a`, forming a cycle:

```none
(a)-->(b)-->(c)-->(a)
```

The following pattern refers to the same node with variable `b` in different path patterns of the same graph pattern, forming a "T" shaped pattern:

```none
(a)-->(b)-->(c), (b)-->(e)
```

Equijoins can only be made using variables outside of quantified path patterns. The following would not be a valid equijoin:

```none
(a)-->(b)-->(c), ((b)-->(e))+ (:X)    //this is not allowed
```

If no equijoin exists between path patterns in a graph pattern, then a Cartesian join is formed from the sets of matches for each path pattern. An equijoin can be expressed between relationship patterns by declaring a relationship variable multiple times. However, as relationships can only be traversed once in a given match, no solutions would be returned.

#### Examples

The `WHERE` clause can refer to variables inside and outside of quantified path patterns:

```none
(a)-->(b)-->(c), (b) ((d)-->(e))+ WHERE any(n in d WHERE n.p = a.p)
```

An equijoin can be formed to match "H" shaped graphs:

```none
(:A)-->(x)--(:B), (x)-[:R]->+(y), (:C)-->(y)-->(:D)
```

With no variables in common, this graph pattern will result in a Cartesian join between the sets of matches for the two path patterns:

```none
(a)-->(b)-->(c), ((d)-->(e))+
```

Multiple equijoins can be formed between path patterns:

```none
(:X)-->(a:A)-[!:R]->+(b:B)-->(:Y), (a)-[:R]->+(b)
```

Variables declared in a previous `MATCH` can be referenced inside of a quantified path pattern:

```none
MATCH (n {p = 'ABC'})
MATCH (n)-->(m:A)-->(:B), (m) (()-[r WHERE r.p <> n.p]->())+ (:C)
```

The repetition of a relationship variable in the following yields no solutions due to Cypher enforcing relationship uniqueness within a match for a graph pattern:

```none
MATCH ()-[r]->()-->(), ()-[r]-()
```

### Node pattern pairs

It is not valid syntax to write a pair of node patterns next to each other. For example, all of the following would raise a syntax error:

```none
(a:A)(b:B)
```

```none
(a:A)(b:B)<-[r:R]-(c:C)
```

```none
(a:A)<--(b:B)(c:C)-->(d:C)
```

However, the placing of pairs of node patterns next to each other is valid where it results indirectly from the expansion of quantified path patterns.

#### Iterations of quantified path patterns

When a quantified path pattern is expanded, the fixed path pattern contained in its parentheses is repeated and chained. This results in pairs of node patterns sitting next to each other. Take the following quantified path pattern as an example:

```none
((x:X)<--(y:Y)){3}
```

This is expanded by repeating the fixed path pattern `(x:X)←-(y:Y)` three times, with indices on the variables to show that no equijoin is implied (see [equijoins](https://neo4j.com/docs/cypher-manual/current/patterns/reference/#graph-patterns-rules-equijoin) for more information):

```none
(x1:X)<--(y1:Y)(x2:X)<--(y2:Y)(x3:X)<--(y3:Y)
```

The result is that two pairs of node patterns end up adjoining each other, `(y1:Y)(x2:X)` and `(y2:Y)(x3:X)`. During the matching process, each pair of node patterns will match the same nodes, and those nodes will satisfy the conjunction of the predicates in the node patterns. For example, in the first pair both `y1` and `x2` will bind to the same node, and that node must have labels `X` and `Y`. This expansion and binding is depicted in the following diagram:

![patterns node pattern pairs](https://neo4j.com/docs/cypher-manual/current/\_images/patterns\_node\_pattern\_pairs.svg)

#### Simple path patterns and quantified path patterns

Pairs of node patterns are also generated when a simple path pattern is placed next to a quantified path. For example, consider the following path pattern:

```none
(:A)-[:R]->(:B) ((:X)<--(:Y)){1,2}
```

After expanding the iterations of the quantified path pattern, the right-hand node pattern `(:B)` adjoins the left-hand node pattern `(:X)`. The result will match the same paths as the union of matches of the following two path patterns:

```none
(:A)-[:R]->(:B&X)<--(:Y)
```

```none
(:A)-[:R]->(:B&X)<--(:Y&X)<--(:Y)
```

If the simple path pattern is on the right of the quantified path pattern, its leftmost node `(:A)` adjoins the rightmost node `(:Y)` of the last iteration of the quantified path pattern. For example, the following:

```none
((:X)<--(:Y)){1,2} (:A)-[:R]->(:B)
```

will match the same paths as the union of the following two path patterns:

```none
(:X)<--(:Y&A)-[:R]->(:B)
```

```none
(:X)<--(:Y&X)<--(:Y&A)-[:R]->(:B)
```

#### Pairs of quantified path patterns

When two quantified path patterns adjoin, the rightmost node of the last iteration of the first pattern is merged with the leftmost node of the first iteration of the second pattern. For example, the following adjoining patterns:

```none
((:A)-[:R]->(:B)){2} ((:X)<--(:Y)){1,2}
```

will match the same set of paths as the union of the paths matched by these two path patterns:

```none
(:A)-[:R]->(:B&A)-[:R]->(:B&X)<--(:Y)
```

```none
(:A)-[:R]->(:B&A)-[:R]->(:B&X)<--(:Y&X)<--(:Y)
```

#### Zero iterations

If the quantifier allows for zero iterations of a pattern, for example `{0,3}`, then the 0th iteration of that pattern results in the node patterns on either side pairing up.

For example, the following path pattern:

```none
(:X) ((a:A)-[:R]->(b:B)){0,1} (:Y)
```

will match the same set of paths as the union of the paths matched by the following two path patterns:

```none
(:X&Y)
```

```none
(:X&A)-[:R]->(:B&Y)
```

### Variable-length relationships

Prior to the introduction of the syntax for quantified path patterns and quantified relationships in Neo4j 5.9, the only way in Cypher to match paths of variable length was with a variable-length relationship. This syntax is still available. It is equivalent to the syntax for quantified relationships, with the following differences:

* Position and syntax of quantifier.
* Semantics of the asterisk symbol.
* Type expressions are limited to the [disjunction operator](https://neo4j.com/docs/cypher-manual/current/syntax/operators/#query-operators-boolean).
* The [WHERE](https://neo4j.com/docs/cypher-manual/current/clauses/where/) clause is not allowed.

#### Syntax

```syntax
varLengthRelationship ::=
    "<-[" varLengthFiller "]-"
  | "-[" varLengthFiller "]->"
  | "-[" varLengthFiller "]-"
varLengthFiller ::= [ relationshipVariable ] [ varLengthTypeExpression ]
    [ varLengthQuantifier ] [ propertyKeyValueExpression ]

varLengthTypeExpression ::= ":" varLengthTypeTerm

varLengthTypeTerm ::=
    typeIdentifier
  | varLengthTypeTerm "|" varLengthTypeTerm

varLengthQuantifier ::= varLengthVariable | varLengthFixed

varLengthVariable ::= "*" [ [ lowerBound ] ".." [ upperBound ] ]

varLengthFixed ::= "*" fixedBound

fixedBound ::= unsignedInteger
lowerBound ::= unsignedInteger
upperBound ::= unsignedInteger
unsignedInteger ::= [0-9]+View all (9 more lines)
```

For rules on valid relationship variable names, see [Cypher naming rules](https://neo4j.com/docs/cypher-manual/current/syntax/naming/).

#### Rules

The following table shows variants of the variable-length quantifier syntax and their equivalent [quantifier](https://neo4j.com/docs/cypher-manual/current/patterns/reference/#quantifiers) form (the form used by [quantified path patterns](https://neo4j.com/docs/cypher-manual/current/patterns/reference/#quantified-path-patterns)):

| **Variant**  | **Description**             | **Quantified relationship equivalent** | **Quantified path pattern equivalent** |
| ------------ | --------------------------- | -------------------------------------- | -------------------------------------- |
| `-[*]->`     | 1 or more iterations.       | `-[]->+`                               | `(()-[]->())+`                         |
| `-[*n]->`    | Exactly n iterations.       | `-[]->{n}`                             | `(()-[]->()){n}`                       |
| `-[*m..n]->` | Between m and n iterations. | `-[]->{m,n}`                           | `(()-[]->()){m,n}`                     |
| `-[*m..]->`  | m or more iterations.       | `-[]->{m,}`                            | `(()-[]->()){m,}`                      |
| `-[*0..]->`  | 0 or more iterations.       | `-[]->*`                               | `(()-[]->())*`                         |
| `-[*..n]->`  | Between 1 and n iterations. | `-[]->{1,n}`                           | `(()-[]->()){1,n}`                     |
| `-[*0..n]->` | Between 0 and n iterations. | `-[]->{,n}`                            | `(()-[]->()){,n}`                      |

Note that `*` used here on its own is not the same as the Kleene star (an operator that represents zero or more repetitions), as the Kleene star has a lower bound of zero. The above table can be used to translate the quantifier used in variable-length relationships. The rules given for [quantified path patterns](https://neo4j.com/docs/cypher-manual/current/patterns/reference/#quantified-path-patterns) would apply to the translation.

This table shows some examples:

| **Variable-length relationship**    | **Equivalent quantified path pattern**       |
| ----------------------------------- | -------------------------------------------- |
| `(a)-[*2]->(b)`                     | `(a) (()-[]->()){2,2} (b)`                   |
| `(a)-[:KNOWS*3..5]->(b)`            | `(a) (()-[:KNOWS]->()){3,5} (b)`             |
| `(a)-[r*..5 {name: 'Filipa'}]->(b)` | `(a) (()-[r {name: 'Filipa'}]->()){1,5} (b)` |

#### Equijoins

The variable of a variable-length relationship can be used in subsequent patterns to refer to the list of relationships the variable is bound to. This is the same as the [equijoin](https://neo4j.com/docs/cypher-manual/current/patterns/reference/#graph-patterns-rules-equijoin) for variables bound to single nodes or relationships.

This section uses the following graph:

![patterns equijoin reference](https://neo4j.com/docs/cypher-manual/current/\_images/patterns\_equijoin\_reference.svg)

To recreate the graph, run the following query against an empty Neo4j database:

Query

```cypher
CREATE ({name: 'Filipa'})-[:KNOWS]->({name:'Anders'})-[:KNOWS]->
         ({name:'Dilshad'})
```

In the following query, the node variables will be bound to the same nodes:

Query

```cypher
MATCH (a {name: 'Dilshad'})<-[r*1..2]-(b)
MATCH (c)<-[r*1..2]-(d)
RETURN a = c, b = d, size(r)
```

| a = c   | b = d  | size(r) |
| ------- | ------ | ------- |
| `true`  | `true` | `1`     |
| `true`  | `true` | `2`     |
| Rows: 2 |        |         |

The list of relationships keeps its order. This means that in the following query, where the direction of the variable-length relationship in the second `MATCH` is switched, the equijoin will only match once, when there is a single relationship:

Query

```cypher
MATCH (a {name: 'Dilshad'})<-[r*1..2]-(b)
MATCH (c)-[r*1..2]->(d)
RETURN a = c, b = d, size(r)
```

| a = c   | b = d   | size(r) |
| ------- | ------- | ------- |
| `false` | `false` | `1`     |
| Rows: 1 |         |         |

The variable `r` can be reversed in order like any list, and made to match the switch in relationship pattern direction:

Query

```cypher
MATCH (a {name: 'Dilshad'})<-[r*1..2]-(b)
WITH a, b, reverse(r) AS s
MATCH (c)-[s*1..2]->(d)
RETURN a = d, b = c, size(s)
```

| a = d   | b = c  | size() |
| ------- | ------ | ------ |
| `true`  | `true` | `1`    |
| `true`  | `true` | `2`    |
| Rows: 2 |        |        |

Changing the bounds on subsequent `MATCH` statements will mean that only the overlapping lengths of the quantifier bounds will produce results:

Query

```cypher
MATCH (a {name: 'Dilshad'})<-[r*1..2]-(b)
MATCH (c)<-[r*2..3]-(d)
RETURN a = c, b = d, size(r)
```

| a = c   | b = d  | size(r) |
| ------- | ------ | ------- |
| `true`  | `true` | `2`     |
| Rows: 1 |        |         |

Because Cypher only allows paths to traverse a relationship once (see [relationship uniqueness](https://neo4j.com/docs/cypher-manual/current/patterns/reference/#graph-patterns-rules-relationship-uniqueness)), repeating a variable-length relationship in the same graph pattern will yield no results. For example, this `MATCH` clause will never pass on any intermediate results to subsequent clauses:

```none
MATCH (x)-[r*1..2]->(y)-[r*1..2]->(z)
```

Attempting to repeat a variable-length relationship in a single relationship pattern will raise an error. For example, the following pattern raises an error because the variable `r` appears in both a variable-length relationship and a fixed-length relationship:

```none
MATCH (x)-[r*1..2]->(y)-[r]->(z)
```

#### Examples

The following pattern matches paths starting with nodes labeled `A` and ending with nodes labeled `B`, that traverse between two and three relationships of type `R`:

```none
(:A)-[r:R*2..3]->(:B)
```

The following traverses relationships of type `R` or `S` or `T` exactly five times:

```none
()-[r:R|S|T*5]->()
```

The following traverses any relationship between zero and five times, with the path beginning at nodes labeled `A` and ending at nodes labeled `B`. Note that this will also return all nodes that have both labels `A` and `B` for the case where zero relationships are traversed:

```none
(:A)-[*0..5]->(:B)
```

If the lower bound is removed, it will default to one, and will no longer return paths of length zero, i.e. single nodes:

```none
(:A)-[*..5]->(:B)
```

The following pattern traverses one or more relationships of any direction that have property `p` = `$param`:

```syntax
()-[* {p: $param}]-()
```
