### Задание 1
```
---
elasticsearch:
  hosts:
    elasticsearch:
      ansible_connection: ssh
      ansible_host: 127.0.0.1
      ansible_user: root
      ansible_port: 2222
```
***
### Задание 2
> Добавил  play для установки Kibana
***
### Задание 3-4
> Создал task с использованием необходимых модулей
***
### Задание 5
```
falconow@falconow:~/lesson/ansible_8.2$ ansible-lint site.yml 
WARNING  Overriding detected file kind 'yaml' with 'playbook' for given positional argument: site.yml
WARNING  Listing 5 violation(s) that are fatal
risky-file-permissions: File permissions unset or incorrect
site.yml:10 Task/Handler: Upload .tar.gz file containing binaries from local storage

risky-file-permissions: File permissions unset or incorrect
site.yml:17 Task/Handler: Ensure installation dir exists

risky-file-permissions: File permissions unset or incorrect
site.yml:33 Task/Handler: Export environment variables

risky-file-permissions: File permissions unset or incorrect
site.yml:53 Task/Handler: Create directrory for Elasticsearch

risky-file-permissions: File permissions unset or incorrect
site.yml:68 Task/Handler: Set environment Elastic

You can skip specific rules or tags by adding them to your configuration file:
# .ansible-lint
warn_list:  # or 'skip_list' to silence them completely
  - experimental  # all rules tagged as experimental

Finished with 0 failure(s), 5 warning(s) on 1 files.
falconow@falconow:~/lesson/ansible_8.2$ 
```

> Исправил недочеты
```
falconow@falconow:~/lesson/ansible_8.2$ ansible-lint site.yml 
WARNING  Overriding detected file kind 'yaml' with 'playbook' for given positional argument: site.yml
falconow@falconow:~/lesson/ansible_8.2$ 
```
### Задание 6
```
falconow@falconow:~/lesson/ansible_8.2$ ansible-playbook -i inventory/prod.yml site.yml --check
[WARNING]: Found both group and host with same name: elasticsearch

PLAY [Install Java] **************************************************************************************************************************************

TASK [Gathering Facts] ***********************************************************************************************************************************
ok: [elasticsearch]

TASK [Set facts for Java 11 vars] ************************************************************************************************************************
ok: [elasticsearch]

TASK [Upload .tar.gz file containing binaries from local storage] ****************************************************************************************
changed: [elasticsearch]

TASK [Ensure installation dir exists] ********************************************************************************************************************
changed: [elasticsearch]

TASK [Extract java in the installation directory] ********************************************************************************************************
An exception occurred during task execution. To see the full traceback, use -vvv. The error was: NoneType: None
fatal: [elasticsearch]: FAILED! => {"changed": false, "msg": "dest '/opt/jdk/11.0.12' must be an existing dir"}

PLAY RECAP ***********************************************************************************************************************************************
elasticsearch              : ok=4    changed=2    unreachable=0    failed=1    skipped=0    rescued=0    ignored=0   
```

