**1** Попробуйте запустить playbook на окружении из test.yml, зафиксируйте какое значение имеет факт some_fact для указанного 
хоста при выполнении playbook'a.

***Ответ***
```commandline
   /var/run/media/k55/share/share/git/playbook    main ?6  ansible-playbook -i inventory/test.yml site.yml                                                                                        ✔ 

PLAY [Print os facts] ****************************************************************************************************************************************************************************************

TASK [Gathering Facts] ***************************************************************************************************************************************************************************************
[WARNING]: Platform linux on host localhost is using the discovered Python interpreter at /sbin/python3.10, but future installation of another Python interpreter could change the meaning of that path. See
https://docs.ansible.com/ansible-core/2.13/reference_appendices/interpreter_discovery.html for more information.
ok: [localhost]

TASK [Print OS] **********************************************************************************************************************************************************************************************
ok: [localhost] => {
    "msg": "Archlinux"
}

TASK [Print fact] ********************************************************************************************************************************************************************************************
ok: [localhost] => {
    "msg": 12
}

PLAY RECAP ***************************************************************************************************************************************************************************************************
localhost                  : ok=3    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0 
```

**2** Найдите файл с переменными (group_vars) в котором задаётся найденное в первом пункте значение и поменяйте его на 
'all default fact'.

***Ответ***
```commandline
    /var/run/media/k55/share/share/git/playbook    ansible !1 ?6  ansible-playbook -i inventory/test.yml site.yml                                                                                  ✔ 

PLAY [Print os facts] ****************************************************************************************************************************************************************************************

TASK [Gathering Facts] ***************************************************************************************************************************************************************************************
[WARNING]: Platform linux on host localhost is using the discovered Python interpreter at /sbin/python3.10, but future installation of another Python interpreter could change the meaning of that path. See
https://docs.ansible.com/ansible-core/2.13/reference_appendices/interpreter_discovery.html for more information.
ok: [localhost]

TASK [Print OS] **********************************************************************************************************************************************************************************************
ok: [localhost] => {
    "msg": "Archlinux"
}

TASK [Print fact] ********************************************************************************************************************************************************************************************
ok: [localhost] => {
    "msg": "all default fact"
}

PLAY RECAP ***************************************************************************************************************************************************************************************************
localhost                  : ok=3    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
```

**3** Воспользуйтесь подготовленным (используется docker) или создайте собственное окружение для проведения дальнейших испытаний.
***Ответ*** Готово

**4** Проведите запуск playbook на окружении из prod.yml. Зафиксируйте полученные значения some_fact для каждого из managed host.

***Ответ***
```commandline
   /var/run/media/k55/share/share/git/playbook    ansible !1 ?6  ansible-playbook --inventory  inventory/prod.yml site.yml  
[WARNING]: log file at /home/k55/.ansible/logs/ansible.log is not writeable and we cannot create it, aborting

[WARNING]: Ansible is being run in a world writable directory (/run/media/k55/share/share/git/playbook), ignoring it as an ansible.cfg source. For more information see
https://docs.ansible.com/ansible/devel/reference_appendices/config.html#cfg-in-world-writable-dir
PLAY [Print os facts] **************************************************************************************************************************

TASK [Gathering Facts] *************************************************************************************************************************
ok: [centos7]
ok: [ubuntu]

TASK [Print OS] ********************************************************************************************************************************
ok: [centos7] => {
    "msg": "CentOS"
}
ok: [ubuntu] => {
    "msg": "Ubuntu"
}

TASK [Print fact] ******************************************************************************************************************************
ok: [centos7] => {
    "msg": "el"
}
ok: [ubuntu] => {
    "msg": "deb"
}

PLAY RECAP *************************************************************************************************************************************
centos7                    : ok=3    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
ubuntu                     : ok=3    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   

```

**5** Добавьте факты в group_vars каждой из групп хостов так, чтобы для some_fact получились следующие значения: для 
deb - 'deb default fact', для el - 'el default fact'.

***Ответ***
```commandline
    /var/run/media/k55/share/share/git/playbook    ansible !1 ?6  cat group_vars/deb/examp.yml                                                                                           ✔  52s  
---
  some_fact: "deb default fact"
    /var/run/media/k55/share/share/git/playbook    ansible !1 ?6  cat group_vars/el/examp.yml                                                                                                    ✔ 
---
  some_fact: "el default fact"
```

