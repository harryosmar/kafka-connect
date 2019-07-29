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

## Lesson learned

Setting `batch.max.rows` will not be used in the SQL query.

This setting can be used to limit the amount of data buffered internally in the connector.

eg : we can use `SELECT TOP 1000` in the query. But we want to buffer `100` records to connector. Objectives :
- Only use `1` connection query with limit `1000` to the Database, instead of `10` connections with limit `100` (10 * 100).
- Do not overload the payload data for the kafka connector, limit it by default `100`

## How the connector query looks like ? when fetching the data from MSSQL Server

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
			(`modified` = '2019-07-28 16:31:05' AND `id` > 1) 
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
			(`modified` = '2019-07-28 16:31:05' AND `id` > 2) 
			OR `modified` > '2019-07-28 16:31:05'
	) 
ORDER BY `modified`,`id` ASC
```

### 4th second

```sql
SELECT TOP 1 id AS sink_id, id, email, department, modified 
FROM test 
WHERE `modified` < '2019-07-28 16:31:55' 
	AND (
			(`modified` = '2019-07-28 16:31:05' AND `id` > 3) 
			OR `modified` > '2019-07-28 16:31:05'
	) 
ORDER BY `modified`,`id` ASC
```

### 5th, 6th, ...th

```sql
SELECT TOP 1 id AS sink_id, id, email, department, modified 
FROM test 
WHERE `modified` < '2019-07-28 16:31:55' 
	AND (
			(`modified` = '2019-07-28 16:31:05' AND `id` > PREVIOUS_OFFSET + 1) 
			OR `modified` > '2019-07-28 16:31:05'
	) 
ORDER BY `modified`,`id` ASC
```


