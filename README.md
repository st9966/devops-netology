**Задача 1**
В этом задании вы потренируетесь в:

установке elasticsearch
первоначальном конфигурировании elastcisearch
запуске elasticsearch в docker
Используя докер образ centos:7 как базовый и документацию по установке и запуску Elastcisearch:

составьте Dockerfile-манифест для elasticsearch
соберите docker-образ и сделайте push в ваш docker.io репозиторий
запустите контейнер из получившегося образа и выполните запрос пути / c хост-машины
Требования к elasticsearch.yml:

данные path должны сохраняться в /var/lib
имя ноды должно быть netology_test
В ответе приведите:

текст Dockerfile манифеста
ссылку на образ в репозитории dockerhub
ответ elasticsearch на запрос пути / в json виде
Подсказки:

возможно вам понадобится установка пакета perl-Digest-SHA для корректной работы пакета shasum
при сетевых проблемах внимательно изучите кластерные и сетевые настройки в elasticsearch.yml
при некоторых проблемах вам поможет docker директива ulimit
elasticsearch в логах обычно описывает проблему и пути ее решения
Далее мы будем работать с данным экземпляром elasticsearch.

***Ответ***
```commandline
root@vagrant:/home/vagrant/elasticsearch# curl -XGET http://localhost:9200
{
  "name" : "ed91683b3b14",
  "cluster_name" : "netology_test",
  "cluster_uuid" : "oLl2d999Qma608BIQBJLYg",
  "version" : {
    "number" : "8.1.0",
    "build_flavor" : "default",
    "build_type" : "tar",
    "build_hash" : "3700f7679f7d95e36da0b43762189bab189bc53a",
    "build_date" : "2022-03-03T14:20:00.690422633Z",
    "build_snapshot" : false,
    "lucene_version" : "9.0.0",
    "minimum_wire_compatibility_version" : "7.17.0",
    "minimum_index_compatibility_version" : "7.0.0"
  },
  "tagline" : "You Know, for Search"
```

**Задача 2**
В этом задании вы научитесь:

создавать и удалять индексы
изучать состояние кластера
обосновывать причину деградации доступности данных
Ознакомтесь с документацией и добавьте в elasticsearch 3 индекса, в соответствии со таблицей:

Имя	Количество реплик	Количество шард
ind-1	0	1
ind-2	1	2
ind-3	2	4
Получите список индексов и их статусов, используя API и приведите в ответе на задание.

Получите состояние кластера elasticsearch, используя API.

Как вы думаете, почему часть индексов и кластер находится в состоянии yellow?

Удалите все индексы.

Важно

При проектировании кластера elasticsearch нужно корректно рассчитывать количество реплик и шард, иначе возможна потеря данных индексов, вплоть до полной, при деградации системы.


***Ответ***

