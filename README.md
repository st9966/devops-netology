2. Могут ли файлы, являющиеся жесткой ссылкой на один объект, иметь разные права доступа и владельца? Почему?  
***Ответ***
Нет, не могут, т.к. ссылки имеют один и тот же inode
3. Сделайте vagrant destroy на имеющийся инстанс Ubuntu. Замените содержимое Vagrantfile следующим:
***Ответ***  
```  
NAME                 MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT  
sda                    8:0    0   64G  0 disk  
├─sda1                 8:1    0  512M  0 part /boot/efi  
├─sda2                 8:2    0    1K  0 part  
└─sda5                 8:5    0 63.5G  0 part  
  ├─vgvagrant-root   253:0    0 62.6G  0 lvm  /  
  └─vgvagrant-swap_1 253:1    0  980M  0 lvm  [SWAP]  
sdb                    8:16   0  2.5G  0 disk  
sdc                    8:32   0  2.5G  0 disk  
```  
4. Используя fdisk, разбейте первый диск на 2 раздела: 2 Гб, оставшееся пространство.
***Ответ***  
```  
root@vagrant:/home/vagrant# lsblk  
  NAME                 MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT  
  sda                    8:0    0   64G  0 disk  
  ├─sda1                 8:1    0  512M  0 part /boot/efi  
  ├─sda2                 8:2    0    1K  0 part  
  └─sda5                 8:5    0 63.5G  0 part  
      ├─vgvagrant-root   253:0    0 62.6G  0 lvm  /  
      └─vgvagrant-swap_1 253:1    0  980M  0 lvm  [SWAP]  
  sdb                    8:16   0  2.5G  0 disk  
  ├─sdb1                 8:17   0    2G  0 part  
  └─sdb2                 8:18   0  511M  0 part  
  sdc                    8:32   0  2.5G  0 disk  
  ```
5. Используя sfdisk, перенесите данную таблицу разделов на второй диск.
***Ответ***  
```root@vagrant:/home/vagrant# sfdisk -d /dev/sdb|sfdisk --force /dev/sdc  
Checking that no-one is using this disk right now ... OK  
Disk /dev/sdc: 2.51 GiB, 2684354560 bytes, 5242880 sectors  
Disk model: VBOX HARDDISK  
Units: sectors of 1 * 512 = 512 bytes  
Sector size (logical/physical): 512 bytes / 512 bytes  
I/O size (minimum/optimal): 512 bytes / 512 bytes  
 Script header accepted.  
 Script header accepted.  
 Script header accepted.  
 Script header accepted.  
 Script header accepted.  
 Script header accepted.  
 Created a new GPT disklabel (GUID: 39F0215D-94EC-0145-B2BB-003165263541).  
/dev/sdc1: Created a new partition 1 of type 'Linux filesystem' and of size 2 GiB.  
/dev/sdc2: Created a new partition 2 of type 'Linux filesystem' and of size 511 MiB.  
/dev/sdc3: Done.  
New situation:  
Disklabel type: gpt  
Disk identifier: 39F0215D-94EC-0145-B2BB-003165263541  
Device       Start     End Sectors  Size Type  
/dev/sdc1     2048 4196351 4194304    2G Linux filesystem  
/dev/sdc2  4196352 5242846 1046495  511M Linux filesystem
```    

6. Соберите mdadm RAID1 на паре разделов 2 Гб
***Ответ***  
```
root@vagrant:/home/vagrant# mdadm --create --verbose /dev/md1 -l 1 -n 2 /dev/sd{b1,c1}  
mdadm: Note: this array has metadata at the start and  
    may not be suitable as a boot device.  If you plan to  
    store '/boot' on this device please ensure that  
    your boot-loader understands md/v1.x metadata, or use  
    --metadata=0.90  
mdadm: size set to 2094080K  
Continue creating array? y  
mdadm: Defaulting to version 1.2 metadata  
mdadm: array /dev/md1 started.
```
7. Соберите mdadm RAID0 на второй паре маленьких разделов.
***Ответ***  
```
root@vagrant:/home/vagrant# mdadm --create --verbose /dev/md0 -l 1 -n 2 /dev/sd{b2,c2}  
mdadm: Note: this array has metadata at the start and  
    may not be suitable as a boot device.  If you plan to  
    store '/boot' on this device please ensure that  
    your boot-loader understands md/v1.x metadata, or use  
    --metadata=0.90  
mdadm: size set to 522176K  
Continue creating array? y  
mdadm: Defaulting to version 1.2 metadata  
mdadm: array /dev/md0 started.
```
8. Создайте 2 независимых PV на получившихся md-устройствах.
***Ответ***  
```
root@vagrant:/home/vagrant# pvcreate /dev/md1 /dev/md0  
  Physical volume "/dev/md1" successfully created.  
  Physical volume "/dev/md0" successfully created.
 ```
9. Создайте общую volume-group на этих двух PV.
***Ответ***  
```
root@vagrant:/home/vagrant# vgcreate vg0 /dev/md1 /dev/md0  
  Volume group "vg0" successfully created
```
10. Создайте LV размером 100 Мб, указав его расположение на PV с RAID0.
***Ответ***  
```
root@vagrant:/home/vagrant# lvcreate -L 100M vg0 /dev/md0  
  Logical volume "lvol0" created.  
root@vagrant:/home/vagrant# lvs  
  LV     VG        Attr       LSize   Pool Origin Data%  Meta%  Move Log Cpy%Sync Convert  
  lvol0  vg0       -wi-a----- 100.00m  
  root   vgvagrant -wi-ao---- <62.54g  
  swap_1 vgvagrant -wi-ao---- 980.00m
 ```
