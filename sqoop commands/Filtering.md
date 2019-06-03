# Filtering

Below are different ways for applying filtering while sqoop import.

	1. Using Boundary queries
	2. Specifying columns.
	3. Using query
	

## Boundary-query

While doing sqoop import,sqoop splits the data based on number of mappers.As you know,4 is the defaut number of mappers and hence,there will 4 splits of data.How does sqoop split the data?.The answer is ,`sqoop generates a boundary query on its own` and split the data into 4 mutual exclusive subset.

Let us have a look on the default behaviour.Now I run a simple sqoop import.

```
sqoop import \
  --connect jdbc:mysql://ms.itversity.com:3306/retail_db \
  --username retail_user \
  --password itversity \
  --table order_items \
  --warehouse-dir /user/rajasekar/sqoop_import/retail_db \
  --delete-target-dir
```

When you notice the execution,you will see the below log.

```
19/06/02 05:57:08 INFO db.DBInputFormat: Using read commited transaction isolation
19/06/02 05:57:08 INFO db.DataDrivenDBInputFormat: BoundingValsQuery: SELECT MIN(`order_item_id`), MAX(`order_item_id`) FROM `order_items`
19/06/02 05:57:08 INFO db.IntegerSplitter: Split size: 43049; Num splits: 4 from: 1 to: 172198
19/06/02 05:57:08 INFO mapreduce.JobSubmitter: number of splits:4
```

Sqoop runs the boundary query on the table and calculates the min and max of the primary key, and splits the data into 4 parts.This is the default behaviour.

Consider a case,we want to customize the split logic or discard some unwanted data in the boundary query or apply some extra filter on the boundary query.In such cases,we can pass our own query in the option `--boundary-query` as below.The given range is applied only on the primary key.

```
sqoop import \
  --connect jdbc:mysql://ms.itversity.com:3306/retail_db \
  --username retail_user \
  --password itversity \
  --table order_items \
  --warehouse-dir /user/rajasekar/sqoop_import/retail_db \
  --boundary-query 'select 100000, 172198' \
  --delete-target-dir
```
Now notice the console,

```
19/06/02 06:01:49 INFO db.DataDrivenDBInputFormat: BoundingValsQuery: select 100000, 172198
19/06/02 06:01:49 INFO db.IntegerSplitter: Split size: 18049; Num splits: 4 from: 100000 to: 172198
19/06/02 06:01:50 INFO mapreduce.JobSubmitter: number of splits:4
```

Our custom boundary query is applied.So we can use --boundary-query to customize the split logic.
Note:Boundary query can be applied only in primary key

## Specifying columns.

Consider there is a requirement where we need to import only few columns from the table rather than importing all columns.

We can use an option `--columns`

Example,

```
sqoop import \
 --connect "jdbc:mysql://localhost:3306/mytrainingdb" \
 --username root \
 --password root \
 --table temptable \
 --columns name,id \
 --warehouse-dir /sqoop/test_13/ \
--delete-target-dir 
```
## Query

If we wan to join a table and import the data ,or if more complex query to be used to import data.In such cases,we can specify the custom query using `--query`

If we want to join tables or if i want to have many joins,we need use --query because we cannot mention both table names in --table.

Example,

```
Join query : select * from temptable t,address a where t.id = a.addressid;

sqoop import \
 --connect "jdbc:mysql://localhost:3306/mytrainingdb" \
 --username root \
 --password root \
 --warehouse-dir /sqoop/test_13/ \
 --delete-target-dir \
 --num-mappers 1 \
 --query "select * from temptable t,address a where t.id = a.addressid

```


The above command will throw error becuase if we use --query we cannot use --warehouse-dir ,because we are joining two tables and --warehouse-dir will have to create a folder with the table name.Since we are using joins it is not possible to create a folder with table name.

Hence in this case we need to `use --target-dir`

```
sqoop import \
 --connect "jdbc:mysql://localhost:3306/mytrainingdb" \
 --username root \
 --password root \
 --target-dir /sqoop/test_13/ \
 --delete-target-dir \
 --num-mappers 1 \
 --query "select * from temptable t,address a where t.id = a.addressid

```

But still the updated command will throw an error as below

```
8/07/06 08:01:15 INFO manager.MySQLManager: Preparing to use a MySQL streaming resultset.
18/07/06 08:01:15 INFO tool.CodeGenTool: Beginning code generation
18/07/06 08:01:15 ERROR tool.ImportTool: Import failed: java.io.IOException: Query [select * from temptable t,address a where t.id = a.addressid] must contain '$CONDITIONS' in WHERE clause.
	at org.apache.sqoop.manager.ConnManager.getColumnTypes(ConnManager.java:332)
	at org.apache.sqoop.orm.ClassWriter.getColumnTypes(ClassWriter.java:1872)

```

So if we are using --query ,we need add `$CONDITIONS`. This is need for sqoop to run and doesnot mean any logic to us.It is how sqoop works.

So,
```
sqoop import \
 --connect "jdbc:mysql://localhost:3306/mytrainingdb" \
 --username root \
 --password root \
 --target-dir /sqoop/test_13/ \
 --delete-target-dir \
 --num-mappers 1 \
 --query "select t.id,t.name,t.gender,a.addname,a.city from temptable t,address a where t.id = a.addressid and \$CONDITIONS"
```

The above command works will give a result with a join.

Pleas note,we are using --num-mappers as 1.So no issue.

However if we remove and run the command.

```
sqoop import \
 --connect "jdbc:mysql://localhost:3306/mytrainingdb" \
 --username root \
 --password root \
 --target-dir /sqoop/test_13/ \
 --delete-target-dir \
 --query "select t.id,t.name,t.gender,a.addname,a.city from temptable t,address a where t.id = a.addressid and \$CONDITIONS"
```

We will get below error,

```
Please set $ZOOKEEPER_HOME to the root of your Zookeeper installation.
18/07/06 08:16:42 INFO sqoop.Sqoop: Running Sqoop version: 1.4.7
18/07/06 08:16:42 WARN tool.BaseSqoopTool: Setting your password on the command-line is insecure. Consider using -P instead.
When importing query results in parallel, you must specify --split-by.
Try --help for usage instructions.
```

So,add `--split-by` to tell sqoop to know the primary key to be used for splits

```
sqoop import \
 --connect "jdbc:mysql://localhost:3306/mytrainingdb" \
 --username root \
 --password root \
 --target-dir /sqoop/test_13/ \
 --delete-target-dir \
 --query "select t.id,t.name,t.gender,a.addname,a.city from temptable t,address a where t.id = a.addressid and \$CONDITIONS" \
 --split-by t.id
```

Please note in the above command, we cannot give the column name as "id" in --split-by instaed we need to give t.id.

Let us try with allias as below,

```
sqoop import \
 --connect "jdbc:mysql://localhost:3306/mytrainingdb" \
 --username root \
 --password root \
 --target-dir /sqoop/test_13/ \
 --delete-target-dir \
 --query "select t.id as id,t.name,t.gender,a.addname,a.city from temptable t,address a where t.id = a.addressid and \$CONDITIONS" \
 --split-by id
```

It is still throwin error with even alias.So we need to give t.id

### Points to be noted:

	1. table and/or columns are mutualy exclusive
	2. Either you can only table
	3. Either you can have both table and column
	4. Either you can only have query
	5. For --query, --split-by is mandatory if num-mappers is greated than 1.
	6. --query should have have a placeholder for \$CONDITIONS with backward slash

