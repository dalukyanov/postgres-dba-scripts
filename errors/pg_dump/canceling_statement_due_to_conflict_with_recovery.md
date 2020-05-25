# pg_dump: Error message from server: ERROR:  canceling statement due to conflict with recovery

## Версия

```
SUSE Linux Enterprise Server 12.3
PostgreSQL 10.3
```

## Проблема

При попытке снять дамп через pg_dump в DBeaver получаем следующие ошибки:

```
pg_dump: Dumping the contents of table "df_history_attribute" failed: PQgetResult() failed.
pg_dump: Error message from server: ERROR:  canceling statement due to conflict with recovery
DETAIL:  User query might have needed to see row versions that must be removed.
pg_dump: The command was: COPY eln.df_history_attribute (id, df_id, user_id, action_date_time, reason, history_log) TO stdout;
```

## Диагностика

Запросы на горячем стендбае могут свалиться, т.к. требуемые строки были обновлены или удалены на праймари, и реплицированы на стендбай. Поскольку праймари не знает о том, что строки требуются на стендбае, процесс VACUUM очищает старые версии строк. А стендбай должен повторить данные операции, и очистить строки также.
Таким образом, долгие запросы могут отваливаться.

## Решение

На стендбае до 15 минут увеличены значения следующих параметров для задержки в очистке строк.

```
max_standby_archive_delay = 900s        # max delay before canceling queries
                                        # when reading WAL from archive;
                                        # -1 allows indefinite delay
max_standby_streaming_delay = 900s      # max delay before canceling queries
                                        # when reading streaming WAL;
                                        # -1 allows indefinite delay
```