**6** Повторите запуск playbook на окружении prod.yml. Убедитесь, что выдаются корректные значения для всех хостов.

***Ответ***
```commandline
   /var/run/media/k55/share/share/git/playbook    ansible !1 ?6  ansible-playbook --inventory  inventory/prod.yml site.yml
[WARNING]: log file at /home/k55/.ansible/logs/ansible.log is not writeable and we cannot create it, aborting

[WARNING]: Ansible is being run in a world writable directory (/run/media/k55/share/share/git/playbook), ignoring it as an ansible.cfg source. For more information see
https://docs.ansible.com/ansible/devel/reference_appendices/config.html#cfg-in-world-writable-dir
 PLAY [Print os facts] **************************************************************************************************************************

TASK [Gathering Facts] *************************************************************************************************************************
ok: [centos7]
ok: [ubuntu]

TASK [Print OS] ********************************************************************************************************************************
ok: [centos7] => {
    "msg": "CentOS"
}
ok: [ubuntu] => {
    "msg": "Ubuntu"
}

TASK [Print fact] ******************************************************************************************************************************
ok: [centos7] => {
    "msg": "el default fact"
}
ok: [ubuntu] => {
    "msg": "deb default fact"
}

PLAY RECAP *************************************************************************************************************************************
centos7                    : ok=3    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
ubuntu                     : ok=3    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
```

**7** При помощи ansible-vault зашифруйте факты в group_vars/deb и group_vars/el с паролем netology.

***Ответ***
```commandline
   /var/run/media/k55/share/share/git/playbook    ansible !1 ?6  ansible-vault encrypt group_vars/deb/examp.yml                                                                             INT ✘ 
[WARNING]: log file at /home/k55/.ansible/logs/ansible.log is not writeable and we cannot create it, aborting

[WARNING]: Ansible is being run in a world writable directory (/run/media/k55/share/share/git/playbook), ignoring it as an ansible.cfg source. For more information see
https://docs.ansible.com/ansible/devel/reference_appendices/config.html#cfg-in-world-writable-dir
New Vault password: 
Confirm New Vault password: 
Encryption successful
    /var/run/media/k55/share/share/git/playbook    ansible !1 ?6  ansible-vault encrypt group_vars/el/examp.yml                                                                           ✔  7s  
[WARNING]: log file at /home/k55/.ansible/logs/ansible.log is not writeable and we cannot create it, aborting

[WARNING]: Ansible is being run in a world writable directory (/run/media/k55/share/share/git/playbook), ignoring it as an ansible.cfg source. For more information see
https://docs.ansible.com/ansible/devel/reference_appendices/config.html#cfg-in-world-writable-dir
New Vault password: 
Confirm New Vault password: 
Encryption successful
```

**8** Запустите playbook на окружении prod.yml. При запуске ansible должен запросить у вас пароль. Убедитесь в работоспособности.

***Ответ***
```commandline
    /var/run/media/k55/share/share/git/playbook    ansible !1 ?6  ansible-playbook --inventory  inventory/prod.yml site.yml                                                               ✔  6s  
[WARNING]: log file at /home/k55/.ansible/logs/ansible.log is not writeable and we cannot create it, aborting

[WARNING]: Ansible is being run in a world writable directory (/run/media/k55/share/share/git/playbook), ignoring it as an ansible.cfg source. For more information see
https://docs.ansible.com/ansible/devel/reference_appendices/config.html#cfg-in-world-writable-dir

PLAY [Print os facts] **************************************************************************************************************************************************************************************
ERROR! Attempting to decrypt but no vault secrets found
    /var/run/media/k55/share/share/git/playbook    ansible !1 ?6  ansible-playbook --inventory  inventory/prod.yml site.yml --ask-vault-pass                                                   4 ✘ 
[WARNING]: log file at /home/k55/.ansible/logs/ansible.log is not writeable and we cannot create it, aborting

[WARNING]: Ansible is being run in a world writable directory (/run/media/k55/share/share/git/playbook), ignoring it as an ansible.cfg source. For more information see
https://docs.ansible.com/ansible/devel/reference_appendices/config.html#cfg-in-world-writable-dir
Vault password: 
PLAY [Print os facts] **************************************************************************************************************************

TASK [Gathering Facts] *************************************************************************************************************************
ok: [ubuntu]
ok: [centos7]

TASK [Print OS] ********************************************************************************************************************************
ok: [centos7] => {
    "msg": "CentOS"
}
ok: [ubuntu] => {
    "msg": "Ubuntu"
}

TASK [Print fact] ******************************************************************************************************************************
ok: [centos7] => {
    "msg": "el default fact"
}
ok: [ubuntu] => {
    "msg": "deb default fact"
}

PLAY RECAP *************************************************************************************************************************************
centos7                    : ok=3    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
ubuntu                     : ok=3    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   

```

