# Домашнее задание к занятию "`MySQL`" - `Никулин Михаил Сергеевич`



---

### Задание 1

Создаем .yml файл:

```
version: '3.7'
services:
  db:
    image: mysql:8
    restart: always
    environment:
      MYSQL_DATABASE: 'nikulinm_db'
      MYSQL_USER: 'nikulinm'
      MYSQL_PASSWORD: '123456'
      MYSQL_ROOT_PASSWORD: '123456'
    volumes:
      - my-db:/var/lib/mysql
volumes:
  my-db:
```

Запускаем проект:

```
[root@node01 docker1]# docker-compose up -d
Creating network "docker1_default" with the default driver
Creating volume "docker1_my-db" with default driver
Pulling db (mysql:8)...
8: Pulling from library/mysql
46ef68baacb7: Pull complete
94c1114b2e9c: Pull complete
ff05e3f38802: Pull complete
41cc3fcd9912: Pull complete
07bbc8bdf52a: Pull complete
6d88f83726a9: Pull complete
cf5c7d5d33f7: Pull complete
9db3175a2a66: Pull complete
feaedeb27fa9: Pull complete
cf91e7784414: Pull complete
b1770db1c329: Pull complete
Digest: sha256:15f069202c46cf861ce429423ae3f8dfa6423306fbf399eaef36094ce30dd75c
Status: Downloaded newer image for mysql:8
Creating docker1_db_1 ... done
```

Копируем бэкап в запущенный контейнер
```
[root@node01 docker1]# docker cp /var/tmp/test_dump.sql docker1_db_1:/var/tmp/test_dump.sql
Successfully copied 4.1kB to docker1_db_1:/var/tmp/test_dump.sql
```
Запускаем командную строку в контейнере и восстановим дамп
```
[root@node01 docker1]# sudo docker exec -it docker1_db_1 bash
bash-4.4# mysql -u nikulinm -p nikulinm_db < /var/tmp/test_dump.sql
Enter password:
bash-4.4#
```
Версия сервера БД
```
mysql> \s
--------------
mysql  Ver 8.0.33 for Linux on x86_64 (MySQL Community Server - GPL)

Connection id:          10
Current database:
Current user:           nikulinm@localhost
SSL:                    Not in use
Current pager:          stdout
Using outfile:          ''
Using delimiter:        ;
Server version:         8.0.33 MySQL Community Server - GPL
Protocol version:       10
Connection:             Localhost via UNIX socket
Server characterset:    utf8mb4
Db     characterset:    utf8mb4
Client characterset:    latin1
Conn.  characterset:    latin1
UNIX socket:            /var/run/mysqld/mysqld.sock
Binary data as:         Hexadecimal
Uptime:                 13 min 58 sec

Threads: 2  Questions: 35  Slow queries: 0  Opens: 138  Flush tables: 3  Open tables: 56  Queries per second avg: 0.041
--------------
```
Список таблиц БД
```
mysql> show databases;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| nikulinm_db        |
| performance_schema |
+--------------------+
3 rows in set (0.00 sec)

mysql> use nikulinm_db;
Reading table information for completion of table and column names
You can turn off this feature to get a quicker startup with -A

Database changed
mysql> show tables;
+-----------------------+
| Tables_in_nikulinm_db |
+-----------------------+
| orders                |
+-----------------------+
1 row in set (0.00 sec)
```
Приведем в ответе количество записей с price > 300
```
mysql> select count(*) from orders where price > 300;
+----------+
| count(*) |
+----------+
|        1 |
+----------+
1 row in set (0.00 sec)
```


---

### Задание 2

Создадим пользователя с требуемыми параметрами: 
```
mysql> CREATE USER 'test'@'localhost'
    ->     IDENTIFIED WITH mysql_native_password BY 'test-pass'
    ->     WITH MAX_CONNECTIONS_PER_HOUR 100
    ->     PASSWORD EXPIRE INTERVAL 180 DAY
    ->     FAILED_LOGIN_ATTEMPTS 3 PASSWORD_LOCK_TIME 2
    ->     ATTRIBUTE '{"first_name":"James", "last_name":"Pretty"}';
Query OK, 0 rows affected (0.02 sec)
```
Предоставим привелегии пользователю test на операции SELECT базы nikulinm_db:
```
mysql> GRANT SELECT ON nikulinm_db.* to 'test'@'localhost';
Query OK, 0 rows affected, 1 warning (0.03 sec)
```
Используя таблицу INFORMATION_SCHEMA.USER_ATTRIBUTES получим данные по пользователю test:
```
mysql> SELECT * from INFORMATION_SCHEMA.USER_ATTRIBUTES where USER = 'test';
+------+-----------+------------------------------------------------+
| USER | HOST      | ATTRIBUTE                                      |
+------+-----------+------------------------------------------------+
| test | localhost | {"last_name": "Pretty", "first_name": "James"} |
+------+-----------+------------------------------------------------+
1 row in set (0.00 sec)
```

---

### Задание 3