> Получаем ошибку так как директория еще не создана
***
### Задание 7
```
falconow@falconow:~/lesson/ansible_8.2$ ansible-playbook -i inventory/prod.yml site.yml --diff
[WARNING]: Found both group and host with same name: elasticsearch

PLAY [Install Java] **************************************************************************************************************************************

TASK [Gathering Facts] ***********************************************************************************************************************************
ok: [elasticsearch]

TASK [Set facts for Java 11 vars] ************************************************************************************************************************
ok: [elasticsearch]

TASK [Upload .tar.gz file containing binaries from local storage] ****************************************************************************************
diff skipped: source file size is greater than 104448
changed: [elasticsearch]

TASK [Ensure installation dir exists] ********************************************************************************************************************
--- before
+++ after
@@ -1,5 +1,5 @@
 {
-    "mode": "0755",
+    "mode": "0644",
     "path": "/opt/jdk/11.0.12",
-    "state": "absent"
+    "state": "directory"
 }

changed: [elasticsearch]

TASK [Extract java in the installation directory] ********************************************************************************************************
changed: [elasticsearch]

TASK [Export environment variables] **********************************************************************************************************************
--- before
+++ after: /home/falconow/.ansible/tmp/ansible-local-12679p7rei926/tmpcyniqswb/jdk.sh.j2
@@ -0,0 +1,5 @@
+# Warning: This file is Ansible Managed, manual changes will be overwritten on next playbook run.
+#!/usr/bin/env bash
+
+export JAVA_HOME=/opt/jdk/11.0.12
+export PATH=$PATH:$JAVA_HOME/bin
\ No newline at end of file

changed: [elasticsearch]

PLAY [Install Elasticsearch] *****************************************************************************************************************************

TASK [Gathering Facts] ***********************************************************************************************************************************
ok: [elasticsearch]

TASK [Upload tar.gz Elasticsearch from remote URL] *******************************************************************************************************
changed: [elasticsearch]

TASK [Create directrory for Elasticsearch] ***************************************************************************************************************
--- before
+++ after
@@ -1,5 +1,5 @@
 {
-    "mode": "0755",
+    "mode": "0644",
     "path": "/opt/elastic/7.10.1",
-    "state": "absent"
+    "state": "directory"
 }

changed: [elasticsearch]

TASK [Extract Elasticsearch in the installation directory] ***********************************************************************************************
changed: [elasticsearch]

TASK [Set environment Elastic] ***************************************************************************************************************************
--- before
+++ after: /home/falconow/.ansible/tmp/ansible-local-12679p7rei926/tmpho5kylih/elk.sh.j2
@@ -0,0 +1,5 @@
+# Warning: This file is Ansible Managed, manual changes will be overwritten on next playbook run.
+#!/usr/bin/env bash
+
+export ES_HOME=/opt/elastic/7.10.1
+export PATH=$PATH:$ES_HOME/bin
\ No newline at end of file

changed: [elasticsearch]

PLAY [Install Kibana] ************************************************************************************************************************************

TASK [Gathering Facts] ***********************************************************************************************************************************
ok: [elasticsearch]

TASK [Upload tar.gz Kibana from remote URL] **************************************************************************************************************
changed: [elasticsearch]

TASK [Create directrory for Kibana] **********************************************************************************************************************
--- before
+++ after
@@ -1,5 +1,5 @@
 {
-    "mode": "0755",
+    "mode": "0644",
     "path": "/opt/kibana/7.14.1",
-    "state": "absent"
+    "state": "directory"
 }

changed: [elasticsearch]

TASK [Extract Kibana in the installation directory] ******************************************************************************************************
changed: [elasticsearch]

TASK [Set environment Kibana] ****************************************************************************************************************************
--- before
+++ after: /home/falconow/.ansible/tmp/ansible-local-13932ahas0tsp/tmppmpampd3/kib.sh.j2
@@ -0,0 +1,5 @@
+# Warning: This file is Ansible Managed, manual changes will be overwritten on next playbook run.
+#!/usr/bin/env bash
+
+export KIBANA_HOME=/opt/kibana/7.10.1
+export PATH=$PATH:$KIBANA_HOME/bin
\ No newline at end of file

changed: [elasticsearch]

PLAY RECAP ***********************************************************************************************************************************************
elasticsearch              : ok=13   changed=1    unreachable=0    failed=0    skipped=3    rescued=0    ignored=0   
```
***
###  Задание 8
```
falconow@falconow:~/lesson/ansible_8.2$ ansible-playbook -i inventory/prod.yml site.yml --diff
[WARNING]: Found both group and host with same name: elasticsearch

PLAY [Install Java] **************************************************************************************************************************************

TASK [Gathering Facts] ***********************************************************************************************************************************
ok: [elasticsearch]

TASK [Set facts for Java 11 vars] ************************************************************************************************************************
ok: [elasticsearch]

TASK [Upload .tar.gz file containing binaries from local storage] ****************************************************************************************
ok: [elasticsearch]

TASK [Ensure installation dir exists] ********************************************************************************************************************
ok: [elasticsearch]

TASK [Extract java in the installation directory] ********************************************************************************************************
skipping: [elasticsearch]

TASK [Export environment variables] **********************************************************************************************************************
ok: [elasticsearch]

PLAY [Install Elasticsearch] *****************************************************************************************************************************

TASK [Gathering Facts] ***********************************************************************************************************************************
ok: [elasticsearch]

TASK [Upload tar.gz Elasticsearch from remote URL] *******************************************************************************************************
ok: [elasticsearch]

TASK [Create directrory for Elasticsearch] ***************************************************************************************************************
ok: [elasticsearch]

TASK [Extract Elasticsearch in the installation directory] ***********************************************************************************************
skipping: [elasticsearch]

TASK [Set environment Elastic] ***************************************************************************************************************************
ok: [elasticsearch]

PLAY [Install Kibana] ************************************************************************************************************************************

TASK [Gathering Facts] ***********************************************************************************************************************************
ok: [elasticsearch]

TASK [Upload tar.gz Kibana from remote URL] **************************************************************************************************************
ok: [elasticsearch]

TASK [Create directrory for Kibana] **********************************************************************************************************************
ok: [elasticsearch]

TASK [Extract Kibana in the installation directory] ******************************************************************************************************
skipping: [elasticsearch]

TASK [Set environment Kibana] ****************************************************************************************************************************
ok: [elasticsearch]

PLAY RECAP ***********************************************************************************************************************************************
elasticsearch              : ok=13   changed=0    unreachable=0    failed=0    skipped=3    rescued=0    ignored=0   
```
> Запустили еще раз, видим что playbook идемпотентен