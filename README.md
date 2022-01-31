**Задача 1**

Опишите своими словами основные преимущества применения на практике IaaC паттернов.
Какой из принципов IaaC является основополагающим?

***Ответ***
Ускорение разработки и доработки продукта, вывода его на рынок. Основополагающим принципом на мой взгляд является 
унификация конфигурации сред разработки для идентичного результата. 

**Задача 2**
Чем Ansible выгодно отличается от других систем управление конфигурациями?
Какой, на ваш взгляд, метод работы систем конфигурации более надёжный push или pull?

***Ответ***
Ansible бесплатный, 
На мой взгляд более эффективным является гибридынй) Но из предложенных методов это push. Доставка конфигурации 
выполняется управляющим сервером и вызывает дополнительной нагрузки на управляемом сервере. 



**Задача 3**
Установить на личный компьютер:
VirtualBox
Vagrant
Ansible

***Ответ***

VirtualBox
```commandline
PS C:\Users\vasilev\.vagrant.d> vagrant.exe global-status
id       name    provider   state    directory
-------------------------------------------------------------------------
177cfb9  default virtualbox poweroff C:/HashiCorp/conf
```

Vagrant
```commandline
PS C:\Users\vasilev\.vagrant.d> vagrant.exe version
Installed Version: 2.2.19
Latest Version: 2.2.19
PS C:\Users\vasilev\.vagrant.d> vagrant.exe box list
bento/ubuntu-20.04 (virtualbox, 202107.28.0)
```

Ansible:
```
vasilev@223-11 /etc/ansible
$ ansible --version
ansible 2.8.4
  config file = /etc/ansible/ansible.cfg
  configured module search path = ['/home/vasilev/.ansible/plugins/modules', '/usr/share/ansible/plugins/modules']
  ansible python module location = /usr/lib/python3.7/site-packages/ansible
  executable location = /usr/bin/ansible
  python version = 3.7.12 (default, Nov 23 2021, 18:58:07) [GCC 11.2.0]
```
