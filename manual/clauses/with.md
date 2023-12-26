# WITH





```plsql
 WITH [ ALL | DISTINCT [ ON ( expression [, ...] ) ] ]
* | expression [AS output_name] [, ...]}
[ WHERE condition ]
[ GROUP BY [ ALL | DISTINCT ] grouping_element  [HAVING expresion [,...]] ]
[ WINDOW window_name AS ( window_definition ) [, ...] ]
[ ORDER BY expression [ ASC | DESC | USING operator ] [ NULLS { FIRST | LAST } ] [, ...] ]
[ LIMIT { count | ALL } ]
[ OFFSET start [ ROW | ROWS ] ]
[ FETCH { FIRST | NEXT } [ count ] { ROW | ROWS } { ONLY | WITH TIES } ]

WHERE grouping_element can be one of:
    ( )
    expression
    ( expression [, ...] )
    ROLLUP ( { expression | ( expression [, ...] ) } [, ...] )
    CUBE ( { expression | ( expression [, ...] ) } [, ...] )
    GROUPING SETS ( grouping_element [, ...] )
```
