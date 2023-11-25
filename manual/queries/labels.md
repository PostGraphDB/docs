---
description: Primary Categorization System
---

# Labels

Labels are the primary classification system for Vertices and Edge within PostGraph. Labels allow you to classify and reduce the search space that you can check per query. PostGraph takes the definition of labels provided in the openCypher specification and enhances them with the partition management system of TimescaleDB's hypertables and the inheritance query system that Postgres' LTree data type.&#x20;

## Defintion

A _label_ is a sequence of alphanumeric characters, underscores, and hyphens. Valid alphanumeric character ranges are dependent on the database locale. For example, in C locale, the characters `A-Za-z0-9_-` are allowed. Labels must be no more than 1000 characters long. Labels must begin with a `A-Za-z_`

Examples: `Personal_Services`

A _label path_ is a sequence of zero or more labels separated by dots, for example `L1.L2.L3`, representing a path from the root of a hierarchical tree to a particular node. The length of a label path cannot exceed 65535 labels.

Example: `Top.Countries.Europe.Russia`

&#x20;represents a regular-expression-like pattern for matching `labels`. A simple word matches that label within a path. A star symbol (`*`) matches zero or more labels. These can be joined with dots to form a pattern that must match the whole label path. For example:



```
foo         Match the exact label path foo
*.foo.*     Match any label path containing the label foo
*.foo       Match any label path whose last label is foo
```

Both star symbols and simple words can be quantified to restrict how many labels they can match:

```
*{n}        Match exactly n labels
*{n,}       Match at least n labels
*{n,m}      Match at least n but not more than m labels
*{,m}       Match at most m labels — same as *{0,m}
foo{n,m}    Match at least n but not more than m occurrences of foo
foo{,}      Match any number of occurrences of foo, including zero
```

In the absence of any explicit quantifier, the default for a star symbol is to match any number of labels (that is, `{,}`) while the default for a non-star item is to match exactly once (that is, `{1}`).

There are several modifiers that can be put at the end of a non-star `lquery` item to make it match more than just the exact match:

```
@           Match case-insensitively, for example a@ matches A
*           Match any label with this prefix, for example foo* matches foobar
%           Match initial underscore-separated words
```

The behavior of `%` is a bit complicated. It tries to match words rather than the entire label. For example `foo_bar%` matches `foo_bar_baz` but not `foo_barbaz`. If combined with `*`, prefix matching applies to each word separately, for example `foo_bar%*` matches `foo1_bar2_baz` but not `foo1_br2_baz`.

Also, you can write several possibly-modified non-star items separated with `|` (OR) to match any of those items, and you can put `!` (NOT) at the start of a non-star group to match any label that doesn't match any of the alternatives. A quantifier, if any, goes at the end of the group; it means some number of matches for the group as a whole (that is, some number of labels matching or not matching any of the alternatives).

Here's an annotated example of `lquery`:

<pre><code><strong>MATCH (a:'Top.*{0,2}.sport*@.!football|tennis{1,}.Russ*|Spain') RETURN a
</strong>          a.  b.     c.      d.                   e.
</code></pre>

This query will match any label path that:

1. begins with the label `Top`
2. and next has zero to two labels before
3. a label beginning with the case-insensitive prefix `sport`
4. then has one or more labels, none of which match `football` nor `tennis`
5. and then ends with a label beginning with `Russ` or exactly matching `Spain`.

`ltxtquery` represents a full-text-search-like pattern for matching `ltree` values. An `ltxtquery` value contains words, possibly with the modifiers `@`, `*`, `%` at the end; the modifiers have the same meanings as in `lquery`. Words can be combined with `&` (AND), `|` (OR), `!` (NOT), and parentheses. The key difference from `lquery` is that `ltxtquery` matches words without regard to their position in the label path.

Here's an example `ltxtquery`:

```
MATCH (a:'Europe & Russia*@ & !Transportation') RETURN a
```

This will match paths that contain the label `Europe` and any label beginning with `Russia` (case-insensitive), but not paths containing the label `Transportation`. The location of these words within the path is not important. Also, when `%` is used, the word can be matched to any underscore-separated word within a label, regardless of position.

{% hint style="info" %}
Whitespace is not allowed in labels
{% endhint %}



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



MATCH (n) CREATE (:n.ltree)