Установим профилирование и изучим вывод профилирования команд:
```
mysql> SET profiling = 1;
Query OK, 0 rows affected, 1 warning (0.00 sec)
```
```
mysql> SHOW PROFILES;
+----------+------------+---------------------------------+
| Query_ID | Duration   | Query                           |
+----------+------------+---------------------------------+
|        1 | 0.00016725 | SET profiling = 1
SHOW PROFILES |
|        2 | 0.00193100 | SHOW DATABASES                  |
|        3 | 0.00031100 | SHOW GRANTS                     |
+----------+------------+---------------------------------+
3 rows in set, 1 warning (0.00 sec)
```
В  таблице БД `lnikulinm_db` используется engine InnoDB:
```
mysql> SELECT TABLE_NAME, ENGINE FROM information_schema.TABLES where TABLE_SCHEMA = 'nikulinm_db';
+------------+--------+
| TABLE_NAME | ENGINE |
+------------+--------+
| orders     | InnoDB |
+------------+--------+
1 row in set (0.01 sec)
```
Измените engine и **приведите время выполнения и запрос на изменения из профайлера в ответе**:
```
mysql> SET profiling = 1;
Query OK, 0 rows affected, 1 warning (0.00 sec)

mysql> ALTER TABLE nikulinm_db.orders ENGINE = MyIsam;
Query OK, 5 rows affected (0.08 sec)
Records: 5  Duplicates: 0  Warnings: 0

mysql> ALTER TABLE nikulinm_db.orders ENGINE = InnoDB;
Query OK, 5 rows affected (0.09 sec)
Records: 5  Duplicates: 0  Warnings: 0

mysql> SHOW PROFILES;
+----------+------------+------------------------------------------------+
| Query_ID | Duration   | Query                                          |
+----------+------------+------------------------------------------------+
|        1 | 0.08343750 | ALTER TABLE nikulinm_db.orders ENGINE = MyIsam |
|        2 | 0.09244800 | ALTER TABLE nikulinm_db.orders ENGINE = InnoDB |
+----------+------------+------------------------------------------------+
2 rows in set, 1 warning (0.00 sec)
```

___

### Задание 4

Изучим файл my.cnf:
```
bash-4.4# cat /etc/my.cnf
# For advice on how to change settings please see
# http://dev.mysql.com/doc/refman/8.0/en/server-configuration-defaults.html

[mysqld]
#
# Remove leading # and set to the amount of RAM for the most important data
# cache in MySQL. Start at 70% of total RAM for dedicated server, else 10%.
# innodb_buffer_pool_size = 128M
#
# Remove leading # to turn on a very important data integrity option: logging
# changes to the binary log between backups.
# log_bin
#
# Remove leading # to set options mainly useful for reporting servers.
# The server defaults are faster for transactions and fast SELECTs.
# Adjust sizes as needed, experiment to find the optimal values.
# join_buffer_size = 128M
# sort_buffer_size = 2M
# read_rnd_buffer_size = 2M

# Remove leading # to revert to previous value for default_authentication_plugin,
# this will increase compatibility with older clients. For background, see:
# https://dev.mysql.com/doc/refman/8.0/en/server-system-variables.html#sysvar_default_authentication_plugin
# default-authentication-plugin=mysql_native_password
skip-host-cache
skip-name-resolve
datadir=/var/lib/mysql
socket=/var/run/mysqld/mysqld.sock
secure-file-priv=/var/lib/mysql-files
user=mysql

pid-file=/var/run/mysqld/mysqld.pid
[client]
socket=/var/run/mysqld/mysqld.sock

!includedir /etc/mysql/conf.d/
```
Изменим его согласно ТЗ (движок InnoDB):
```
bash-4.4# cat /etc/my.cnf
# For advice on how to change settings please see
# http://dev.mysql.com/doc/refman/8.0/en/server-configuration-defaults.html

[mysqld]
#
# Remove leading # and set to the amount of RAM for the most important data
# cache in MySQL. Start at 70% of total RAM for dedicated server, else 10%.
# innodb_buffer_pool_size = 128M
#
# Remove leading # to turn on a very important data integrity option: logging
# changes to the binary log between backups.
# log_bin
#
# Remove leading # to set options mainly useful for reporting servers.
# The server defaults are faster for transactions and fast SELECTs.
# Adjust sizes as needed, experiment to find the optimal values.
# join_buffer_size = 128M
# sort_buffer_size = 2M
# read_rnd_buffer_size = 2M

# Remove leading # to revert to previous value for default_authentication_plugin,
# this will increase compatibility with older clients. For background, see:
# https://dev.mysql.com/doc/refman/8.0/en/server-system-variables.html#sysvar_default_authentication_plugin
# default-authentication-plugin=mysql_native_password
skip-host-cache
skip-name-resolve
datadir=/var/lib/mysql
socket=/var/run/mysqld/mysqld.sock
secure-file-priv=/var/lib/mysql-files
user=mysql

pid-file=/var/run/mysqld/mysqld.pid
[client]
socket=/var/run/mysqld/mysqld.sock

!includedir /etc/mysql/conf.d/
innodb_flush_log_at_trx_commit = 0
innodb_file_format=Barracuda
innodb_log_buffer_size= 1M
key_buffer_size = 300M
max_binlog_size= 100M
```

---
