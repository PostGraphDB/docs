# Query Engine Redesign

There have been some issues that have existed in PostGraph's design for the past 4 years ago when it was called AgensGraph-Extension.&#x20;

Since the Postgres hook system can't be used until after the parser is run, we have had to hide the query from Postgres. One of the biggest complaints users have in how everyone the query gets wrapped around with the `SELECT * FROM cypher('', $$ ... $$) AS (a gtype);` Also, DDL commands have to be run in functions, which causes a number of usability problems such as indices cannot be created concurrently.&#x20;

The original idea I had was to create a piece of middleware that sat between the client and Postgres, which takes a cypher or hybrid query and transforms it into something that Postgres can process without syntax errors... which always sounded like a painful experience to get working right.&#x20;

Instead, create a process that listens on another port and PostGraph can process the query itself. This lets PostGraph bypass the Postgres' parser and transform logic. First, just copy the Postgres Frontend/Backend Protocol and model Postgres' query processing engine exactly the same, with a different parser and transform logic. That way the`USE GRAPH;` command and real DDL commands can be implemented, performance issues on DDL commands can be resolved,  the integration of SQL and Cypher (with a lot of copy/paste from Postgres source code for the sql side) is greatly simplified and has a cleaner interface, and all Postgres drivers, connection poolers, etc can be supported.&#x20;

Next, support the Bolt connection protocol so Neo4J applications can be used with PostGraph. This in conjunction with the [Label Redesign](label-redesign.md) project will allow applications designed for Neo4J to be usable with PostGraph. (Plus, adding more language features from Cypher).&#x20;

This also leads into some more backend designs to plan after, the primary concern for other designs right now is that Cypher needs sessions between the client and server to know which graph it is supposed to be querying against. That might get fixed by forcing people using these other backend designs to use the `FROM` clause which exists in several proposed specs for multi graph queries, but not implemented anywhere, or maybe a set of processes that handle each requests to each graph and clients register which graph they are using, and when a client changes graph a the process assigned to handle that request will alter its registration information. Not sure about that, this idea is in it's infancy.
