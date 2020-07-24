# Ошибка при ./configure PostGIS 2.5.3

## Конфигурация

```
SuSe Linux 12.3
Postgres 12.1
```

## Проблема

При попытке конфигурации PostGIS ошибка

```
...
configure: error: could not find xml2-config from libxml2 within the current path. You may need to try re-running configure with a --with-xml2config parameter.
```

## Решение

Не хватает пакета libxml2-devel

```
zypper install libxml2-devel
```