---
description: DROP LABEL TRIGGER — remove a trigger
---

# DROP LABEL TRIGGER

### Synopsis

```
DROP LABEL TRIGGER [ IF EXISTS ] name ON graph_name [ CASCADE | RESTRICT ]
```

### Description

`DROP TRIGGER` removes an existing trigger definition. To execute this command, the current user must be the owner of the table for which the trigger is defined.

### Parameters

`IF EXISTS`

Do not throw an error if the trigger does not exist. A notice is issued in this case.

_`name`_

The name of the trigger to remove.

_`label_name`_

The name (optionally schema-qualified) of the label for which the trigger is defined.

`CASCADE`

Automatically drop objects that depend on the trigger, and in turn all objects that depend on those objects (see [Section 5.14](https://www.postgresql.org/docs/current/ddl-depend.html)).

`RESTRICT`

Refuse to drop the trigger if any objects depend on it. This is the default.

### Examples

Destroy the trigger `if_dist_exists` on the label `films`:

```
DROP TRIGGER if_dist_exists ON films;
```

