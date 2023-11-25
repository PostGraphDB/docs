# Existence Check Subqueries

PostGraph has a set of boolean Subqueries, where the subquery is evaluated to determine whether it returns rows and returns true or false, based on the type of existence subquery used.

## EXISTS

```
EXISTS (subquery)
```

The argument of `EXISTS` is an arbitrary sql `SELECT` statement, cypher statement that `RETURN`s results. The subquery is evaluated to determine whether it returns any rows. If it returns at least one row, the result of `EXISTS` is “true”; if the subquery returns no rows, the result of `EXISTS` is “false”.

The subquery can refer to variables from the surrounding query, which will act as constants during any one evaluation of the subquery.

The subquery will generally only be executed long enough to determine whether at least one row is returned, not all the way to completion. It is unwise to write a subquery that has side effects (such as calling sequence functions or calling CREATE, SET, REMOVE, DELETE, or MERGE); whether the side effects occur might be unpredictable.

Since the result depends only on whether any rows are returned, and not on the contents of those rows, the output list of the subquery is normally unimportant. A common coding convention is to write all `EXISTS` tests in the form `EXISTS(MATCH ... RETURN ...)`. There are exceptions to this rule however, such as subqueries that use `INTERSECT`.

This simple example is like an inner join on `prop2`, but it produces at most one output row for each `label1` row, even if there are several matching `label2` rows:

{% code overflow="wrap" %}
```
MATCH (v1:label1)
WHERE EXISTS (
    MATCH (v2:label2) 
    WHERE v1.prop1 = v2.prop2
)
RETURN v1.prop1
```
{% endcode %}

## IN





## NOT IN





## ANY/SOME





## ALL



## Single-Row Comparsion



