# Sqoop list databases

Run below command to list databases.

```
sqoop list-databases \
  --connect jdbc:mysql://localhost:3306 \
  --username retail_user \
  --password itversity

```

# Sqoop list tables

Run below command to list tables.

```
sqoop list-tables \
  --connect jdbc:mysql://localhost:3306/retail-db \
  --username retail_user \
  --password itversity

```