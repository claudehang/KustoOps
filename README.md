# KustoOps

1. Create a new table
```
.create table SourceTable(id: string, val: int)
```

2. Append rows to table
```
.ingest inline into table SourceTable <|
  e, 4
  e, 10
  f, 4
  f, 7
  f, 13
```

3. Add update policy (target will change when source updates)
```
.create table TargetTable(id: string, avgVal: decimal)

.create function trans() {
SourceTable
  | summarize avgVal = todecimal(avg(val)) by id
  | project id, avgVal
}

.alter table TargetTable policy update 
@'[{ "IsEnabled": true, "Source": "SourceTable", "Query": "trans", "IsTransactional": true }]'
```
