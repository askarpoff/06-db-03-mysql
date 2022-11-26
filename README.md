# 06-db-03-mysql
# Домашнее задание к занятию "6.3. MySQL"

## Задача 1

Используя docker поднимите инстанс MySQL (версию 8). Данные БД сохраните в volume.

Изучите [бэкап БД](https://github.com/netology-code/virt-homeworks/tree/virt-11/06-db-03-mysql/test_data) и 
восстановитесь из него.

Перейдите в управляющую консоль `mysql` внутри контейнера.

Используя команду `\h` получите список управляющих команд.

Найдите команду для выдачи статуса БД и **приведите в ответе** из ее вывода версию сервера БД.

Подключитесь к восстановленной БД и получите список таблиц из этой БД.

**Приведите в ответе** количество записей с `price` > 300.

### Ответ:
```
mysql> status
--------------
mysql  Ver 8.0.31 for Linux on x86_64 (MySQL Community Server - GPL)


mysql> use db;
Database changed
mysql> SHOW TABLES;
+--------------+
| Tables_in_db |
+--------------+
| orders       |
+--------------+
1 row in set (0.00 sec)

mysql> select count(*) from orders where price>300;
+----------+
| count(*) |
+----------+
|        1 |
+----------+
1 row in set (0.00 sec)

```
## Задача 2

Создайте пользователя test в БД c паролем test-pass, используя:
- плагин авторизации mysql_native_password
- срок истечения пароля - 180 дней 
- количество попыток авторизации - 3 
- максимальное количество запросов в час - 100
- аттрибуты пользователя:
    - Фамилия "Pretty"
    - Имя "James"

Предоставьте привелегии пользователю `test` на операции SELECT базы `test_db`.
    
Используя таблицу INFORMATION_SCHEMA.USER_ATTRIBUTES получите данные по пользователю `test` и 
**приведите в ответе к задаче**.
### Ответ:
```
mysql> CREATE USER 'test'@'localhost'
    ->     IDENTIFIED WITH mysql_native_password BY 'test-pass'
    ->     WITH MAX_CONNECTIONS_PER_HOUR 100
    ->     PASSWORD EXPIRE INTERVAL 180 DAY
    ->     FAILED_LOGIN_ATTEMPTS 3 PASSWORD_LOCK_TIME 1
    ->     ATTRIBUTE '{"first_name":"James", "last_name":"Pretty"}';
Query OK, 0 rows affected (0.03 sec)

mysql> GRANT SELECT ON db.* TO test@localhost;
Query OK, 0 rows affected, 1 warning (0.01 sec)

mysql> select * from INFORMATION_SCHEMA.USER_ATTRIBUTES where user = 'test';
+------+-----------+------------------------------------------------+
| USER | HOST      | ATTRIBUTE                                      |
+------+-----------+------------------------------------------------+
| test | localhost | {"last_name": "Pretty", "first_name": "James"} |
+------+-----------+------------------------------------------------+
1 row in set (0.00 sec)
```
## Задача 3

Установите профилирование `SET profiling = 1`.
Изучите вывод профилирования команд `SHOW PROFILES;`.

Исследуйте, какой `engine` используется в таблице БД `test_db` и **приведите в ответе**.

Измените `engine` и **приведите время выполнения и запрос на изменения из профайлера в ответе**:
- на `MyISAM`
- на `InnoDB`

## Задача 4 

Изучите файл `my.cnf` в директории /etc/mysql.

Измените его согласно ТЗ (движок InnoDB):
- Скорость IO важнее сохранности данных
- Нужна компрессия таблиц для экономии места на диске
- Размер буффера с незакомиченными транзакциями 1 Мб
- Буффер кеширования 30% от ОЗУ
- Размер файла логов операций 100 Мб

Приведите в ответе измененный файл `my.cnf`.
