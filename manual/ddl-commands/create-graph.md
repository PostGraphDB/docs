---
description: CREATE GRAPH — define a new graph
---

# CREATE GRAPH

### Synopsis

{% code overflow="wrap" %}
```sql
CREATE GRAPH graph_name [ AUTHORIZATION role_specification ] [ graph_element [ ... ] ]
CREATE GRAPH AUTHORIZATION role_specification [ graph_element [ ... ] ]
CREATE GRAPH IF NOT EXISTS graph_name [ AUTHORIZATION role_specification ]
CREATE GRAPH IF NOT EXISTS AUTHORIZATION role_specification

where role_specification can be:
    user_name
  | CURRENT_ROLE
  | CURRENT_USER
  | SESSION_USER
```
{% endcode %}

### Description

`CREATE GRAPH` enters a new graph into the current database. The graph name must be distinct from the name of any existing schema or graph name in the current database.

A graph is essentially a namespace: it contains named objects (vertices and edges, you can add tables, data types, functions, and operators, but its not recommended) whose names can duplicate those of other objects existing in other graphs. Named objects are accessed either by “qualifying” their names with the graph name as a prefix, or by setting a search path that includes the desired graph(s). A `CREATE` command specifying an unqualified object name creates the object in the current graph(the one at the front of the search path, which can be determined with the function `current_schema`).

Optionally, `CREATE GRAPH` can include subcommands to create objects within the new schema. The subcommands are treated essentially the same as separate commands issued after creating the schema, except that if the `AUTHORIZATION` clause is used, all the created objects will be owned by that user.

### Parameters

_`graph_name`_

The name of a schema to be created. The name cannot begin with `pg_`.

_`user_name`_

The role name of the user who will own the new graph. If omitted, defaults to the user executing the command. To create a graph owned by another role, you must be able to `SET ROLE` to that role.

_`graph_element`_

An SQL statement defining an object to be created within the schema. Currently, only `CREATE TABLE`, `CREATE VIEW`, `CREATE INDEX`, `CREATE SEQUENCE`, `CREATE TRIGGER` and `GRANT` are accepted as clauses within `CREATE SCHEMA`. Other kinds of objects may be created in separate commands after the schema is created.

`IF NOT EXISTS`

Do nothing (except issuing a notice) if a schema with the same name already exists. _`graph_element`_ subcommands cannot be included when this option is used.

### Notes

To create a graph, the invoking user must have the `CREATE` privilege for the current database. (Of course, superusers bypass this check.)

### Examples

Create a graph:

```sql
CREATE GRAPH mygraph;
```

Create a graph for user `joe`; the graph will also be named `joe`:

```sql
CREATE GRAPH AUTHORIZATION joe;
```

Create a graph named `test` that will be owned by user `joe`, unless there already is a graph or schema named `test`. (It does not matter whether `joe` owns the pre-existing graph or schema.)

```sql
CREATE GRAPH IF NOT EXISTS test AUTHORIZATION joe;
```

Create a graph and create a table and view within it:

```sql
CREATE SCHEMA hollywood
CREATE LABEL films (title text, release date, awards text[])
CREATE VIEW winners AS 
    MATCH (f:films)
    WHERE f.awards IS NOT NULL
    RETURN f.title AS title, f.release AS release;
```

Notice that the individual subcommands do not end with semicolons.

The following is an equivalent way of accomplishing the same result:

```sql
CREATE GRAPH hollywood;
CREATE LABEL hollywood.films (title text, release date, awards text[]);
CREATE VIEW hollywood.winners AS
    FROM hollywood 
    MATCH (f:films)
    WHERE f.awards IS NOT NULL
    RETURN f.title AS title, f.release AS release;
```
