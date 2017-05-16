Организация псевдо глобальной переменной.
Иногда очень надо, что бы переменная была глобальная для всей книги.
set_fact работает только для одного хоста и не может менять глобальные значения.

Пример:
```
sergey@sergey-VirtualBox:~/mount/emulate_global_var$ ansible-playbook problem.yml --inventory-file=./inventory/test_inventoty/hosts -vvvvvv
Using /home/sergey/mount/emulate_global_var/ansible.cfg as config file
Loading callback plugin default of type stdout, v2.0 from /usr/lib/python2.7/dist-packages/ansible/plugins/callback/__init__.pyc

PLAYBOOK: problem.yml **************************************************************************************************************************************************************************************
2 plays in problem.yml

PLAY [01] **************************************************************************************************************************************************************************************************
META: ran handlers

TASK [role_one_bad : Set fact] *****************************************************************************************************************************************************************************
task path: /home/sergey/mount/emulate_global_var/roles/role_one_bad/tasks/main.yml:3
ok: [host_one] => {
    "ansible_facts": {
        "one_fact": "global value"
    }, 
    "changed": false
}

TASK [role_one_bad : Echo] *********************************************************************************************************************************************************************************
task path: /home/sergey/mount/emulate_global_var/roles/role_one_bad/tasks/main.yml:6
Using module file /usr/lib/python2.7/dist-packages/ansible/modules/commands/command.py
<host_one> ESTABLISH LOCAL CONNECTION FOR USER: sergey
<host_one> EXEC /bin/sh -c 'echo ~ && sleep 0'
<host_one> EXEC /bin/sh -c '( umask 77 && mkdir -p "` echo /home/sergey/.ansible/tmp/ansible-tmp-1494944741.6-109279318635705 `" && echo ansible-tmp-1494944741.6-109279318635705="` echo /home/sergey/.ansible/tmp/ansible-tmp-1494944741.6-109279318635705 `" ) && sleep 0'
<host_one> PUT /tmp/tmpcVkI4q TO /home/sergey/.ansible/tmp/ansible-tmp-1494944741.6-109279318635705/command.py
<host_one> EXEC /bin/sh -c 'chmod u+x /home/sergey/.ansible/tmp/ansible-tmp-1494944741.6-109279318635705/ /home/sergey/.ansible/tmp/ansible-tmp-1494944741.6-109279318635705/command.py && sleep 0'
<host_one> EXEC /bin/sh -c '/usr/bin/python /home/sergey/.ansible/tmp/ansible-tmp-1494944741.6-109279318635705/command.py; rm -rf "/home/sergey/.ansible/tmp/ansible-tmp-1494944741.6-109279318635705/" > /dev/null 2>&1 && sleep 0'
changed: [host_one] => {
    "changed": true, 
    "cmd": "echo \">\"\"global value\"\"<\"", 
    "delta": "0:00:00.002040", 
    "end": "2017-05-16 17:25:41.889343", 
    "invocation": {
        "module_args": {
            "_raw_params": "echo \">\"\"global value\"\"<\"", 
            "_uses_shell": true, 
            "chdir": null, 
            "creates": null, 
            "executable": null, 
            "removes": null, 
            "warn": true
        }
    }, 
    "rc": 0, 
    "start": "2017-05-16 17:25:41.887303", 
    "stderr": "", 
    "stderr_lines": [], 
    "stdout": ">global value<", 
    "stdout_lines": [
        ">global value<"
    ]
}
META: ran handlers
META: ran handlers

PLAY [02] **************************************************************************************************************************************************************************************************
META: ran handlers

TASK [role_two_bad : Echo] *********************************************************************************************************************************************************************************
task path: /home/sergey/mount/emulate_global_var/roles/role_two_bad/tasks/main.yml:2
fatal: [host_two]: FAILED! => {
    "failed": true, 
    "msg": "the field 'args' has an invalid value, which appears to include a variable that is undefined. The error was: 'one_fact' is undefined\n\nThe error appears to have been in '/home/sergey/mount/emulate_global_var/roles/role_two_bad/tasks/main.yml': line 2, column 3, but may\nbe elsewhere in the file depending on the exact syntax problem.\n\nThe offending line appears to be:\n\n---\n- name: \"Echo\"\n  ^ here\n"
}
	to retry, use: --limit @/home/sergey/mount/emulate_global_var/problem.retry

PLAY RECAP *************************************************************************************************************************************************************************************************
host_one                   : ok=2    changed=1    unreachable=0    failed=0   
host_two                   : ok=0    changed=0    unreachable=0    failed=1   

sergey@sergey-VirtualBox:~/mount/emulate_global_var$ 
```
А очень хочется. Для решения проблемы создается псевдо хост, для хранения значений.
```
sergey@sergey-VirtualBox:~/mount/emulate_global_var$ ansible-playbook solve.yml --inventory-file=./inventory/test_inventoty/hosts -vvvvvv
Using /home/sergey/mount/emulate_global_var/ansible.cfg as config file
Loading callback plugin default of type stdout, v2.0 from /usr/lib/python2.7/dist-packages/ansible/plugins/callback/__init__.pyc

PLAYBOOK: solve.yml ****************************************************************************************************************************************************************************************
3 plays in solve.yml

PLAY [00] **************************************************************************************************************************************************************************************************
META: ran handlers

TASK [role_invent : Set fact] ******************************************************************************************************************************************************************************
task path: /home/sergey/mount/emulate_global_var/roles/role_invent/tasks/main.yml:2
ok: [host_inventory] => {
    "ansible_facts": {
        "one_fact": "global value"
    }, 
    "changed": false
}

TASK [role_invent : Echo] **********************************************************************************************************************************************************************************
task path: /home/sergey/mount/emulate_global_var/roles/role_invent/tasks/main.yml:5
Using module file /usr/lib/python2.7/dist-packages/ansible/modules/commands/command.py
<host_inventory> ESTABLISH LOCAL CONNECTION FOR USER: sergey
<host_inventory> EXEC /bin/sh -c 'echo ~ && sleep 0'
<host_inventory> EXEC /bin/sh -c '( umask 77 && mkdir -p "` echo /home/sergey/.ansible/tmp/ansible-tmp-1494945249.85-181196652389063 `" && echo ansible-tmp-1494945249.85-181196652389063="` echo /home/sergey/.ansible/tmp/ansible-tmp-1494945249.85-181196652389063 `" ) && sleep 0'
<host_inventory> PUT /tmp/tmpl3hTot TO /home/sergey/.ansible/tmp/ansible-tmp-1494945249.85-181196652389063/command.py
<host_inventory> EXEC /bin/sh -c 'chmod u+x /home/sergey/.ansible/tmp/ansible-tmp-1494945249.85-181196652389063/ /home/sergey/.ansible/tmp/ansible-tmp-1494945249.85-181196652389063/command.py && sleep 0'
<host_inventory> EXEC /bin/sh -c '/usr/bin/python /home/sergey/.ansible/tmp/ansible-tmp-1494945249.85-181196652389063/command.py; rm -rf "/home/sergey/.ansible/tmp/ansible-tmp-1494945249.85-181196652389063/" > /dev/null 2>&1 && sleep 0'
changed: [host_inventory] => {
    "changed": true, 
    "cmd": "echo \">\"\"global value\"\"<\"", 
    "delta": "0:00:00.002504", 
    "end": "2017-05-16 17:34:10.134717", 
    "invocation": {
        "module_args": {
            "_raw_params": "echo \">\"\"global value\"\"<\"", 
            "_uses_shell": true, 
            "chdir": null, 
            "creates": null, 
            "executable": null, 
            "removes": null, 
            "warn": true
        }
    }, 
    "rc": 0, 
    "start": "2017-05-16 17:34:10.132213", 
    "stderr": "", 
    "stderr_lines": [], 
    "stdout": ">global value<", 
    "stdout_lines": [
        ">global value<"
    ]
}
META: ran handlers
META: ran handlers

PLAY [01] **************************************************************************************************************************************************************************************************
META: ran handlers

TASK [role_one : Echo] *************************************************************************************************************************************************************************************
task path: /home/sergey/mount/emulate_global_var/roles/role_one/tasks/main.yml:3
Using module file /usr/lib/python2.7/dist-packages/ansible/modules/commands/command.py
<host_one> ESTABLISH LOCAL CONNECTION FOR USER: sergey
<host_one> EXEC /bin/sh -c 'echo ~ && sleep 0'
<host_one> EXEC /bin/sh -c '( umask 77 && mkdir -p "` echo /home/sergey/.ansible/tmp/ansible-tmp-1494945250.24-274745448736423 `" && echo ansible-tmp-1494945250.24-274745448736423="` echo /home/sergey/.ansible/tmp/ansible-tmp-1494945250.24-274745448736423 `" ) && sleep 0'
<host_one> PUT /tmp/tmpoiCkEJ TO /home/sergey/.ansible/tmp/ansible-tmp-1494945250.24-274745448736423/command.py
<host_one> EXEC /bin/sh -c 'chmod u+x /home/sergey/.ansible/tmp/ansible-tmp-1494945250.24-274745448736423/ /home/sergey/.ansible/tmp/ansible-tmp-1494945250.24-274745448736423/command.py && sleep 0'
<host_one> EXEC /bin/sh -c '/usr/bin/python /home/sergey/.ansible/tmp/ansible-tmp-1494945250.24-274745448736423/command.py; rm -rf "/home/sergey/.ansible/tmp/ansible-tmp-1494945250.24-274745448736423/" > /dev/null 2>&1 && sleep 0'
changed: [host_one] => {
    "changed": true, 
    "cmd": "echo \">\"\"global value\"\"<\"", 
    "delta": "0:00:00.004472", 
    "end": "2017-05-16 17:34:10.552063", 
    "invocation": {
        "module_args": {
            "_raw_params": "echo \">\"\"global value\"\"<\"", 
            "_uses_shell": true, 
            "chdir": null, 
            "creates": null, 
            "executable": null, 
            "removes": null, 
            "warn": true
        }
    }, 
    "rc": 0, 
    "start": "2017-05-16 17:34:10.547591", 
    "stderr": "", 
    "stderr_lines": [], 
    "stdout": ">global value<", 
    "stdout_lines": [
        ">global value<"
    ]
}
META: ran handlers
META: ran handlers

PLAY [02] **************************************************************************************************************************************************************************************************
META: ran handlers

TASK [role_two : Echo] *************************************************************************************************************************************************************************************
task path: /home/sergey/mount/emulate_global_var/roles/role_two/tasks/main.yml:2
Using module file /usr/lib/python2.7/dist-packages/ansible/modules/commands/command.py
<host_two> ESTABLISH LOCAL CONNECTION FOR USER: sergey
<host_two> EXEC /bin/sh -c 'echo ~ && sleep 0'
<host_two> EXEC /bin/sh -c '( umask 77 && mkdir -p "` echo /home/sergey/.ansible/tmp/ansible-tmp-1494945250.73-35388986122954 `" && echo ansible-tmp-1494945250.73-35388986122954="` echo /home/sergey/.ansible/tmp/ansible-tmp-1494945250.73-35388986122954 `" ) && sleep 0'
<host_two> PUT /tmp/tmp9u2GEK TO /home/sergey/.ansible/tmp/ansible-tmp-1494945250.73-35388986122954/command.py
<host_two> EXEC /bin/sh -c 'chmod u+x /home/sergey/.ansible/tmp/ansible-tmp-1494945250.73-35388986122954/ /home/sergey/.ansible/tmp/ansible-tmp-1494945250.73-35388986122954/command.py && sleep 0'
<host_two> EXEC /bin/sh -c '/usr/bin/python /home/sergey/.ansible/tmp/ansible-tmp-1494945250.73-35388986122954/command.py; rm -rf "/home/sergey/.ansible/tmp/ansible-tmp-1494945250.73-35388986122954/" > /dev/null 2>&1 && sleep 0'
changed: [host_two] => {
    "changed": true, 
    "cmd": "echo \">\"\"global value\"\"<\"", 
    "delta": "0:00:00.002103", 
    "end": "2017-05-16 17:34:11.010817", 
    "invocation": {
        "module_args": {
            "_raw_params": "echo \">\"\"global value\"\"<\"", 
            "_uses_shell": true, 
            "chdir": null, 
            "creates": null, 
            "executable": null, 
            "removes": null, 
            "warn": true
        }
    }, 
    "rc": 0, 
    "start": "2017-05-16 17:34:11.008714", 
    "stderr": "", 
    "stderr_lines": [], 
    "stdout": ">global value<", 
    "stdout_lines": [
        ">global value<"
    ]
}
META: ran handlers
META: ran handlers

PLAY RECAP *************************************************************************************************************************************************************************************************
host_inventory             : ok=2    changed=1    unreachable=0    failed=0   
host_one                   : ok=1    changed=1    unreachable=0    failed=0   
host_two                   : ok=1    changed=1    unreachable=0    failed=0   

sergey@sergey-VirtualBox:~/mount/emulate_global_var$ 
```
