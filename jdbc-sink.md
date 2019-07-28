# Notes

```sql
SELECT id, email, department, modified 
FROM test 
WHERE `modified` > ? 
	AND `modified` < ? 
ORDER BY `modified` ASC
```


```
SELECT id, email, department, modified 
FROM test 
WHERE `modified` > '2019-07-11 06:11:38' 
	AND `modified` < '2019-07-11 06:20:40' 
ORDER BY `modified` ASC

SELECT id, email, department, modified 
FROM test 
WHERE `modified` > '2019-07-11 06:11:38' 
	AND `modified` < '2019-07-11 06:20:41' 
ORDER BY `modified` ASC;

SELECT id, email, department, modified 
FROM test 
WHERE `modified` > '2019-07-11 06:11:38' 
	AND `modified` < '2019-07-11 06:20:42' 
ORDER BY `modified` ASC;
```

`2019-07-11 06:11:38` is the max `modified` for last snapshot offset.

# Links
- https://docs.confluent.io/current/connect/kafka-connect-jdbc/sink-connector/sink_config_options.html