Создание индексов:
```commandline
root@vagrant:/home/vagrant/elasticsearch# curl -X PUT localhost:9200/ind-1 -H 'Content-Type: application/json' -d'{ "settings": { "number_of_shards": 1,  "number_of_replicas": 0 }}'
{"acknowledged":true,"shards_acknowledged":true,"index":"ind-1"}root@vagrant:/home/vagrant/elasticsearch#
root@vagrant:/home/vagrant/elasticsearch# curl -X PUT localhost:9200/ind-2 -H 'Content-Type: application/json' -d'{ "settings": { "number_of_shards": 2,  "number_of_replicas": 1 }}'
{"acknowledged":true,"shards_acknowledged":true,"index":"ind-2"}root@vagrant:/home/vagrant/elasticsearch#
root@vagrant:/home/vagrant/elasticsearch# curl -X PUT localhost:9200/ind-3 -H 'Content-Type: application/json' -d'{ "settings": { "number_of_shards": 4,  "number_of_replicas": 2 }}'
{"acknowledged":true,"shards_acknowledged":true,"index":"ind-3"}root@vagrant:/home/vagrant/elasticsearch#
root@vagrant:/home/vagrant/elasticsearch#
```
Список индексов:
```commandline
root@vagrant:/home/vagrant/elasticsearch# curl -X GET 'http://localhost:9200/_cat/indices?v'
health status index uuid                   pri rep docs.count docs.deleted store.size pri.store.size
green  open   ind-1 BFkMUrQhQ6WbX2fcRtWyBw   1   0          0            0       225b           225b
yellow open   ind-3 3pqK8LXXSSuTPBqkHiALtQ   4   2          0            0       900b           900b
yellow open   ind-2 Mgvj65RFRkKrUgbzc-DlFA   2   1          0            0       450b           450b
root@vagrant:/home/vagrant/elasticsearch#
```
Статус:
```commandline
root@vagrant:/home/vagrant/elasticsearch# curl -X GET 'http://localhost:9200/_cluster/health/ind-1?pretty'
{
  "cluster_name" : "netology_test",
  "status" : "green",
  "timed_out" : false,
  "number_of_nodes" : 1,
  "number_of_data_nodes" : 1,
  "active_primary_shards" : 1,
  "active_shards" : 1,
  "relocating_shards" : 0,
  "initializing_shards" : 0,
  "unassigned_shards" : 0,
  "delayed_unassigned_shards" : 0,
  "number_of_pending_tasks" : 0,
  "number_of_in_flight_fetch" : 0,
  "task_max_waiting_in_queue_millis" : 0,
  "active_shards_percent_as_number" : 100.0
}
root@vagrant:/home/vagrant/elasticsearch# curl -X GET 'http://localhost:9200/_cluster/health/ind-2?pretty'
{
  "cluster_name" : "netology_test",
  "status" : "yellow",
  "timed_out" : false,
  "number_of_nodes" : 1,
  "number_of_data_nodes" : 1,
  "active_primary_shards" : 2,
  "active_shards" : 2,
  "relocating_shards" : 0,
  "initializing_shards" : 0,
  "unassigned_shards" : 2,
  "delayed_unassigned_shards" : 0,
  "number_of_pending_tasks" : 0,
  "number_of_in_flight_fetch" : 0,
  "task_max_waiting_in_queue_millis" : 0,
  "active_shards_percent_as_number" : 44.44444444444444
}
root@vagrant:/home/vagrant/elasticsearch# curl -X GET 'http://localhost:9200/_cluster/health/ind-3?pretty'
{
  "cluster_name" : "netology_test",
  "status" : "yellow",
  "timed_out" : false,
  "number_of_nodes" : 1,
  "number_of_data_nodes" : 1,
  "active_primary_shards" : 4,
  "active_shards" : 4,
  "relocating_shards" : 0,
  "initializing_shards" : 0,
  "unassigned_shards" : 8,
  "delayed_unassigned_shards" : 0,
  "number_of_pending_tasks" : 0,
  "number_of_in_flight_fetch" : 0,
  "task_max_waiting_in_queue_millis" : 0,
  "active_shards_percent_as_number" : 44.44444444444444
}
```
Статус кластера:
```commandline
root@vagrant:/home/vagrant/elasticsearch# curl -XGET localhost:9200/_cluster/health/?pretty=true
{
  "cluster_name" : "netology_test",
  "status" : "yellow",
  "timed_out" : false,
  "number_of_nodes" : 1,
  "number_of_data_nodes" : 1,
  "active_primary_shards" : 8,
  "active_shards" : 8,
  "relocating_shards" : 0,
  "initializing_shards" : 0,
  "unassigned_shards" : 10,
  "delayed_unassigned_shards" : 0,
  "number_of_pending_tasks" : 0,
  "number_of_in_flight_fetch" : 0,
  "task_max_waiting_in_queue_millis" : 0,
  "active_shards_percent_as_number" : 44.44444444444444
}
```
Удаление:
```commandline
root@vagrant:/home/vagrant/elasticsearch# curl -X DELETE 'http://localhost:9200/ind-1?pretty'
{
  "acknowledged" : true
}
root@vagrant:/home/vagrant/elasticsearch# curl -X DELETE 'http://localhost:9200/ind-2?pretty'
{
  "acknowledged" : true
}
root@vagrant:/home/vagrant/elasticsearch# curl -X DELETE 'http://localhost:9200/ind-3?pretty'
{
  "acknowledged" : true
}
```

**Задача 3**
В данном задании вы научитесь:

создавать бэкапы данных
восстанавливать индексы из бэкапов
Создайте директорию {путь до корневой директории с elasticsearch в образе}/snapshots.

Используя API зарегистрируйте данную директорию как snapshot repository c именем netology_backup.

Приведите в ответе запрос API и результат вызова API для создания репозитория.

Создайте индекс test с 0 реплик и 1 шардом и приведите в ответе список индексов.

Создайте snapshot состояния кластера elasticsearch.

Приведите в ответе список файлов в директории со snapshotами.

Удалите индекс test и создайте индекс test-2. Приведите в ответе список индексов.

Восстановите состояние кластера elasticsearch из snapshot, созданного ранее.

