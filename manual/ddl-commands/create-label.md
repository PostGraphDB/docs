---
description: CREATE LABEL - Creates a Label
---

# CREATE LABEL

{% code overflow="wrap" %}
```sql
CREATE [ TEMP | TEMPORARY ] (VLABEL | ELABEL ) [ IF NOT EXISTS ] label_name IN GRAPH 
[ HAVING 
(NO SCHEMA | SCHEMA { property_name [datatype] [property_constraint [...]] [, ... ]} [ALLOW OTHER PROPERTIES]] 
[PARTITION BY { LIST | LIST | HASH } (property_name | (expression)) [AS HYPERLABEL]]
[INHERITS FROM parent_label] 
[USING LABELSPACE labelspace_name]

where property_constraint:
[ CONSTRAINT constraint_name ]
{ NOT NULL |
  NULL |
  CHECK ( expression ) [ NO INHERIT ] |
  DEFAULT default_expr |
  GENERATED ALWAYS AS ( generation_expr ) STORED |
  UNIQUE [ NULLS [ NOT ] DISTINCT ] index_parameters |
[ DEFERRABLE | NOT DEFERRABLE ] [ INITIALLY DEFERRED | INITIALLY IMMEDIATE ]
```
{% endcode %}
