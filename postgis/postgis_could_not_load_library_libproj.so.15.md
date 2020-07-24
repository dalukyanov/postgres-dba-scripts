# Ошибка после установки PostGIS

## Конфигурация

```
SuSe Linux 12.3
Postgres 12.1
```

## Проблема

При попытке проверить состояние PostGIS ошибка

```
SELECT PostGIS_Version();
ERROR:  could not load library "/u01/postgres/12.1/pgsql/lib/postgis-2.5.so": libproj.so.15: cannot open shared object file: No such file or directory
```

## Решение

Не сделан ldconfig после make install

```
# ldconfig
```

Проверка

```
SELECT PostGIS_Version();
            postgis_version
---------------------------------------
 2.5 USE_GEOS=1 USE_PROJ=1 USE_STATS=1
(1 row)

```

## Ссылки

Описание в инструкции
https://postgis.net/docs/manual-1.3/ch02.html