# Hive import

One of the main feature of sqoop is to import data into HDFS from databases.However if you have Hive and hive metastore associated with your HDFS then sqoop can directly import to that location.Sqoop will automatically create table schema to define hive table.

We can use `--hive-import` option to import directly to hive.

	1.hive-import will enable hive import. It creates table if it does not exists.If the Hive table already exists, you can specify the --hive-overwrite option to indicate that existing table in hive must be replaced.
	2.hive-database can be used to specify the database
	3.Instead of –hive-database, we can use database name as prefix as part of –hive-table
	
Note:

By default the sqoop delimeter is (,)
By default the hive delimeter is (^A) ctrl+A

The below sqoop hive import command is used to import directly to hive.This will import data to hive managed metastore.

```
sqoop import \
 --connect "jdbc:mysql://localhost:3306/mytrainingdb" \
 --username root \
 --password root \
 --table temptable \
 --hive-import \
 --hive-database hduser_sqoop_import \
 --hive-table temptable_hive/ \
 --num-mappers 2
```

Once the above command is run,we can see a folder with databasename/table name/Part file  will be created in the below path(hive metastore)
 
```
/user/hive/warehouse/hduser_sqoop_import/temptable_hive/00000
```

Pleaset note,sqoop first put the data in a temp directory of hdfs and from there it is moved to hive table.

# Managing tables



	1.IF we run the same above sqoop import once again, the data will be appended.
	2.So by default it will be appended.
	3.If we can to ovewrite we need to add below command
			`--hive-overwrite`

It will drop and recreate the table and load the data again.

```
sqoop import \
 --connect "jdbc:mysql://localhost:3306/mytrainingdb" \
 --username root \
 --password root \
 --table temptable \
 --hive-import \
 --hive-database hduser_sqoop_import \
 --hive-table temptable_hive/ \
 --hive-overwrite \
 --num-mappers 2
```

If we want to throw an error if a table is already present,then use the below control argument.

```
--create-hive-table will fail hive import, if table already exists
```
the error will be thrown at the last step before hive import.but the data will be in staging directory. this is an issue with sqoop.

so we need to manually delete it.
