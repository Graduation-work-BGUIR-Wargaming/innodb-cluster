# InnoDB cluster

Стенд для изучения InnoDB-кластера.

## Документация
https://docs.oracle.com/cd/E17952_01/mysql-5.7-en/mysql-innodb-cluster-using-router.html

https://docs.oracle.com/cd/E17952_01/mysql-5.7-en/mysql-innodb-cluster-production-deployment.html


---
## Запуск

Для запуска необходим вагрант. После провижинга хостов на данном этапе необходимо вручную с mysql-shell ноды создать кластер. Так же вручную инициализировать mysql-router.

---
## Инициализация кластера

Для каждой ноды выполнить (в примере приведено только для ```node1```):

```mysqlsh -e "dba.configureLocalInstance('admin@node1:3306');"```

Это приготовит инстансы будущего кластера.

Все остальные действия производятся на хосте ```mysql-shell```

```sudo mysqlsh --uri admin@node1 -e "dba.createCluster('clTest');```

```sudo mysqlsh --uri admin@node1 -e "dba.getCluster().addInstance('admin@node2');"```

```sudo mysqlsh --uri admin@node1 -e "dba.getCluster().addInstance('admin@node3');"```

### Просмотр состояния
```sudo mysqlsh --uri admin@node1 -e "dba.getCluster().status()"```

### Инициализация и запуск ```mysql-router```

```mysqlrouter --bootstrap admin@node1 --user=mysqlrouter```
```mysqlrouter&```

### Подключение к кластеру
```mysql -u admin -p -P6446 -h 127.0.0.1```

---
### Учетные данные:
    mysql_root_password = "_SecretPass1"
    mysql_admin_username = "admin"
    mysql_admin_password = "_AdminPassword2"
