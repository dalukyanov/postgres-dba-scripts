# Создание пользователя с правами только на чтение в БД Postgres

Создание пользователя SOME_VIEWER в БД и выдача ему права на использования чужой схемы myschema

```
CREATE USER SOME_VIEWER WITH PASSWORD 'SOME_VIEWER';
GRANT USAGE ON schema myschema to SOME_VIEWER;
```

Выдача прав на просмотр объектов в интересующей схеме
```
GRANT SELECT ON myschema.TABLE1 TO some_viewer;
GRANT SELECT ON myschema.TABLE2 TO some_viewer;
```

В файле pg_hba.conf добавляем запись для внешнего сервера some_host.somedomain, чтобы он мог заходить под данной учётной записью на сервер.
```
host  myschema  some_viewer  some_host.somedomain      md5
```

Перечитываем конфигурацию.
```
SELECT pg_reload_conf();
```