# Sqoop import

Sqoop import command is used to import data to HDFS from RDBMS such Oracle,MySql etc.

Below is the sample import command.


```
sqoop import \
  --connect jdbc:mysql://ms.itversity.com:3306/retail_db \
  --username retail_user \
  --password itversity \
  --table order_items \
  --target-dir /user/rajasekar/sqoop_import/retail_db/order_items

```

connect -> database connection detail
table -> Source table name
target-dir -> Destination file system folder.


--target-dir will create part files in the specified path.

```
[rajasekar@gw02 ~]$ hadoop fs -ls /user/rajasekar/sqoop_import/retail_db/order_items
Found 5 items
-rw-r--r--   2 rajasekar hdfs          0 2019-06-01 04:02 /user/rajasekar/sqoop_import/retail_db/order_items/_SUCCESS
-rw-r--r--   2 rajasekar hdfs    1303818 2019-06-01 04:02 /user/rajasekar/sqoop_import/retail_db/order_items/part-m-00000
-rw-r--r--   2 rajasekar hdfs    1343222 2019-06-01 04:02 /user/rajasekar/sqoop_import/retail_db/order_items/part-m-00001
-rw-r--r--   2 rajasekar hdfs    1371917 2019-06-01 04:02 /user/rajasekar/sqoop_import/retail_db/order_items/part-m-00002
-rw-r--r--   2 rajasekar hdfs    1389923 2019-06-01 04:02 /user/rajasekar/sqoop_import/retail_db/order_items/part-m-00003

```

## --warehouse-dir

warehouse dir will create a folder with the name of the table nam provided in the command and files will be created inside that folder.


```
sqoop import \
  --connect jdbc:mysql://ms.itversity.com:3306/retail_db \
  --username retail_user \
  --password itversity \
  --table order_items \
  --warehouse-dir /user/rajasekar/sqoop_import/retail_db

```

Output

```

[rajasekar@gw02 ~]$ hadoop fs -ls /user/rajasekar/sqoop_import/retail_db
Found 1 items
drwxr-xr-x   - rajasekar hdfs          0 2019-06-01 04:06 /user/rajasekar/sqoop_import/retail_db/order_items


[rajasekar@gw02 ~]$ hadoop fs -ls /user/rajasekar/sqoop_import/retail_db/order_items
Found 5 items
-rw-r--r--   2 rajasekar hdfs          0 2019-06-01 04:06 /user/rajasekar/sqoop_import/retail_db/order_items/_SUCCESS
-rw-r--r--   2 rajasekar hdfs    1303818 2019-06-01 04:06 /user/rajasekar/sqoop_import/retail_db/order_items/part-m-00000
-rw-r--r--   2 rajasekar hdfs    1343222 2019-06-01 04:06 /user/rajasekar/sqoop_import/retail_db/order_items/part-m-00001
-rw-r--r--   2 rajasekar hdfs    1371917 2019-06-01 04:06 /user/rajasekar/sqoop_import/retail_db/order_items/part-m-00002
-rw-r--r--   2 rajasekar hdfs    1389923 2019-06-01 04:06 /user/rajasekar/sqoop_import/retail_db/order_items/part-m-00003


```