Приведите в ответе запрос к API восстановления и итоговый список индексов.

Подсказки:

возможно вам понадобится доработать elasticsearch.yml в части директивы path.repo и перезапустить elasticsearch

***Ответ***

Регистрация репозитория:
```commandline
root@vagrant:/home/vagrant/elasticsearch# curl -XPOST localhost:9200/_snapshot/netology_backup?pretty -H 'Content-Type: application/json' -d'{"type": "fs", "settings": { "location":"/elasticsearch-8.1.0/snapshots/" }}'
{
  "acknowledged" : true
}
```
Создание индекса:
```commandline
root@vagrant:/home/vagrant/elasticsearch# curl -X PUT localhost:9200/test -H 'Content-Type: application/json' -d'{ "settings": { "number_of_shards": 1,  "number_of_replicas": 0 }}'
{"acknowledged":true,"shards_acknowledged":true,"index":"test"}root@vagrant:/home/vagrant/elasticsearch#
```
Создание снапшота:
```commandline
root@vagrant:/home/vagrant/elasticsearch# curl -X PUT localhost:9200/_snapshot/netology_backup/elasticsearch?wait_for_completion=true
{"snapshot":{"snapshot":"elasticsearch","uuid":"M5JF8kVwQIO4JD998PBEFQ","repository":"netology_backup","version_id":8010099,"version":"8.1.0","indices":[".geoip_databases","test"],"data_streams":[],"include_global_state":true,"state":"SUCCESS","start_time":"2022-03-14T16:04:10.112Z","start_time_in_millis":1647273850112,"end_time":"2022-03-14T16:04:11.742Z","end_time_in_millis":1647273851742,"duration_in_millis":1630,"failures":[],"shards":{"total":2,"failed":0,"successful":2},"feature_states":[{"feature_name":"geoip","indices":[".geoip_databases"]}]}}root@vagrant:/home/vagrant/elasticsearch#
```
Файлы снапшота:
```commandline
root@vagrant:/home/vagrant/elasticsearch# docker exec -it c29469e9dedd /bin/ls -la /elasticsearch-8.1.0/snapshots
total 48
drwxr-xr-x 1 elasticsearch elasticsearch  4096 Mar 14 16:04 .
drwxr-xr-x 1 elasticsearch elasticsearch  4096 Mar 14 15:59 ..
-rw-r--r-- 1 elasticsearch elasticsearch   846 Mar 14 16:04 index-0
-rw-r--r-- 1 elasticsearch elasticsearch     8 Mar 14 16:04 index.latest
drwxr-xr-x 4 elasticsearch elasticsearch  4096 Mar 14 16:04 indices
-rw-r--r-- 1 elasticsearch elasticsearch 18273 Mar 14 16:04 meta-M5JF8kVwQIO4JD998PBEFQ.dat
-rw-r--r-- 1 elasticsearch elasticsearch   361 Mar 14 16:04 snap-M5JF8kVwQIO4JD998PBEFQ.dat
```
Создание индексов, восстановление снапшота:
```commandline
root@vagrant:/home/vagrant/elasticsearch# curl -X DELETE 'http://localhost:9200/test?pretty'
{
  "acknowledged" : true
}
root@vagrant:/home/vagrant/elasticsearch# curl -X PUT localhost:9200/test-2?pretty -H 'Content-Type: application/json' -d'{ "settings": { "number_of_shards": 1,  "number_of_replicas": 0 }}'
{
  "acknowledged" : true,
  "shards_acknowledged" : true,
  "index" : "test-2"
}
root@vagrant:/home/vagrant/elasticsearch# curl -X GET 'http://localhost:9200/_cat/indices?v'
health status index  uuid                   pri rep docs.count docs.deleted store.size pri.store.size
green  open   test-2 k9znejj-QLSRIGNvo-duuQ   1   0          0            0       225b           225b
root@vagrant:/home/vagrant/elasticsearch# curl -X POST localhost:9200/_snapshot/netology_backup/elasticsearch/_restore?pretty -H 'Content-Type: application/json' -d'{"include_global_state":true}'
{
  "accepted" : true
}
root@vagrant:/home/vagrant/elasticsearch# curl -X GET 'http://localhost:9200/_cat/indices?v'                                                                               health status index  uuid                   pri rep docs.count docs.deleted store.size pri.store.size
green  open   test-2 k9znejj-QLSRIGNvo-duuQ   1   0          0            0       225b           225b
green  open   test   3MuzfqtATJq7W6DmsbTNBw   1   0          0            0       225b           225b
```