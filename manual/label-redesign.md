---
description: >-
  Comparing the difference between PostGraph and openCypher's Cypher Query
  Language and the proposed redesign
---

# Label Redesign

Dating back to PostGraph's parent project, AgensGraph. PostGraph and openCypher had always altered the design of labels to optimize for the implementation of a Graph Query Language in a Relational Database



## openCypher

openCypher Describes a label pattern in this way:

In addition to simply describing the shape of a node in the pattern, one can also describe attributes. The most simple attribute that can be described in the pattern is a label that the node must have. For example:&#x20;

```
(a:User)-[]->(b) 
```

One can also describe a node that has multiple labels:&#x20;

```
(a:User:Admin)-[]->(b)
```

Neo4J, who is the biggest Graph Database on the market has also greatly expanded on this concept.

{% embed url="https://neo4j.com/docs/cypher-manual/current/patterns/reference/#label-expressions" %}

## AgensGraph/Apache AGE/PostGraph

The original concept in AgensGraph is to turn a label into a PostGres relational table. This methodology can work, however in its most basic form allowing multiple labels to be assigned to a vertex or edge means that the search space of a query gets expanded to the entire graph stored in the database. The performance implications of this made the AgensGraph team redesign the concept of a label.

They decided to utilize Postgres' [table inheritance system](https://www.postgresql.org/docs/current/tutorial-inheritance.html). Rather than allow an entity (vertex/edge) to have multiple labels a label can be the parent of another label, allowing a complex hierarchy of labels, as opposed to the complex network of labels that Neo4J and Cypher have.



### The Problem

### No Partitioning

Postgres views the Inheritance system as a form of partitioning, which means that the label inheritance system cannot be combined with other partitioning methodologies. This has significant performance implications and makes integration of other complex systems such as Citus or TimeScaleDB impossible because they require partitioning to be used on tables.

### Compatibility

AgensGraph, Apache AGE, and PostGraph are all incompatible with other systems that implement openCypher and the primary culprit is multi label vs label inheritance and the implied changes to the query language that result from this change.

## Goal

We want to support both systems. In a way that does not compromise performance (to the very best of our ability).

This system should:

* Support a label Inheritance system (not necessarily Postgres Inheritance)
* Support a multi-label system
* Support partitioning of a label

## Solution

### Step 1: Remove Postgres Inheritance

Inheritance, Partitioning, and the UNION clause all are using the same execution logic to do their respective tasks. Functionally there is no difference between `SELECT * FROM parent` and `SELECT * FROM parent UNION ALL SELECT * FROM child` if the parent and child have the same number of columns and the child is in fact a child of the parent table.

We can track this parent/child relation ourselves and make the UNION clauses to combine the tables we need for the query the user provides.

#### Drawbacks

With this system defined as is, multi inheritance is impossible, however there is a new solution in the next step.

### Step 2: Make the Label an LTree

The Postgres extension [LTree](https://www.postgresql.org/docs/current/ltree.html) offers a powerful representation of an inheritance tree. With an ltree you can define extremely complex hierarchical data.

An ltree has a path of labels from a root label that can traversed such as

```
Top.Countries.Europe.Russia
```

We can design this as the label inheritance. If we store a labels representation as an ltree in our system catalog we can query that column to determine what tables to include in our UNION as described in Step 1.

While the above label and this label tree structure are different, we can  still include them in the same query.

<pre><code><strong>Top.Countries.Russia.Europe
</strong></code></pre>

For the query

```
MATCH (n:Russia) RETURN n
```

We can use both label paths `Top.Countries.Europe.Russia` and `Top.Countries.Russia` and all the children of  `Top.Countries.Russia` in the UNION ALL.&#x20;

With this system users can define an inheritance structure of labels if they want or they can adopt the multi label inheritance system of openCypher.

#### Using Label Inheritance

For users wanting the inheritance model, they can enter in the parent/child model via a '`.`'&#x20;

```
MATCH (n:Top.Countries.Europe.Russia) RETURN n
```

this works in a relative manner so the above query and this query&#x20;

```
MATCH (n:Europe.Russia) RETURN n
```

Should return the same results if there is no other `Europe.Russia` in your inheritance model.

#### Using Multi-Label

For users wanting the multi label model. They use a `:` As defined in the openCypher specification

So the Query

```
MATCH (n:Europe:Russia) RETURN n
```

and the query

```
MATCH (n:Russia:Europe) RETURN n
```

will return the data from any label path that has both labels

#### Using an LQuery

To use an lquery as defined in the ltree extension, wrap the query in `''.`

```
MATCH (n:'Top.*{0,2}.sport*@.!football|tennis{1,}.Russ*|Spain') RETURN n
```

This query will run an ltquery on the inheritance hierarchy path and only inlcude the valid labels in the UNION ALL.

## Conclusion&#x20;

This system will allow for a wide variety of ways that a user can model a graph, and it can get very complicated very fast. However, it also offers the most power out of any proposed graph/relational  hybrid model the author is aware of.&#x20;

The ability to use both label inheritance and multi label offers the ability for users to leverage their data and refine the required search space in the most fine-grained way possible, and the user can begin with very simple models as allowed in openCypher and expand in incredible unique ways.

## Next Steps

Define the functionality of the other clauses.

How does CREATE (Top:Countries:Russia:Europe) work. How does MATCH (n:Top:Countries) SET n:German work, etc.&#x20;

Also determine if the more complex Neo4J label expressions can be supported.
