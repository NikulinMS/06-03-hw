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

`Приведите ответ в свободной форме........`

1. `Заполните здесь этапы выполнения, если требуется ....`
2. `Заполните здесь этапы выполнения, если требуется ....`
3. `Заполните здесь этапы выполнения, если требуется ....`
4. `Заполните здесь этапы выполнения, если требуется ....`
5. `Заполните здесь этапы выполнения, если требуется ....`
6. 

```
Поле для вставки кода...
....
....
....
....
```

`При необходимости прикрепитe сюда скриншоты
![Название скриншота 2](ссылка на скриншот 2)`


---

### Задание 3

`Приведите ответ в свободной форме........`

1. `Заполните здесь этапы выполнения, если требуется ....`
2. `Заполните здесь этапы выполнения, если требуется ....`
3. `Заполните здесь этапы выполнения, если требуется ....`
4. `Заполните здесь этапы выполнения, если требуется ....`
5. `Заполните здесь этапы выполнения, если требуется ....`
6. 

```
Поле для вставки кода...
....
....
....
....
```

`При необходимости прикрепитe сюда скриншоты
![Название скриншота](ссылка на скриншот)`

### Задание 4

`Приведите ответ в свободной форме........`

1. `Заполните здесь этапы выполнения, если требуется ....`
2. `Заполните здесь этапы выполнения, если требуется ....`
3. `Заполните здесь этапы выполнения, если требуется ....`
4. `Заполните здесь этапы выполнения, если требуется ....`
5. `Заполните здесь этапы выполнения, если требуется ....`
6. 

```
Поле для вставки кода...
....
....
....
....
```

`При необходимости прикрепитe сюда скриншоты
![Название скриншота](ссылка на скриншот)`

---
## Дополнительные задания (со звездочкой*)


### Задание 5

`Приведите ответ в свободной форме........`

1. `Заполните здесь этапы выполнения, если требуется ....`
2. `Заполните здесь этапы выполнения, если требуется ....`
3. `Заполните здесь этапы выполнения, если требуется ....`
4. `Заполните здесь этапы выполнения, если требуется ....`
5. `Заполните здесь этапы выполнения, если требуется ....`
6. 

`При необходимости прикрепитe сюда скриншоты
![Название скриншота](ссылка на скриншот)`
