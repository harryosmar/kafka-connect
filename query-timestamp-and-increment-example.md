## Connector config

```json
{
	"connector.class": "io.confluent.connect.jdbc.JdbcSourceConnector",
	"tasks.max": 1,
	"connection.url": "jdbc:sqlserver://my-mssql:1433;DatabaseName=test_db",
	"connection.user": "sa",
	"connection.password": "EmergingSystems1",
	"mode": "timestamp+incrementing",
	"timestamp.column.name": "modified",
	"incrementing.column.name": "id",
	"topic.prefix": "quickstart-jdbc-mssql-query",
	"poll.interval.ms": 1000,
	"query": "SELECT TOP 1 id AS sink_id, id, email, department, modified FROM test_db.dbo.test"
}
```

## query

### 1st second

```sql
SELECT TOP 1 id AS sink_id, id, email, department, modified 
FROM test 
WHERE `modified` < '2019-07-28 16:31:53' 
	AND (
			(`modified` = '1970-01-01 00:00:00' AND `id` > -1) 
			OR `modified` > '1970-01-01 00:00:00'
		) 
ORDER BY `modified`,`id` ASC
```

### 2nd second

```sql
SELECT TOP 1 id AS sink_id, id, email, department, modified 
FROM test 
WHERE `modified` < '2019-07-28 16:31:54' 
	AND (
			(`modified` = '2019-07-28 16:31:05' AND `id` > 10) 
			OR `modified` > '2019-07-28 16:31:05'
		) 
ORDER BY `modified`,`id` ASC
```

### 3rd second

```sql
SELECT TOP 1 id AS sink_id, id, email, department, modified 
FROM test 
WHERE `modified` < '2019-07-28 16:31:55' 
	AND (
			(`modified` = '2019-07-28 16:31:05' AND `id` > 10) 
			OR `modified` > '2019-07-28 16:31:05'
	) 
ORDER BY `modified`,`id` ASC
```
