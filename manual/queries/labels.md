---
description: Primary Categorization System
---

# Labels

Labels are the primary classification system for Vertices and Edge within PostGraph. Labels allow you to classify and reduce the search space that you can check per query. PostGraph takes the definition of labels provided in the openCypher specification and enhances them with the partition management system of TimescaleDB's hypertables and the inheritance query system that Postgres' LTree data type.&#x20;

Labels are case-sensitive.



{% code overflow="wrap" %}
```sql
CREATE [ TEMP | TEMPORARY ] (VLABEL | ELABEL ) [ IF NOT EXISTS ] label_name 
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
  GENERATED { ALWAYS | BY DEFAULT } AS IDENTITY [ ( sequence_options ) ] |
  UNIQUE [ NULLS [ NOT ] DISTINCT ] index_parameters |
[ DEFERRABLE | NOT DEFERRABLE ] [ INITIALLY DEFERRED | INITIALLY IMMEDIATE ]
```
{% endcode %}





{% code overflow="wrap" %}
```
CREATE PARTITION FOR (ELABEL | VLABEL) label_name FOR ( partition_bound_spec | DEFAULT)

and partition_bound_spec is:

IN ( partition_bound_expr [, ...] ) |
FROM ( { partition_bound_expr | MINVALUE | MAXVALUE } [, ...] )
  TO ( { partition_bound_expr | MINVALUE | MAXVALUE } [, ...] ) |
WITH ( MODULUS numeric_literal, REMAINDER numeric_literal )
```
{% endcode %}



