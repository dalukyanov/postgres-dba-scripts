# Разворачивание Postgres 12.1 под SuSe 12.3

## Подготовка ОС.

### Необходимые пакеты
> В действительности тут прозапас. Взят список для Oracle. Но внятной инструкции для Postgres я не нашёл. Полагаю, ему вообще достаточно пакетов по умолчанию. Но, поскольку этот список может меняться в зависимости от дистрибутива, то пусть будет с запасом.

```
zypper install binutils \
gcc \
gcc48 \
glibc \
glibc-32bit \
glibc-devel \
glibc-devel-32bit \
libaio1 \
libaio-devel \
libcap1 \
libstdc++48-devel \
libstdc++48-devel-32bit \
libstdc++6 \
libstdc++6-32bit \
libstdc++-devel \
libstdc++-devel-32bit \
libgcc_s1 \
libgcc_s1-32bit \
mksh \
make \
sysstat \
readline-devel \
zlib-devel
```

### Настройка параметров ядра

vi /etc/sysctl.conf

```
# Конфигурация для RAM = 4GB
fs.aio-max-nr = 1048576
fs.file-max = 6815744
kernel.hung_task_timeout_secs = 600
kernel.sem = 250 32000 100 128
kernel.shmall = 1011681
kernel.shmmax = 2071922688
kernel.shmmni = 4096
kernel.sysrq = 0
kernel.watchdog = 1
kernel.watchdog_thresh = 60
net.core.rmem_default = 262144
net.core.rmem_max = 4194304
net.core.wmem_default = 262144
net.core.wmem_max = 1048576
net.ipv4.ip_forward = 0
net.ipv4.ip_local_port_range = 9000 65500
net.ipv4.tcp_syncookies = 1
net.ipv6.conf.all.disable_ipv6 = 1
net.ipv6.conf.all.forwarding = 0
vm.dirty_expire_centisecs=500
vm.dirty_writeback_centisecs=100
vm.swappiness=0
vm.dirty_background_ratio=3
vm.dirty_ratio=15
```

### Лимиты

vi /etc/security/limits.conf

```
# PostgreSQL 10
postgres soft nproc 2047
postgres hard nproc 16384
postgres soft nofile 4096
postgres hard nofile 65536
postgres soft stack 10240
postgres hard stack 32768
postgres soft  memlock 3145728
postgres hard  memlock 3145728
```

### PAM

vi /etc/pam.d/login

```
# PostgreSQL 10
session required pam_limits.so
```

### Параметры монтирования

vi /etc/fstab

```
none                    /dev/shm        tmpfs   defaults,size=3G       0 0
```

### Перезагрузка

```
shutdown -r now
```

## Создание пользователя и необходимых директорий

```
useradd --base-dir=/u01 --user-group --shell=/bin/bash --home-dir=/u01/postgres --create-home postgres
passwd postgres

mkdir -p /u01/postgres/12.1/pgsql/data
chown -R postgres:postgres /u01/postgres
```

## Сборка Postgres 12.1 из исходников

```
./configure --prefix=/u01/postgres/12.1/pgsql
make -C /u01/distr/postgresql-12.1 world
```

#### Под root

```
make -C /u01/distr/postgresql-12.1 install-world
```

## Создание сервиса systemd

vi /etc/systemd/system/postgresql.service

```
[Unit]
Description=PostgreSQL database server
Documentation=man:postgres(1)

[Service]
User=postgres
ExecStart=/u01/postgres/12.1/pgsql/bin/postgres -D /u01/postgres/12.1/pgsql/data
ExecReload=/bin/kill -HUP $MAINPID
KillMode=mixed
KillSignal=SIGINT
TimeoutSec=0

[Install]
WantedBy=multi-user.target
```

Права и автозагрузка.

```
chmod 755 postgresql.service
systemctl enable postgresql
```

## Инициализация и запуск кластера

```
initdb --data-checksums  --pgdata=/u01/postgres/12.1/pgsql/data --encoding=utf8 --username=postgres --pwprompt
```
