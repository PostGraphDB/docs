---
description: DROP GRAPH — remove a graph
---

# DROP GRAPH

### Synopsis

```
DROP GRAPH [ IF EXISTS ] name [, ...] [ CASCADE | RESTRICT ]
```

### Description

`DROP GRAPH` removes graphs from the database.

A graph can only be dropped by its owner or a superuser. Note that the owner can drop the graph (and thereby all contained objects) even if they do not own some of the objects within the graph.

### Parameters

`IF EXISTS`

Do not throw an error if the graph does not exist. A notice is issued in this case.

_`name`_

The name of a graph.

`CASCADE`

Automatically drop objects (vertices, edges, tables, functions, etc.) that are contained in the schema, and in turn all objects that depend on those objects (see [Section 5.14](https://www.postgresql.org/docs/16/ddl-depend.html)).

`RESTRICT`

Refuse to drop the graph if it contains any objects. This is the default.

### Notes

Using the `CASCADE` option might make the command remove objects in other graphs besides the one(s) named.

### Examples

To remove graph `mystuff` from the database, along with everything it contains:

```
DROP GRAPH mystuff CASCADE;
```
