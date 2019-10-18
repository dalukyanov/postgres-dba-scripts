# Как узнать статус репликации на стендбай в Postgres

## На праймари

> pg_stat_replication - Это кому база раздает redo-log'и. Т.е. на мастере будут видны все непосредственно подключенные slaves и их состояние. Но поскольку допустима каскадная репликация, то на slave эта таблица может отображать состояние других slave-баз, подключенных именно к этой.

```
postgres=# select * from pg_stat_replication;
 pid  | usesysid |  usename  | application_name | client_addr | client_hostname | client_port |         backend_start         | backend_xmin |   state   | sent_location | write_location | flush_location | replay_location | sync_priority | sync_state
------+----------+-----------+------------------+-------------+-----------------+-------------+-------------------------------+--------------+-----------+---------------+----------------+----------------+-----------------+---------------+------------
 3799 |    16942 | replicant | walreceiver      | 10.2.150.6  |                 |       45426 | 2019-10-01 13:13:52.984154+03 |              | streaming | 0/501A2158    | 0/501A2120     | 0/501A2120     | 0/501A2120      |             0 | async

```

Статус (передачи или роль?)

```
postgres=# select pg_is_in_recovery();
 pg_is_in_recovery
-------------------
 f
(1 row)

```

TODO: Разобраться, чем отличаются f и t

## На стендбае

```
postgres=# select * from pg_stat_replication;
 pid | usesysid | usename | application_name | client_addr | client_hostname | client_port | backend_start | backend_xmin | state | sent_location | write_location | flush_location | replay_location | sync_priority | sync_state
-----+----------+---------+------------------+-------------+-----------------+-------------+---------------+--------------+-------+---------------+----------------+----------------+-----------------+---------------+------------
(0 rows)
```

Статус (передачи или роль?)

```
postgres=# select pg_is_in_recovery();
 pg_is_in_recovery
-------------------
 t
(1 row)
```