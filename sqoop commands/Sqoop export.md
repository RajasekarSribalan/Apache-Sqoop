# Sqoop Export

This command `sqoop export` is used to upload or export HDFS files to Database.Sqoop export always uses HDFS.It doesnt know hive or anything else.It always reads the HDFS for importing into mysql.The mysql or oracle should have the database before running this command.

Having said that,If we create a table with several joins in hive.We can verify the hdfs location of the table by querying `describe formatted tablename` which will list the hdfs location.

That location has to be given in the export command.

I am creating a table in hive.and by  "describe formatted temphive".We will get the hdfs location.
hdfs://localhost:54310/user/hive/warehouse/temphivetable.db/temphive

Before running the command,we need to create a table in mysql.because sqoop export will not create a table dynamically like import.We need to create it explicitly.

It should have same number of columns but can have different names.

`create table tempmysql_hive (sno int,myid int,firstname varchar(30),gender varchar(30));`

We need to consider delimiter also.Because in hive the column is delimited by `ctrl+A`.But sqoop will expect to be `(,)`.So we need to pass an control argument so that sqoop export can understand the delimiter.

```
sqoop export \
 --connect "jdbc:mysql://localhost:3306/mytrainingdb" \
 --username root \
 --password root \
 --export-dir /user/hive/warehouse/temphivetable.db/temphive \
 --table tempmysql_hive \
 --input-fields-terminated-by "\001"
```

The above command has inserted from hdfs to mysql

**what if I run this command again?**

It has appended to the mysql .But there was no primary key present in the mysql.So what will happen if I insert the same record to the mysql table.Need to check.


## num of mappers in export

It is purely depends on hadoop fs block size concept.Number of splits are based on block size

But still we can mention --num-mappers


## Exchanging column names

Create another table by exchanging the columns as below.
```
create table tempmysql_hive_demo (firstname varchar(30),gender varchar(30),sno int,myid int);
```

```
sqoop export \
 --connect "jdbc:mysql://localhost:3306/mytrainingdb" \
 --username root \
 --password root \
 --export-dir /user/hive/warehouse/temphivetable.db/temphive \
 --table tempmysql_hive_demo \
 --input-fields-terminated-by "\001"
```

It failed

```
18/07/07 21:36:20 ERROR tool.ExportTool: Error during export: 
Export job failed!
	at org.apache.sqoop.mapreduce.ExportJobBase.runExport(ExportJobBase.java:445)
	at org.apache.sqoop.manager.SqlManager.exportTable(SqlManager.java:931)
	at org.apache.sqoop.tool.ExportTool.exportTable(ExportTool.java:80)
	at org.apache.sqoop.tool.ExportTool.run(ExportTool.java:99)
	at org.apache.
```

## Different scenario - we will have same type of columns but with extra column

```
create table tempmysql_hive (sno int,myid int,firstname varchar(30),gender varchar(30),city varchar(20));

sqoop export \
 --connect "jdbc:mysql://localhost:3306/mytrainingdb" \
 --username root \
 --password root \
 --export-dir /user/hive/warehouse/temphivetable.db/temphive \
 --table tempmysql_hive_1 \
 --input-fields-terminated-by "\001"

It is inserted but extra column has null values.

```


## Let us create a same table with not null type column

```
create table tempmysql_hive_2 (sno int,myid int,firstname varchar(30),gender varchar(30),city varchar(20) not null);

sqoop export \
 --connect "jdbc:mysql://localhost:3306/mytrainingdb" \
 --username root \
 --password root \
 --export-dir /user/hive/warehouse/temphivetable.db/temphive \
 --table tempmysql_hive_2 \
 --input-fields-terminated-by "\001"

it FAILED 
```

## Now let us try  the same scenario with --columns

Create another table by exchanging the columns as below.

```
create table tempmysql_hive_demo (firstname varchar(30),gender varchar(30),sno int,myid int);
```

--columns in target directory not the source directory

````
sqoop export \
 --connect "jdbc:mysql://localhost:3306/mytrainingdb" \
 --username root \
 --password root \
 --columns sno,myid,firstname,gender \
 --export-dir /user/hive/warehouse/temphivetable.db/temphive \
 --table tempmysql_hive_demo \
 --input-fields-terminated-by "\001"
```