**9** Посмотрите при помощи ansible-doc список плагинов для подключения. Выберите подходящий для работы на control node.

***Ответ***
plugin "local"

**10** В prod.yml добавьте новую группу хостов с именем local, в ней разместите localhost с необходимым типом подключения.

***Ответ***
```commandline
    /var/run/media/k55/share/share/git/playbook    ansible !1 ?6  cat ./inventory/prod.yml                                                                                               ✔  14s  
---
  el:
    hosts:
      centos:
        ansible_connection: docker
  deb:
    hosts:
      ubuntu:
        ansible_connection: docker
  local:
    hosts:
      localhost:
        ansible_connection: local    
```

**11** Запустите playbook на окружении prod.yml. При запуске ansible должен запросить у вас пароль. Убедитесь что 
факты some_fact для каждого из хостов определены из верных group_vars.

***Ответ***
```commandline
ansible-playbook --inventory  inventory/prod.yml site.yml --ask-vault-pass                                            4 ✘  7s  
[WARNING]: Ansible is being run in a world writable directory (/run/media/k55/share/share/git/playbook), ignoring it as an ansible.cfg source. For more information see
https://docs.ansible.com/ansible/devel/reference_appendices/config.html#cfg-in-world-writable-dir
Vault password: 

PLAY [Print os facts] **************************************************************************************************************************************************************************************

TASK [Gathering Facts] *************************************************************************************************************************************************************************************
fatal: [centos]: UNREACHABLE! => {"changed": false, "msg": "Failed to create temporary directory. In some cases, you may have been able to authenticate and did not have permissions on the target directory. Consider changing the remote tmp path in ansible.cfg to a path rooted in \"/tmp\", for more error information use -vvv. Failed command was: ( umask 77 && mkdir -p \"` echo /tmp `\"&& mkdir \"` echo /tmp/ansible-tmp-1658733132.0633495-23457-91709901055804 `\" && echo ansible-tmp-1658733132.0633495-23457-91709901055804=\"` echo /tmp/ansible-tmp-1658733132.0633495-23457-91709901055804 `\" ), exited with result 1", "unreachable": true}
fatal: [ubuntu]: UNREACHABLE! => {"changed": false, "msg": "Failed to create temporary directory. In some cases, you may have been able to authenticate and did not have permissions on the target directory. Consider changing the remote tmp path in ansible.cfg to a path rooted in \"/tmp\", for more error information use -vvv. Failed command was: ( umask 77 && mkdir -p \"` echo /tmp `\"&& mkdir \"` echo /tmp/ansible-tmp-1658733132.0827954-23464-68222226009827 `\" && echo ansible-tmp-1658733132.0827954-23464-68222226009827=\"` echo /tmp/ansible-tmp-1658733132.0827954-23464-68222226009827 `\" ), exited with result 1", "unreachable": true}
[WARNING]: Platform linux on host localhost is using the discovered Python interpreter at /sbin/python3.10, but future installation of another Python interpreter could change the meaning of that path.
See https://docs.ansible.com/ansible-core/2.13/reference_appendices/interpreter_discovery.html for more information.
ok: [localhost]
ok: [centos7]
ok: [ubuntu]

TASK [Print OS] ********************************************************************************************************************************************************************************************
ok: [localhost] => {
    "msg": "Archlinux"
}
ok: [centos7] => {
    "msg": "CentOS"
}
ok: [ubuntu] => {
    "msg": "Ubuntu"
}

TASK [Print fact] ******************************************************************************************************************************
ok: [localhost] => {
    "msg": "all default fact"
}
ok: [centos7] => {
    "msg": "el default fact"
}
ok: [ubuntu] => {
    "msg": "deb default fact"
}


PLAY RECAP *************************************************************************************************************************************
centos7                    : ok=3    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
localhost                  : ok=3    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
ubuntu                     : ok=3    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0  
```

**12** Заполните README.md ответами на вопросы. Сделайте git push в ветку master. В ответе отправьте ссылку на ваш 
открытый репозиторий с изменённым playbook и заполненным README.md.

***Ответ***
Готово
