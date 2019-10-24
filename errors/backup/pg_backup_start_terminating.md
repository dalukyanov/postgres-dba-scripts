# Ошибки при бэкапе. FATAL:  terminating connection due to administrator command, STATEMENT:  SELECT pg_start_backup(...)

## Версия

```
SUSE Linux Enterprise Server 12.3
Postgres 9.6
```

## Проблема

Не проходит бэкап.
В логе ~/9.6/data/pg_log/postgresql-Thu.log имеем следующие ошибки:

```
< 2019-10-24 21:05:01.949 MSK > FATAL:  terminating connection due to administrator command
< 2019-10-24 21:05:01.949 MSK > STATEMENT:  SELECT pg_start_backup('pgida_backup__803226_1571940150')
```

## Диагностика

Из доки https://postgrespro.ru/docs/postgrespro/9.5/continuous-archiving:

```
По умолчанию pg_start_backup может выполняться длительное время. Это объясняется тем, что функция выполняет контрольную точку, а операции ввода/вывода, требуемые для этого, распределяются в интервале времени, по умолчанию равном половине интервала между контрольными точками (см. параметр checkpoint_completion_target).
```

## Решение

1. Изменить значение параметра checkpoint_completion_target в postgresql.conf с 0.5 на 0.3

2. Перечитать файл параметров
```
psql
psql (9.6.9)
Type "help" for help.

postgres=# SELECT pg_reload_conf();
 pg_reload_conf
----------------
 t
(1 row)
```