# File format

The default file format is `text file`.No need to explicitly specify this option.

Sqoop supports various file formats as well.

1. text file (default)
2. Sequence file (binary file format)
3. Avro (binary json format)
4. Parquet (columnar file format)
5. ORC


## Sequence file format

Use `--as-sequencefile` option in the sqoop import command to save the output in sequence file format.

```
sqoop import \
  --connect jdbc:mysql://ms.itversity.com:3306/retail_db \
  --username retail_user \
  --password itversity \
  --table order_items \
  --warehouse-dir /user/rajasekar/sqoop_import/retail_db \
  --num-mappers 2 \
  --delete-target-dir \
  --as-sequencefile
```

Output 

```
[rajasekar@gw02 ~]$ hadoop fs -ls /user/rajasekar/sqoop_import/retail_db/*
Found 3 items
-rw-r--r--   2 rajasekar hdfs          0 2019-06-01 10:54 /user/rajasekar/sqoop_import/retail_db/order_items/_SUCCESS
-rw-r--r--   2 rajasekar hdfs    3999746 2019-06-01 10:54 /user/rajasekar/sqoop_import/retail_db/order_items/part-m-00000
-rw-r--r--   2 rajasekar hdfs    3999746 2019-06-01 10:54 /user/rajasekar/sqoop_import/retail_db/order_items/part-m-00001

```

## Parquet file format

Use `--as-Parquet` option in the sqoop import command to save the output in parquet file format.

```
sqoop import \
 --connect "jdbc:mysql://localhost:3306/mytrainingdb" \
 --username root \
 --password root \
 --table temptable \
 --warehouse-dir /sqoop/temp_parq/ \
 --as-parquetfile \
 --delete-target-dir
```

# Compression


Buy default ,compression is diabled.

It can be enabled using `--compress` in the import command.

And by default `gzip algorithm` is the compression-codec

Compression can be applied for any file format.

```
  --compression-codec org.apache.hadoop.io.compress.GzipCodec
  --compression-codec org.apache.hadoop.io.compress.SnappyCodec
```

## GzipCodec

```

sqoop import \
  --connect jdbc:mysql://ms.itversity.com:3306/retail_db \
  --username retail_user \
  --password itversity \
  --table order_items \
  --warehouse-dir /user/rajasekar/sqoop_import/retail_db \
  --num-mappers 2 \
  --delete-target-dir \
  --as-textfile \
  --compress \
  --compression-codec org.apache.hadoop.io.compress.GzipCodec

```
Output

```
[rajasekar@gw02 ~]$ hadoop fs -ls /user/rajasekar/sqoop_import/retail_db/*
Found 3 items
-rw-r--r--   2 rajasekar hdfs          0 2019-06-01 11:13 /user/rajasekar/sqoop_import/retail_db/order_items/_SUCCESS
-rw-r--r--   2 rajasekar hdfs     516790 2019-06-01 11:13 /user/rajasekar/sqoop_import/retail_db/order_items/part-m-00000.gz
-rw-r--r--   2 rajasekar hdfs     516080 2019-06-01 11:13 /user/rajasekar/sqoop_import/retail_db/order_items/part-m-00001.gz
```

## Snappy codec

```
sqoop import \
  --connect jdbc:mysql://ms.itversity.com:3306/retail_db \
  --username retail_user \
  --password itversity \
  --table order_items \
  --warehouse-dir /user/rajasekar/sqoop_import/retail_db \
  --num-mappers 2 \
  --delete-target-dir \
  --as-textfile \
  --compress \
  --compression-codec org.apache.hadoop.io.compress.SnappyCodec
```

Ouput

```
[rajasekar@gw02 ~]$ hadoop fs -ls /user/rajasekar/sqoop_import/retail_db/*
Found 3 items
-rw-r--r--   2 rajasekar hdfs          0 2019-06-01 11:15 /user/rajasekar/sqoop_import/retail_db/order_items/_SUCCESS
-rw-r--r--   2 rajasekar hdfs     903308 2019-06-01 11:15 /user/rajasekar/sqoop_import/retail_db/order_items/part-m-00000.snappy
-rw-r--r--   2 rajasekar hdfs     907969 2019-06-01 11:15 /user/rajasekar/sqoop_import/retail_db/order_items/part-m-00001.snappy

```
  
Note : Go to /etc/hadoop/conf and check core-site.xml for supported compression codecs