11. Создайте mkfs.ext4 ФС на получившемся LV.
***Ответ***  
```
root@vagrant:/home/vagrant# mkfs.ext4 /dev/vg0/lvol0  
mke2fs 1.45.5 (07-Jan-2020)  
Creating filesystem with 25600 4k blocks and 25600 inodes  
Allocating group tables: done  
Writing inode tables: done  
Creating journal (1024 blocks): done  
Writing superblocks and filesystem accounting information: done
```
12. Смонтируйте этот раздел в любую директорию, например, /tmp/new.
***Ответ***  
```
root@vagrant:/home/vagrant# mount /dev/vg0/lvol0 /tmp/new
```
13. Поместите туда тестовый файл, например wget https://mirror.yandex.ru/ubuntu/ls-lR.gz -O /tmp/new/test.gz
***Ответ***  
```
root@vagrant:/home/vagrant# ls /tmp/new/  
lost+found  test.gz
```
14. Прикрепите вывод lsblk
***Ответ***  
```
root@vagrant:/home/vagrant# lsblk  
NAME                 MAJ:MIN RM  SIZE RO TYPE  MOUNTPOINT  
sda                    8:0    0   64G  0 disk  
├─sda1                 8:1    0  512M  0 part  /boot/efi  
├─sda2                 8:2    0    1K  0 part  
└─sda5                 8:5    0 63.5G  0 part  
  ├─vgvagrant-root   253:0    0 62.6G  0 lvm   /  
  └─vgvagrant-swap_1 253:1    0  980M  0 lvm   [SWAP]  
sdb                    8:16   0  2.5G  0 disk  
├─sdb1                 8:17   0    2G  0 part  
│ └─md1                9:1    0    2G  0 raid1  
└─sdb2                 8:18   0  511M  0 part  
  └─md0                9:0    0  510M  0 raid1  
    └─vg0-lvol0      253:2    0  100M  0 lvm   /tmp/new  
sdc                    8:32   0  2.5G  0 disk  
├─sdc1                 8:33   0    2G  0 part  
│ └─md1                9:1    0    2G  0 raid1  
└─sdc2                 8:34   0  511M  0 part  
  └─md0                9:0    0  510M  0 raid1  
    └─vg0-lvol0      253:2    0  100M  0 lvm   /tmp/new
   ```
15. Протестируйте целостность файла:
***Ответ***  
```
root@vagrant:/home/vagrant# gzip -t /tmp/new/test.gz  
root@vagrant:/home/vagrant# echo $?  
0
```
16. Используя pvmove, переместите содержимое PV с RAID0 на RAID1.
***Ответ***  
```
root@vagrant:/home/vagrant# pvmove /dev/md0  
  /dev/md0: Moved: 16.00%  
  /dev/md0: Moved: 100.00%  
root@vagrant:/home/vagrant# lsblk  
NAME                 MAJ:MIN RM  SIZE RO TYPE  MOUNTPOINT  
sda                    8:0    0   64G  0 disk  
├─sda1                 8:1    0  512M  0 part  /boot/efi  
├─sda2                 8:2    0    1K  0 part  
└─sda5                 8:5    0 63.5G  0 part  
  ├─vgvagrant-root   253:0    0 62.6G  0 lvm   /  
  └─vgvagrant-swap_1 253:1    0  980M  0 lvm   [SWAP]  
sdb                    8:16   0  2.5G  0 disk  
├─sdb1                 8:17   0    2G  0 part  
│ └─md1                9:1    0    2G  0 raid1  
│   └─vg0-lvol0      253:2    0  100M  0 lvm   /tmp/new  
└─sdb2                 8:18   0  511M  0 part  
  └─md0                9:0    0  510M  0 raid1  
sdc                    8:32   0  2.5G  0 disk  
├─sdc1                 8:33   0    2G  0 part  
│ └─md1                9:1    0    2G  0 raid1  
│   └─vg0-lvol0      253:2    0  100M  0 lvm   /tmp/new  
└─sdc2                 8:34   0  511M  0 part  
  └─md0                9:0    0  510M  0 raid1
  ```
17. Сделайте --fail на устройство в вашем RAID1 md.
***Ответ***  
```
mdadm /dev/md1 --fail /dev/sdb1
```
18. Подтвердите выводом dmesg, что RAID1 работает в деградированном состоянии.
***Ответ***  
```
root@vagrant:/home/vagrant# dmesg | grep md1  
[ 3760.548748] md/raid1:md1: not clean -- starting background reconstruction  
[ 3760.548754] md/raid1:md1: active with 2 out of 2 mirrors  
[ 3760.548806] md1: detected capacity change from 0 to 2144337920  
[ 3760.553447] md: resync of RAID array md1  
[ 3771.294864] md: md1: resync done.  
[ 4929.658462] md/raid1:md1: Disk failure on sdb1, disabling device.  
               md/raid1:md1: Operation continuing on 1 devices.
 ```
19. Протестируйте целостность файла, несмотря на "сбойный" диск он должен продолжать быть доступен:
***Ответ***  
```
root@vagrant:/home/vagrant# gzip -t /tmp/new/test.gz  
root@vagrant:/home/vagrant# echo $?  
0
```
20. Погасите тестовый хост, vagrant destroy
***Ответ***  
```
PS C:\HashiCorp\conf> vagrant.exe destroy  
    default: Are you sure you want to destroy the 'default' VM? [y/N] y  
==> default: Forcing shutdown of VM...  
==> default: Destroying VM and associated drives...
```


