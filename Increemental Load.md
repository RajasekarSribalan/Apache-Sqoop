# Increemental Load


First we need to take a base import and then we can import incrementally.

## Base import

```
sqoop import \
 --connect "jdbc:mysql://localhost:3306/mytrainingdb" \
 --username root \
 --password root \
 --target-dir /sqoop/test_14/ \
 --num-mappers 2 \
 --query "select * from temptable where \$CONDITIONS and created_date like '2013-%'" \
 --split-by id
```
Output is ,

```
1,raj,Male,1,2013-01-01
1,sekar,Male,2,2013-05-01
```

Because we have given created_date like '2013-%' so it fetched only 2013 dates.

## Append

Now to do the incremental loads,we can now changed the date in the condition and use `--append`

```
sqoop import \
 --connect "jdbc:mysql://localhost:3306/mytrainingdb" \
 --username root \
 --password root \
 --target-dir /sqoop/test_14/ \
 --num-mappers 2 \
 --query "select * from temptable where \$CONDITIONS and created_date like '2014-01-%'" \
 --split-by id \
 --append

```
Output is,

```
1,raj,Male,1,2013-01-01
1,sekar,Male,2,2013-05-01
1,siva,female,3,2014-01-01
```
Another append,

```
sqoop import \
 --connect "jdbc:mysql://localhost:3306/mytrainingdb" \
 --username root \
 --password root \
 --target-dir /sqoop/test_14/ \
 --num-mappers 2 \
 --query "select * from temptable where \$CONDITIONS and created_date like '2014-04-%'" \
 --split-by id \
 --append
```

Output is,

```
1,raj,Male,1,2013-01-01
1,sekar,Male,2,2013-05-01
1,siva,female,3,2014-01-01
1,jane,female,4,2014-04-01
```

## Where option

Another way of giving where clause is as below,Instead of --query we can use --table with `--where` clause

```
sqoop import \
 --connect "jdbc:mysql://localhost:3306/mytrainingdb" \
 --username root \
 --password root \
 --target-dir /sqoop/test_14/ \
 --num-mappers 2 \
 --table temptable \
 --where "created_date like '2014-05-%'" \
 --append
```
 
Output is,

```
1,raj,Male,1,2013-01-01
1,sekar,Male,2,2013-05-01
1,siva,female,3,2014-01-01
1,jane,female,4,2014-04-01
10,margy,female,5,2014-05-01
13,bans,Male,6,2014-05-06
```

## Incremental load using arguments specific to incremental load

```
sqoop import \
 --connect "jdbc:mysql://localhost:3306/mytrainingdb" \
 --username root \
 --password root \
 --target-dir /sqoop/test_14/ \
 --num-mappers 2 \
 --table temptable \
 --check-column created_date \
 --incremental append \
 --last-value '2014-05-06'
```

This will print below,

```
Following arguments:
18/07/07 10:12:50 INFO tool.ImportTool:  --incremental append
18/07/07 10:12:50 INFO tool.ImportTool:   --check-column created_date
18/07/07 10:12:50 INFO tool.ImportTool:   --last-value 2014-11-06
18/07/07 10:12:50 INFO tool.ImportTool: (Consider saving this with 'sqoop job --create')
```

Output is,

```
1,raj,Male,1,2013-01-01
1,sekar,Male,2,2013-05-01
1,siva,female,3,2014-01-01
1,jane,female,4,2014-04-01
10,margy,female,5,2014-05-01
13,bans,Male,6,2014-05-06
19,jason,st,8,2014-08-06
113,Rick,null,9,2014-09-06
131,Jon,null,10,2014-10-06
145,snow,null,11,2014-11-06
```