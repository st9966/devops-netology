1. Мы выгрузили JSON, который получили через API запрос к нашему сервису:
```commandline
    { "info" : "Sample JSON output from our service\t",
        "elements" :[
            { "name" : "first",
            "type" : "server",
            "ip" : 7175 
            }
            { "name" : "second",
            "type" : "proxy",
            "ip : 71.78.22.43
            }
        ]
    }
```
Нужно найти и исправить все ошибки, которые допускает наш сервис
***Ответ***
Нет кавычек: 
```commandline
    { "info" : "Sample JSON output from our service\t",
        "elements" :[
            { "name" : "first",
            "type" : "server",
!            "ip" : "7175" 
            }
            { "name" : "second",
            "type" : "proxy",
!            "ip" : "71.78.22.43"
            }
        ]
    }
```

2. В прошлый рабочий день мы создавали скрипт, позволяющий опрашивать веб-сервисы и получать их IP. 
К уже реализованному функционалу нам нужно добавить возможность записи JSON и YAML файлов, описывающих наши сервисы. 
Формат записи JSON по одному сервису: { "имя сервиса" : "его IP"}. Формат записи YAML по одному сервису: - имя сервиса: 
его IP. Если в момент исполнения скрипта меняется IP у сервиса - он должен так же поменяться в yml и json файле.

***Ответ***
```doctest
#!/usr/bin/env python3

import socket as soc
import datetime as date
import time as time
import json
import yaml

i = 1
srv = {'drive.google.com':'0.0.0.0', 'mail.google.com':'0.0.0.0', 'google.com':'0.0.0.0'}
init = 0
path = "/home/vagrant/"
log = "/home/vagrant/err.log"

print(' start ')
print(srv)
print('+++++++')

while 1==1 :
  for host in srv:
    ip = soc.gethostbyname(host)
    if ip != srv[host]:
      if i==1 and init !=1:
        is_error=True
        with open(log,'a') as fl:
          print(str(date.datetime.now().strftime("%Y-%m-%d %H:%M:%S")) +' [Ошибка] ' + str(host) +' IP mistmatch: '+srv[host]+' '+ip,file=fl)
        with open(path+host+".json",'w') as write_file_json:
          json_data= json.dumps({host:ip})
          write_file_json.write(json_data)
        with open(path+host+".yaml",'w') as write_file_yaml:
          yaml_data= yaml.dump([{host : ip}])
          write_file_yaml.write(yaml_data)
    srv[host]=ip
  time.sleep(2)
```
```
root@vagrant:/home/vagrant# tail err.log
2021-12-29 05:24:09 [Ошибка] mail.google.com IP mistmatch: 0.0.0.0 173.194.222.19
2021-12-29 05:24:09 [Ошибка] google.com IP mistmatch: 0.0.0.0 64.233.161.102
2021-12-29 05:24:24 [Ошибка] drive.google.com IP mistmatch: 0.0.0.0 173.194.221.194
2021-12-29 05:24:24 [Ошибка] mail.google.com IP mistmatch: 0.0.0.0 173.194.222.19
2021-12-29 05:24:24 [Ошибка] google.com IP mistmatch: 0.0.0.0 64.233.161.102
2021-12-29 05:25:08 [Ошибка] drive.google.com IP mistmatch: 0.0.0.0 173.194.221.194
2021-12-29 05:25:08 [Ошибка] mail.google.com IP mistmatch: 0.0.0.0 108.177.14.17
2021-12-29 05:25:08 [Ошибка] google.com IP mistmatch: 0.0.0.0 64.233.161.102
2021-12-29 05:25:10 [Ошибка] mail.google.com IP mistmatch: 108.177.14.17 108.177.14.83
2021-12-29 05:25:14 [Ошибка] drive.google.com IP mistmatch: 173.194.221.194 74.125.205.194
```
```
root@vagrant:/home/vagrant# tail google.com.json
{"google.com": "64.233.161.102"}
root@vagrant:/home/vagrant#

root@vagrant:/home/vagrant# tail google.com.yaml
- google.com: 64.233.161.102
root@vagrant:/home/vagrant#

```
