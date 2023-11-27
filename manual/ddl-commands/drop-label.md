---
description: DROP LABEL — remove a label
---

# DROP LABEL

### Synopsis

```
DROP LABEL[ IF EXISTS ] absolute_name [, ...] [ CASCADE | RESTRICT ]
```

### Description

`DROP LABEL` removes labelsfrom the database. Only the table owner, the label owner, and superuser can drop a table. To empty a table of rows without destroying the label, use [`DELETE`](https://www.postgresql.org/docs/16/sql-delete.html) or [`TRUNCATE`](https://www.postgresql.org/docs/16/sql-truncate.html).

`DROP LABEL` always removes any indexes, rules, triggers, and constraints that exist for the target table. However, to drop a label that is referenced by a view or a foreign-key constraint of another table, `CASCADE` must be specified. (`CASCADE` will remove a dependent view entirely, but in the foreign-key case it will only remove the foreign-key constraint, not the other table entirely.)

### Parameters

`IF EXISTS`

Do not throw an error if the label does not exist. A notice is issued in this case.

_`absolute_name`_

The absolute path of the name of the label to drop.

`CASCADE`

Automatically drop objects that depend on the label (such as views), and in turn all objects that depend on those objects (see [Section 5.14](https://www.postgresql.org/docs/16/ddl-depend.html)).

`RESTRICT`

Refuse to drop the label if any objects depend on it. This is the default.

### Examples

To destroy two labels, `films` and `distributors`:

```
DROP LABEL films, distributors;
```
