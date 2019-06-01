# Sqoop eval

Sqoop-eval command is mainly used to run SQL queries and verify the results.It can be used to test/preview the data before importing to HDFS.


We can either use `--query` or `--e` to provide the SQL command. 

*Using --e

```
sqoop eval \
  --connect jdbc:mysql://ms.itversity.com:3306/retail_db \
  --username retail_user \
  --password itversity \
  --e "SELECT * FROM order_items LIMIT 10"

```

*Using --query

```
sqoop eval \
  --connect jdbc:mysql://ms.itversity.com:3306/retail_db \
  --username retail_user \
  --password itversity \
  --query "SELECT * FROM order_items LIMIT 10"

sqoop eval \
  --connect jdbc:mysql://ms.itversity.com:3306/retail_db \
  --username retail_user \
  --password itversity \
  --query "INSERT INTO orders VALUES (100000, '2017-10-31 00:00:00.0', 100000, 'DUMMY')"

sqoop eval \
  --connect jdbc:mysql://ms.itversity.com:3306/retail_export \
  --username retail_user \
  --password itversity \
  --query "CREATE TABLE dummy (i INT)"

sqoop eval \
  --connect jdbc:mysql://ms.itversity.com:3306/retail_export \
  --username retail_user \
  --password itversity \
  --query "INSERT INTO dummy VALUES (1)"

sqoop eval \
  --connect jdbc:mysql://ms.itversity.com:3306/retail_export \
  --username retail_user \
  --password itversity \
  --query "SELECT * FROM dummy"


```