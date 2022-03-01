**Задача 1**
Используя docker поднимите инстанс PostgreSQL (версию 12) c 2 volume, в который будут складываться данные БД и бэкапы.

Приведите получившуюся команду или docker-compose манифест.

***Ответ***
```
root@vagrant:/etc/netplan# docker pull postgres:12
root@vagrant:/home/vagrant/postgres# docker volume create postgres_base1
postgres_base1
root@vagrant:/home/vagrant/postgres# docker volume create postgres_base2
postgres_base2
root@vagrant:/home/vagrant/postgres# docker run --rm --name postgres12 -e POSTGRES_PASSWORD=postgres -ti -p 5433:5432 -d -v postgres_base1:/var/lib/postgresql/data -v postgres_base2:/var/lib/postgresql  postgres:12
a1dbf93858e3868550620eed5105315360b1583bea9600266f4512b7333859aa
root@vagrant:/home/vagrant/postgres# docker ps
CONTAINER ID   IMAGE         COMMAND                  CREATED         STATUS         PORTS                                       NAMES
a1dbf93858e3   postgres:12   "docker-entrypoint.s…"   2 seconds ago   Up 2 seconds   0.0.0.0:5433->5432/tcp, :::5433->5432/tcp   postgres12
```

**Задача 2**
В БД из задачи 1:

создайте пользователя test-admin-user и БД test_db
в БД test_db создайте таблицу orders и clients (спeцификация таблиц ниже)
предоставьте привилегии на все операции пользователю test-admin-user на таблицы БД test_db
создайте пользователя test-simple-user
предоставьте пользователю test-simple-user права на SELECT/INSERT/UPDATE/DELETE данных таблиц БД test_db
Таблица orders:

id (serial primary key)
наименование (string)
цена (integer)
Таблица clients:

id (serial primary key)
фамилия (string)
страна проживания (string, index)
заказ (foreign key orders)
Приведите:

итоговый список БД после выполнения пунктов выше,
описание таблиц (describe)
SQL-запрос для выдачи списка пользователей с правами над таблицами test_db
список пользователей с правами над таблицами test_db

***Ответ***
```
root@vagrant:/home/vagrant/postgres# docker exec -it a1dbf93858e3 su postgres
postgres@a1dbf93858e3:/$ psql
psql (12.10 (Debian 12.10-1.pgdg110+1))
Type "help" for help.
postgres=# CREATE DATABASE test_db;
CREATE DATABASE
postgres=# CREATE ROLE "test-admin-user" SUPERUSER NOCREATEDB NOCREATEROLE NOINHERIT LOGIN;
CREATE ROLE
postgres=# CREATE TABLE orders
(
id integer,
name text,
price integer,
PRIMARY KEY (id)
);

CREATE TABLE clients
(
        id integer PRIMARY KEY,
        lastname text,
        country text,
        booking integer,
        FOREIGN KEY (booking) REFERENCES orders (Id)
);
CREATE TABLE
CREATE TABLE
postgres=# CREATE ROLE "test-simple-user" NOSUPERUSER NOCREATEDB NOCREATEROLE NOINHERIT LOGIN;
GRANT SELECT ON TABLE public.clients TO "test-simple-user";
GRANT INSERT ON TABLE public.clients TO "test-simple-user";
GRANT UPDATE ON TABLE public.clients TO "test-simple-user";
GRANT DELETE ON TABLE public.clients TO "test-simple-user";
GRANT SELECT ON TABLE public.orders TO "test-simple-user";
GRANT INSERT ON TABLE public.orders TO "test-simple-user";
GRANT UPDATE ON TABLE public.orders TO "test-simple-user";
GRANT DELETE ON TABLE public.orders TO "test-simple-user";
CREATE ROLE
GRANT
GRANT
GRANT
GRANT
GRANT
GRANT
GRANT
GRANT
postgres=# \l
                                 List of databases
   Name    |  Owner   | Encoding |  Collate   |   Ctype    |   Access privileges
-----------+----------+----------+------------+------------+-----------------------
 postgres  | postgres | UTF8     | en_US.utf8 | en_US.utf8 |
 template0 | postgres | UTF8     | en_US.utf8 | en_US.utf8 | =c/postgres          +
           |          |          |            |            | postgres=CTc/postgres
 template1 | postgres | UTF8     | en_US.utf8 | en_US.utf8 | =c/postgres          +
           |          |          |            |            | postgres=CTc/postgres
 test_db   | postgres | UTF8     | en_US.utf8 | en_US.utf8 |
(4 rows)
postgres=# \du
                                       List of roles
    Role name     |                         Attributes                         | Member of
------------------+------------------------------------------------------------+-----------
 postgres         | Superuser, Create role, Create DB, Replication, Bypass RLS | {}
 test-admin-user  | Superuser, No inheritance                                  | {}
 test-simple-user | No inheritance                                             | {}
postgres=# \dt
          List of relations
 Schema |  Name   | Type  |  Owner
--------+---------+-------+----------
 public | clients | table | postgres
 public | orders  | table | postgres
(2 rows)
postgres=# select * from information_schema.table_privileges where grantee in ('test-admin-user','test-simple-user');
 grantor  |     grantee      | table_catalog | table_schema | table_name | privilege_type | is_grantable | with_hierarchy
----------+------------------+---------------+--------------+------------+----------------+--------------+----------------
 postgres | test-simple-user | postgres      | public       | clients    | INSERT         | NO           | NO
 postgres | test-simple-user | postgres      | public       | clients    | SELECT         | NO           | YES
 postgres | test-simple-user | postgres      | public       | clients    | UPDATE         | NO           | NO
 postgres | test-simple-user | postgres      | public       | clients    | DELETE         | NO           | NO
 postgres | test-simple-user | postgres      | public       | orders     | INSERT         | NO           | NO
 postgres | test-simple-user | postgres      | public       | orders     | SELECT         | NO           | YES
 postgres | test-simple-user | postgres      | public       | orders     | UPDATE         | NO           | NO
 postgres | test-simple-user | postgres      | public       | orders     | DELETE         | NO           | NO
(8 rows)
```


