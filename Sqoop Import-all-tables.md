# Sqoop Import all tables.


To import all tables from database to HDFS we can use,

`sqoop import-all-tables`

This imports all tables from specified database.

Example,

```
sqoop import-all-tables \
 --connect "jdbc:mysql://localhost:3306/mytrainingdb" \
 --username root \
 --password root \
 --warehouse-dir /sqoop/alltables/ \
 --delete-target-dir 
```
   
**Note**: If there are any tables with no primary key,then we cannot have more than one mappers or we cannot use the default splits.It will fail in mid way.

1.	Each table must have a single-column primary key.
2.	You must intend to import all columns of each table.
3.	You must not intend to use non-default splitting column, nor impose any conditions via a WHERE clause.
4.	No incremental load is possible

We get error as below,

```
18/07/07 19:30:56 ERROR tool.BaseSqoopTool: Error parsing arguments for import-all-tables:
18/07/07 19:30:56 ERROR tool.BaseSqoopTool: Unrecognized argument: --delete-target-dir
```

So while using import-all-tables we cannot use these options.
`--delete-target-dir,--table, --split-by, --columns, and --where`

So, let us run now,

```
sqoop import-all-tables \
 --connect "jdbc:mysql://localhost:3306/mytrainingdb" \
 --username root \
 --password root \
 --warehouse-dir /sqoop/alltables/
```

We will get below sample error if any of the table doesnot have primary key.

```
Note: Recompile with -Xlint:deprecation for details.
18/07/07 19:36:17 INFO orm.CompilationManager: Writing jar file: /tmp/sqoop-hduser/compile/20a4ac617bb7f2680c617173c408ea19/COMPLETED_TXN_COMPONENTS.jar
18/07/07 19:36:17 ERROR tool.ImportAllTablesTool: Error during import: No primary key could be found for table COMPLETED_TXN_COMPONENTS. 
Please specify one with --split-by or perform a sequential import with '-m 1'.
```

In this case we need to use `autoreset-to-one-mapper` which will sure it executes only one mapper if a table doesn't have a primary key


```
 sqoop import-all-tables \
 --connect "jdbc:mysql://localhost:3306/mytrainingdb" \
 --username root \
 --password root \
 --warehouse-dir /sqoop/alltables/ \
 --autoreset-to-one-mapper
`` 

