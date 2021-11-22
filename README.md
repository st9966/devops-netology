1. Какой системный вызов делает команда cd? В прошлом ДЗ мы выяснили, что cd не является самостоятельной программой, 
это shell builtin, поэтому запустить strace непосредственно на cd не получится. Тем не менее, вы можете запустить 
strace на /bin/bash -c 'cd /tmp'. В этом случае вы увидите полный список системных вызовов, которые делает сам bash при старте.
Вам нужно найти тот единственный, который относится именно к cd. Обратите внимание, что strace выдаёт результат своей работы в поток stderr, а не в stdout.
***Ответ***
'''chdir("/tmp") = 0'''

2. Попробуйте использовать команду file на объекты разных типов на файловой системе. Например:
'''vagrant@netology1:~$ file /dev/tty
   /dev/tty: character special (5/0)
   vagrant@netology1:~$ file /dev/sda
   /dev/sda: block special (8/0)
   vagrant@netology1:~$ file /bin/bash
   /bin/bash: ELF 64-bit LSB shared object, x86-64'''
Используя strace выясните, где находится база данных file на основании которой она делает свои догадки.
***Ответ***
'''openat(AT_FDCWD, "/usr/share/misc/magic.mgc", O_RDONLY) = 3'''

3. Предположим, приложение пишет лог в текстовый файл. Этот файл оказался удален (deleted в lsof), однако возможности сигналом сказать приложению переоткрыть файлы
или просто перезапустить приложение – нет. Так как приложение продолжает писать в удаленный файл, место на диске постепенно заканчивается. 
Основываясь на знаниях о перенаправлении потоков предложите способ обнуления открытого удаленного файла (чтобы освободить место на файловой системе).
***Ответ***
'''vagrant@vagrant:~$ vi strace.log
[3]+  Stopped                 vi strace.log
vagrant@vagrant:~$ ps aux | grep vi
root         585  0.0  0.7 238192  7500 ?        Ssl  Nov17   0:14 /usr/lib/accountsservice/accounts-daemon
root         949  0.0  0.2 360948  2952 ?        Sl   Nov17   2:11 /usr/sbin/VBoxService --pidfile /var/run/vboxadd-service.sh
vagrant    20585  0.2  0.9  24512  9960 pts/0    T    11:32   0:00 vi strace.log
vagrant    20587  0.0  0.0   9032   736 pts/0    S+   11:32   0:00 grep --color=auto vi
vagrant@vagrant:~$ rm strace.log
vagrant@vagrant:~$ cat strace.log
cat: strace.log: No such file or directory
vagrant@vagrant:~$ lsof -p 20585 | grep strace.log
vi      20585 vagrant    4u   REG  253,0     4096 131110 /home/vagrant/.strace.log.swp
vagrant@vagrant:~$ cat /proc/20585/fd/4 > recovery.log'''

4. Занимают ли зомби-процессы какие-то ресурсы в ОС (CPU, RAM, IO)?
***Ответ***
Нет, только запись в таблице PIDов

5. В iovisor BCC есть утилита opensnoop:
root@vagrant:~# dpkg -L bpfcc-tools | grep sbin/opensnoop
/usr/sbin/opensnoop-bpfcc
На какие файлы вы увидели вызовы группы open за первую секунду работы утилиты? Воспользуйтесь пакетом bpfcc-tools для Ubuntu 20.04. 
***Ответ***
'''vagrant@vagrant:~$ dpkg -L bpfcc-tools | grep sbin/opensnoop
/usr/sbin/opensnoop-bpfcc
vagrant@vagrant:~$ sudo /usr/sbin/opensnoop-bpfcc
PID    COMM               FD ERR PATH
949    vminfo              4   0 /var/run/utmp
593    dbus-daemon        -1   2 /usr/local/share/dbus-1/system-services
593    dbus-daemon        18   0 /usr/share/dbus-1/system-services
593    dbus-daemon        -1   2 /lib/dbus-1/system-services
593    dbus-daemon        18   0 /var/lib/snapd/dbus-1/system-services/
609    irqbalance          6   0 /proc/interrupts
609    irqbalance          6   0 /proc/stat
609    irqbalance          6   0 /proc/irq/20/smp_affinity
609    irqbalance          6   0 /proc/irq/0/smp_affinity
609    irqbalance          6   0 /proc/irq/1/smp_affinity'''

6. Какой системный вызов использует uname -a? Приведите цитату из man по этому системному вызову,
где описывается альтернативное местоположение в /proc, где можно узнать версию ядра и релиз ОС.
***Ответ***
Part of the utsname information is also accessible  via  /proc/sys/kernel/{ostype, hostname, osrelease, version, domainname}.

7. Чем отличается последовательность команд через ; и через && в bash? Например:
root@netology1:~# test -d /tmp/some_dir; echo Hi
Hi
root@netology1:~# test -d /tmp/some_dir && echo Hi
root@netology1:~#
Есть ли смысл использовать в bash &&, если применить set -e?
***Ответ***
&& это управляющие операторы
; это разделитель 
test -d /tmp/some_dir && echo Hi Echo отработает только в случае вывода равного 0.
Смысла использовать в конвеере вместе set -e и && нет, т.к. принцип их работы почти одинаков. 

8. Из каких опций состоит режим bash set -euxo pipefail и почему его хорошо было бы использовать в сценариях?
***Ответ***
-e прерывает выполнение при значении отличном от 0
-u не заданные переменные идут на stderr с завершением вызова
-x трейс простых команд
-o код возврата последовательности команд.
Хороший уровень логирования и завершение выполнения сценария даже при ошибке на последнем этапе.

9. Используя -o stat для ps, определите, какой наиболее часто встречающийся статус у процессов в системе. 
В man ps ознакомьтесь (/PROCESS STATE CODES) что значат дополнительные к основной заглавной буквы статуса процессов. 
Его можно не учитывать при расчете (считать S, Ss или Ssl равнозначными).
***Ответ***
S- спящий
I- процессы прерывания ядра
T- остановленные по управляющему сигналу