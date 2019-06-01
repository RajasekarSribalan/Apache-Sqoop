# Customizing number of mappers.

Internally sqoop imports convert the processing logic or query to `mapr-reduce`.The parallelism of mapr-reduce depends on number of split.By default,the number of input splits is 4 in sqoop import.Hence 4 mappes will be running parallely.

```
Job Counters 
		Launched map tasks=4
		Other local map tasks=4
		Total time spent by all maps in occupied slots (ms)=40122
		Total time spent by all reduces in occupied slots (ms)=0
		Total time spent by all map tasks (ms)=20061
		Total vcore-milliseconds taken by all map tasks=20061
		Total megabyte-milliseconds taken by all map tasks=41084928
```

In order to customize or change the default number of mappers,we can set an option `--num-mappers` to pur choice or based on the cluster configuration.

Example,

```
sqoop import \
  --connect jdbc:mysql://ms.itversity.com:3306/retail_db \
  --username retail_user \
  --password itversity \
  --table order_items \
  --warehouse-dir /user/rajasekar/sqoop_import/retail_db \
  --delete-target-dir \
  --num-mappers 1

```

So only mapper is launched.

```
	Job Counters 
		Launched map tasks=1
		Other local map tasks=1
		Total time spent by all maps in occupied slots (ms)=11302
		Total time spent by all reduces in occupied slots (ms)=0
		Total time spent by all map tasks (ms)=5651
		Total vcore-milliseconds taken by all map tasks=5651
		Total megabyte-milliseconds taken by all map tasks=11573248
```


The number of mappers which is equal to number of splits will be applied on primary key if exists.

If a table dosn't have primary key and if number of mappers is more than one then the sqoop import execution will fail with below error.

```
Import failed: No primary key could be found for table temp_nopk. Please specify one with --split-by or perform a sequential import with '-m 1'.
```

`So primary key is mandatory for mappers more than 1.`

So what to do for a table which doesnt have primary key.

Option 1: Set --num-mappers to 1
Option 2: Use --split-by option

## split-by

1. split-by option can be applied if the table to be imported doesnt have primary key and if we need more mappers to be run.
2. split-by can be applied for numerical columns(Integer based columns).If we need to apply split-by to string based columns,then add an extra option 
	`--Dorg.apache.sqoop.splitter.allow_text_splitter=true ` else below error will be thrown and sqoop import will fail.
	
	```
	Import failed: java.io.IOException: Generating splits for a textual index column allowed only in case of "-Dorg.apache.sqoop.splitter.allow_text_splitter=true" property passed as a parameter
	```
3. Column needs to be indexed if split-by is applied.Because it is costlier operation else whole table will be scanned.
4. If the column has null values,those will be ignored.

```
sqoop import \
	--connect "jdbc:mysql://localhost:3306/mytrainingdb" \
	--username root \
	--password root \
	--table temp_nopk \
	--warehouse-dir /sqoop/nopk_1/ \
	--split-by id \
	--delete-target-dir


```

## Auto reset to one mapper.

`--autoreset-to-one-mapper ` Auto reset to one mapper when no primary key is available..This can be added in the import command and it will check if the table has the primary key,if not it will set the mapped to 1.

```
sqoop import
--connect "jdbc:mysql://localhost:3306/mytrainingdb"
--username root
--password root
--table temp_nopk
--warehouse-dir /sqoop/nopk_1/
--autoreset-to-one-mapper
--delete-target-dir

```



