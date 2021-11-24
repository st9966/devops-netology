1. На лекции мы познакомились с node_exporter. В демонстрации его исполняемый файл запускался в background. 
Этого достаточно для демо, но не для настоящей production-системы, где процессы должны находиться под внешним управлением. 
Используя знания из лекции по systemd, создайте самостоятельно простой unit-файл для node_exporter:
-поместите его в автозагрузку,
-предусмотрите возможность добавления опций к запускаемому процессу через внешний файл (посмотрите, например, на systemctl cat cron),
удостоверьтесь, что с помощью systemctl процесс корректно стартует, завершается, а после перезагрузки автоматически поднимается.
***Ответ***
'vagrant@vagrant:~$ sudo reboot
Connection to 127.0.0.1 closed by remote host.
Connection to 127.0.0.1 closed.
PS C:\HashiCorp\conf> vagrant.exe ssh
vagrant@vagrant:~$ sudo systemctl status node_exporter
● node_exporter.service - Node Exporter
     Loaded: loaded (/etc/systemd/system/node_exporter.service; enabled; vendor preset: enabled)
     Active: active (running) since Tue 2021-11-23 06:55:33 UTC; 4h 34min ago
   Main PID: 709 (node_exporter)
      Tasks: 5 (limit: 1071)
     Memory: 13.7M
     CGroup: /system.slice/node_exporter.service
             └─709 /usr/local/bin/node_exporter
vagrant@vagrant:~$ sudo systemctl restart node_exporter
vagrant@vagrant:~$ sudo systemctl status node_exporter
● node_exporter.service - Node Exporter
     Loaded: loaded (/etc/systemd/system/node_exporter.service; enabled; vendor preset: enabled)
     Active: active (running) since Tue 2021-11-23 11:30:33 UTC; 5s ago
   Main PID: 884 (node_exporter)
      Tasks: 4 (limit: 1071)
     Memory: 2.2M
     CGroup: /system.slice/node_exporter.service
             └─884 /usr/local/bin/node_exporter'
'[Unit]
Description=Node Exporter
Wants=network-online.target
After=network-online.target
After=remote-fs.target nss-user-lookup.target
[Service]
User=node_exporter
Group=node_exporter
Type=simple
ExecStart=/usr/local/bin/node_exporter $EXTRA_OPTS
IgnoreSIGPIPE=false
Restart=on-failure
[Install]
WantedBy=multi-user.target'


2. Ознакомьтесь с опциями node_exporter и выводом /metrics по-умолчанию. 
Приведите несколько опций, которые вы бы выбрали для базового мониторинга хоста по CPU, памяти, диску и сети.
***Ответ***
'CPU:
node_cpu_seconds_total{cpu="0",mode="system"} 16.02
node_cpu_seconds_total{cpu="0",mode="user"} 13.84
node_cpu_seconds_total{cpu="1",mode="idle"} 84586.92
Mem:
node_memory_Mapped_bytes 6.2386176e+07
node_memory_MemAvailable_bytes 7.43059456e+08
node_memory_MemFree_bytes 1.1931648e+08
IO:
node_disk_io_time_seconds_total{device="dm-0"} 14.832
node_disk_io_time_seconds_total{device="dm-1"} 0.044
node_disk_io_time_seconds_total{device="sda"} 15.028
node_disk_read_time_seconds_total{device="dm-0"} 11.936
node_disk_read_time_seconds_total{device="dm-1"} 0.04
node_disk_read_time_seconds_total{device="sda"} 7.474
node_disk_write_time_seconds_total{device="dm-0"} 170.388
node_disk_write_time_seconds_total{device="dm-1"} 0
node_disk_write_time_seconds_total{device="sda"} 12.858
Net:
node_network_receive_bytes_total{device="eth0"} 8.4304905e+07
node_network_receive_bytes_total{device="lo"} 569919
node_network_transmit_bytes_total{device="eth0"} 1.777565e+06
node_network_transmit_bytes_total{device="lo"} 569919'

3. Установите в свою виртуальную машину Netdata. Воспользуйтесь готовыми пакетами для установки (sudo apt install -y netdata). После успешной установки:
в конфигурационном файле /etc/netdata/netdata.conf в секции [web] замените значение с localhost на bind to = 0.0.0.0,
добавьте в Vagrantfile проброс порта Netdata на свой локальный компьютер и сделайте vagrant reload:
config.vm.network "forwarded_port", guest: 19999, host: 19999
После успешной перезагрузки в браузере на своем ПК (не в виртуальной машине) вы должны суметь зайти на localhost:19999. 
Ознакомьтесь с метриками, которые по умолчанию собираются Netdata и с комментариями, которые даны к этим метрикам.
***Ответ***
https://cloud.mail.ru/public/13DT/9PQ7bRmyu

4. Можно ли по выводу dmesg понять, осознает ли ОС, что загружена не на настоящем оборудовании, а на системе виртуализации?
***Ответ***
Да: 
'vagrant@vagrant:~$ dmesg | grep virt
[    0.003541] CPU MTRRs all blank - virtualized system.
[    0.104267] Booting paravirtualized kernel on KVM
[    2.800859] systemd[1]: Detected virtualization oracle.'

5. Как настроен sysctl fs.nr_open на системе по-умолчанию? Узнайте, что означает этот параметр. 
Какой другой существующий лимит не позволит достичь такого числа (ulimit --help)?
***Ответ***
'fs.nr_open = 1048576'
Это параметр означает максимальное количество дескрипторов файлов, которое может выделить процесс. 
Значение по умолчанию - 1024 * 1024 (1048576).
'ulimit -n' (но он распространяется только на пользователя)

6. Запустите любой долгоживущий процесс (не ls, который отработает мгновенно, а, например, sleep 1h) 
в отдельном неймспейсе процессов; покажите, что ваш процесс работает под PID 1 через nsenter. Для простоты работайте в 
данном задании под root (sudo -i). Под обычным пользователем требуются дополнительные опции (--map-root-user) и т.д.
***Ответ***
'root@vagrant:~# ps -e | grep sleep
   1210 pts/1    00:00:00 sleep
root@vagrant:~# nsenter --target 1210 --pid --mount
root@vagrant:/# ps
    PID TTY          TIME CMD
   1211 pts/2    00:00:00 sudo
   1213 pts/2    00:00:00 bash
   1230 pts/2    00:00:00 nsenter
   1231 pts/2    00:00:00 bash
   1240 pts/2    00:00:00 ps
root@vagrant:/#'
7. Найдите информацию о том, что такое :(){ :|:& };:. Запустите эту команду в своей виртуальной машине 
Vagrant с Ubuntu 20.04 (это важно, поведение в других ОС не проверялось). Некоторое время все будет "плохо", 
после чего (минуты) – ОС должна стабилизироваться. Вызов dmesg расскажет, какой механизм помог автоматической 
стабилизации. Как настроен этот механизм по-умолчанию, и как изменить число процессов, которое можно создать в сессии?
***Ответ*** 
'[ 1740.419258] cgroup: fork rejected by pids controller in /user.slice/user-1000.slice/session-1.scope
vagrant@vagrant:~$ ulimit -u 25 поможет ограничить количетво процессов'