**Задача 3**

Используя SQL синтаксис - наполните таблицы следующими тестовыми данными:

Используя SQL синтаксис:

вычислите количество записей для каждой таблицы
приведите в ответе:
запросы
результаты их выполнения.

***Ответ***

```commandline
postgres=# insert into orders VALUES (1, 'Шоколад', 10), (2, 'Принтер', 3000), (3, 'Книга', 500), (4, 'Монитор', 7000), (5, 'Гитара', 4000);
INSERT 0 5
postgres=# insert into clients VALUES (1, 'Иванов Иван Иванович', 'USA'), (2, 'Петров Петр Петрович', 'Canada'), (3, 'Иоганн Себастьян Бах', 'Japan'), (4, 'Ронни Джеймс Дио', 'Russia'), (5, 'Ritchie Blackmore', 'Russia');
INSERT 0 5
postgres=# select count (*) from orders;
 count
-------
     5
(1 row)

postgres=# select count (*) from clients;
 count
-------
     5
(1 row)

postgres=#
```

**Задача 4**
Часть пользователей из таблицы clients решили оформить заказы из таблицы orders.

Используя foreign keys свяжите записи из таблиц, согласно таблице:

ФИО	Заказ
Иванов Иван Иванович	Книга
Петров Петр Петрович	Монитор
Иоганн Себастьян Бах	Гитара
Приведите SQL-запросы для выполнения данных операций.

Приведите SQL-запрос для выдачи всех пользователей, которые совершили заказ, а также вывод данного запроса.

Подсказк - используйте директиву UPDATE.

***Ответ***

```commandline
postgres=# update  clients set booking = 3 where id = 1;
UPDATE 1
postgres=# update  clients set booking = 4 where id = 2;
UPDATE 1
postgres=# update  clients set booking = 5 where id = 3;
UPDATE 1
postgres=# select * from clients as c where  exists (select id from orders as o where c.booking = o.id) ;
 id |       lastname       | country | booking
----+----------------------+---------+---------
  1 | Иванов Иван Иванович | USA     |       3
  2 | Петров Петр Петрович | Canada  |       4
  3 | Иоганн Себастьян Бах | Japan   |       5
postgres=# select * from clients where booking is not null;
 id |       lastname       | country | booking
----+----------------------+---------+---------
  1 | Иванов Иван Иванович | USA     |       3
  2 | Петров Петр Петрович | Canada  |       4
  3 | Иоганн Себастьян Бах | Japan   |       5
(3 rows)
```

**Задача 5**
Получите полную информацию по выполнению запроса выдачи всех пользователей из задачи 4 (используя директиву EXPLAIN).

Приведите получившийся результат и объясните что значат полученные значения.

***Ответ***

```commandline
postgres=# explain select * from clients where booking is not null;
                        QUERY PLAN
-----------------------------------------------------------
 Seq Scan on clients  (cost=0.00..18.10 rows=806 width=72)
   Filter: (booking IS NOT NULL)
(2 rows)
```
Запрос показывает, как будут сканироваться таблицы, применяя фильтр не пустое значение. cost=0.00..18.10 означает
примерное время, которое проходит, прежде чем начнётся этап вывода данных. rows=806 - Ожидаемое число строк, которое
должен вывести этот узел плана. width=72 - Ожидаемый средний размер строк, выводимых этим узлом плана (в байтах)

**Задача 6**
Создайте бэкап БД test_db и поместите его в volume, предназначенный для бэкапов (см. Задачу 1).

Остановите контейнер с PostgreSQL (но не удаляйте volumes).

Поднимите новый пустой контейнер с PostgreSQL.

Восстановите БД test_db в новом контейнере.

Приведите список операций, который вы применяли для бэкапа данных и восстановления.

***Ответ***
```
root@vagrant:/home/vagrant/postgres# docker run --rm --name postgres12-1 -e POSTGRES_PASSWORD=postgres -ti -p 5434:5432 -d -v postgres_base1:/var/lib/postgresql/data -v postgres_base2:/var/lib/postgresql  postgres:12
74be3f7b8f65753ff6abb6995b48e22ae148a5aebb5155c4477b6090bf94e0e7
root@vagrant:/home/vagrant/postgres# docker ps
CONTAINER ID   IMAGE         COMMAND                  CREATED          STATUS          PORTS                                       NAMES
74be3f7b8f65   postgres:12   "docker-entrypoint.s…"   6 seconds ago    Up 5 seconds    0.0.0.0:5434->5432/tcp, :::5434->5432/tcp   postgres12-1
root@vagrant:/home/vagrant/postgres# docker exec -it 74be3f7b8f65 su postgres
postgres@74be3f7b8f65:/$ psql test_db < /var/lib/postgresql/data/dump.sql
SET
SET
SET
SET
SET
 set_config
------------

(1 row)

SET
SET
SET
SET
```
