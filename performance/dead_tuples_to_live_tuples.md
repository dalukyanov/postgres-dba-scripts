# Отношение dead tuples к live tuples

Выполняется в целевой БД

```sql
SELECT 
	relname AS ObjectName
	,pg_stat_get_live_tuples(c.oid) AS LiveTuples
	,pg_stat_get_dead_tuples(c.oid) AS DeadTuples
	,(pg_stat_get_dead_tuples(c.oid)::float / pg_stat_get_live_tuples(c.oid)::float * 100) as DeadToLivePCT
FROM pg_class c
where pg_stat_get_live_tuples(c.oid) != 0
ORDER BY 4 desc;
```

