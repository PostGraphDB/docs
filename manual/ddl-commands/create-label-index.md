---
description: CREATE LABEL INDEX - define a new index on a labeltree
---

# CREATE LABEL INDEX

CREATE \[

```plsql
CREATE ( VLABEL | ELABEL ) [ UNIQUE ] INDEX [ CONCURRENTLY ] [ [ IF NOT EXISTS] name ] ON [ ONLY ] [ label_name | ltree | lquery ] [ USING method ]
( { property_name | ( expression ) } [ COLLATE collation ] [ opclass [ ( opclass_parameter = value [, ... ] ) ] ] [ ASC | DESC ] [ NULLS { FIRST | LAST } ] [, ...] )
    [ INCLUDE ( column_name [, ...] ) ]
    [ NULLS [ NOT ] DISTINCT ]
    [ WITH ( storage_parameter [= value] [, ... ] ) ]
    [ TABLESPACE tablespace_name ]
    [ WHERE predicate ]
```



## Description

`CREATE LABEL INDEX` constructs an index on the specified properties of the specified label, ltree or lquery. Indices are primarily used to enhance database performance (though inappropriate use can result in slower performance).

The key field(s) for the index are specified property names, or alternatively as expressions written in parentheses. Multiple fileds can be specified if the index method supports multi-property indices.

An index field can be used as an expression computed from the values of one or more properties of the label row. This feature can be used to obtain fast access to data based on some transformation of the basic data. For example, an index computed on `upper(properties->prop)` would allow the clause the clause `WHERE upper(e->'prop') = 'JIM'` to use an index, where e is the name assigned to the vertex or edge.

PostGraph provides the index methods B-tree, hash, GiST, SP-GiST, GIN, BRIN, Bloom, IvfFlat, HSNW, and PropertyStore.

When the WHERE clause is present, a partial index is created. A partial index is an index that contains entries for only a portion of the label, usually a portion that is more useful for indexing than the rest of the label. For example, if you have a table that contains both billed and unbilled orders where the unbilled orders take a small fraction, but often used, amount of the total label, you can improve performance by creating an index on just that portion. Another possible application is to use `WHERE` with `UNIQUE` to enforce uniqueness over a subset of a table.&#x20;

Presently, subqueries and aggregate expressions are also forbidden in `WHERE`. The same restrictions apply to index fields that are expressions.

All functions and operators used in an index definition must be “immutable”, that is, their results must depend only on their arguments and never on any outside influence (such as the contents of another table or the current time). This restriction ensures that the behavior of the index is well-defined. To use a user-defined function in an index expression or `WHERE` clause, remember to mark the function immutable when you create it.
