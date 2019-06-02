# Handling nulls

Consider our source table has some `null values` and let us see what would be the out if i run sqoop import on that table

```
sqoop import \
 --connect "jdbc:mysql://localhost:3306/mytrainingdb" \
 --username root \
 --password root \
 --table temptable \
 --warehouse-dir /sqoop/test_13/ \
 --delete-target-dir

```
Output of HDFS,

```
1,raj,Male,1
1,sekar,Male,2
1,marky,female,3
1,jane,null,4
10,steve,null,6

```


Sometimes we need to set null values to some default data.In this case we can use below command.

```
--null-string <null-string>	 -> The string to be written for a null value for string columns
--null-non-string <null-string> -> The string to be written for a null value for non-string columns
```

Now I want to replace null with 'unknown'.So we can use `--null-string` as below.

```
sqoop import \
 --connect "jdbc:mysql://localhost:3306/mytrainingdb" \
 --username root \
 --password root \
 --table temptable \
 --warehouse-dir /sqoop/test_13/ \
 --delete-target-dir \
 --null-string unknown
```

On running the above command we will get below output.

```
1,raj,Male,1
1,sekar,Male,2
1,marky,female,3
1,jane,unknown,4
10,steve,unknown,6
```

# Delimiters


The default behaviour of delimiter is `comma(,)` for fields and `new line character` at end for each line.

Suppose if I need to change the field delimiter to "tab" and  lines terminated to ":".We can use below options.

```
--fields-terminated-by <char>	Sets the field separator character
--lines-terminated-by <char> Sets the end-of-line character
```

```
sqoop import \
 --connect "jdbc:mysql://localhost:3306/mytrainingdb" \
 --username root \
 --password root \
 --table temptable \
 --warehouse-dir /sqoop/test_13/ \
 --delete-target-dir \
 --null-string unknown \
 --fields-terminated-by "\t" \
 --lines-terminated-by  ":"
```

So the output is

```
1	raj	Male	1:1	sekar	Male	2:1	marky	female	3:1	jane	unknown	4:10	steve	unknown		5:
```