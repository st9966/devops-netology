Обязательная задача 1
Есть скрипт:
```commandline
#!/usr/bin/env python3
a = 1
b = '2'
c = a + b
```
Вопросы:
Какое значение будет присвоено переменной c?
Как получить для переменной c значение 12?
Как получить для переменной c значение 3?
***Ответ***
1. ошибка. типы не соответсвуют int и str
2. привести a к строке: c=str(a)+b
3. привести b к целому числу: c=a+int(b)

Обязательная задача 2
Мы устроились на работу в компанию, где раньше уже был DevOps Engineer. Он написал скрипт, позволяющий узнать, 
какие файлы модифицированы в репозитории, относительно локальных изменений. Этим скриптом недовольно начальство, 
потому что в его выводе есть не все изменённые файлы, а также непонятен полный путь к директории, где они находятся. 
Как можно доработать скрипт ниже, чтобы он исполнял требования вашего руководителя?
```commandline
#!/usr/bin/env python3

import os

bash_command = ["cd ~/netology/sysadm-homeworks", "git status"]
result_os = os.popen(' && '.join(bash_command)).read()
is_change = False
for result in result_os.split('\n'):
    if result.find('modified') != -1:
        prepare_result = result.replace('\tmodified:   ', '')
        print(prepare_result)
        break
```
***Ответ***
is_change - лишнее
break не корректна, т.к. прерывает при первом найденом вхождении.

```commandline
import os
bash_command = ["cd ~/devops3-netology", "git status"]
result_os = os.popen(' && '.join(bash_command)).read()
for result in result_os.split('\n'):
    if result.find('изменено') != -1:
        prepare_result = result.replace('\tизменено:   ', '')
        print(prepare_result)
```

Обязательная задача 3
Доработать скрипт выше так, чтобы он мог проверять не только локальный репозиторий в текущей директории, 
а также умел воспринимать путь к репозиторию, который мы передаём как входной параметр. Мы точно знаем, 
что начальство коварное и будет проверять работу этого скрипта в директориях, которые не являются локальными 
репозиториями.

***Ответ***
```commandline
#!/usr/bin/env python3
import os
import sys

cmd = os.getcwd()

if len(sys.argv)>=2:
    cmd = sys.argv[1]
bash_command = ["cd "+cmd, "git status 2>&1"]
result_os = os.popen(' && '.join(bash_command)).read()
for result in result_os.split('\n'):
    if result.find('fatal') != -1:
        print('Каталог '+cmd+' не GIT репозиторий ')
    if result.find('изменено') != -1:
        prepare_result = result.replace('\tизменено: ', '')
        prepare_result = prepare_result.replace(' ', '')
        print(cmd+prepare_result)
```
Вывод:
```
vagrant@vagrant:~$ ./test.py
Каталог /home/vagrant не GIT репозиторий
vagrant@vagrant:~$ ./test.py ~/testGIT/
/home/vagrant/testGIT/README.md
/home/vagrant/testGIT/123.txt
/home/vagrant/testGIT/test1.txt
```

Обязательная задача 4
Наша команда разрабатывает несколько веб-сервисов, доступных по http. Мы точно знаем, что на их стенде нет никакой 
балансировки, кластеризации, за DNS прячется конкретный IP сервера, где установлен сервис. Проблема в том, что отдел, 
занимающийся нашей инфраструктурой очень часто меняет нам сервера, поэтому IP меняются примерно раз в неделю, 
при этом сервисы сохраняют за собой DNS имена. Это бы совсем никого не беспокоило, если бы несколько раз сервера 
не уезжали в такой сегмент сети нашей компании, который недоступен для разработчиков. Мы хотим написать скрипт, 
который опрашивает веб-сервисы, получает их IP, выводит информацию в стандартный вывод в виде: 
<URL сервиса> - <его IP>. Также, должна быть реализована возможность проверки текущего IP сервиса c его IP из 
предыдущей проверки. Если проверка будет провалена - оповестить об этом в стандартный вывод сообщением:
[ERROR] <URL сервиса> IP mismatch: <старый IP> <Новый IP>. Будем считать, что наша разработка реализовала сервисы: 
drive.google.com, mail.google.com, google.com.

***Ответ***
```commandline
#!/usr/bin/env python3

import socket as soc
import datetime as date
import time as time

i = 1
wait = 2
srv = {'drive.google.com':'0.0.0.0', 'mail.google.com':'0.0.0.0', 'google.com':'0.0.0.0'}
init=0

print(' start ')
print(srv)
print('+++++++')

while 1==1 :
  for host in srv:
    ip = soc.gethostbyname(host)
    if ip != srv[host]:
      if i==1 and init !=1:
        print(str(date.datetime.now().strftime("%Y-%m-%d %H:%M:%S")) +' [Ошибка] ' + str(host) +' IP mistmatch: '+srv[h>      srv[host]=ip        
  time.sleep(2)
```
```commandline
vagrant@vagrant:~$ ./test1.py
 start
{'drive.google.com': '0.0.0.0', 'mail.google.com': '0.0.0.0', 'google.com': '0.0.0.0'}
+++++++
2021-12-21 07:35:05 [Ошибка] drive.google.com IP mistmatch: 0.0.0.0 173.194.73.194
2021-12-21 07:35:05 [Ошибка] mail.google.com IP mistmatch: 0.0.0.0 173.194.222.83
2021-12-21 07:35:05 [Ошибка] google.com IP mistmatch: 0.0.0.0 108.177.14.139
2021-12-21 07:35:21 [Ошибка] drive.google.com IP mistmatch: 173.194.73.194 173.194.222.194
2021-12-21 07:35:55 [Ошибка] google.com IP mistmatch: 108.177.14.139 108.177.14.102
2021-12-21 07:35:57 [Ошибка] google.com IP mistmatch: 108.177.14.102 74.125.205.100
2021-12-21 07:35:59 [Ошибка] google.com IP mistmatch: 74.125.205.100 74.125.205.102
2021-12-21 07:39:10 [Ошибка] mail.google.com IP mistmatch: 173.194.222.83 64.233.164.19
2021-12-21 07:39:12 [Ошибка] mail.google.com IP mistmatch: 64.233.164.19 64.233.164.17
vagrant@vagrant:~$
```
