# SYSTEMD
This is the first process and parent of all other processes in system.
```
[root@rhel-9 ~]# pstree
systemd─┬─NetworkManager───2*[{NetworkManager}]
        ├─agetty
        ├─alertmanager───7*[{alertmanager}]
        ├─atd
        ├─auditd─┬─sedispatch
        │        └─2*[{auditd}]
        ├─chronyd
        ├─crond
        ├─dbus-broker-lau───dbus-broker
        ├─dnsmasq
        ├─irqbalance───{irqbalance}
        ├─node_exporter───7*[{node_exporter}]
        ├─polkitd───5*[{polkitd}]
        ├─prometheus───7*[{prometheus}]
        ├─rhcd─┬─rhc-package-man───4*[{rhc-package-man}]
        │      └─10*[{rhcd}]
        ├─rhsmcertd───{rhsmcertd}
        ├─rsyslogd───2*[{rsyslogd}]
        ├─rtkit-daemon───2*[{rtkit-daemon}]
        ├─sshd───sshd-session───sshd-session───bash───sudo───sudo───bash───pstree
        ├─stratisd───5*[{stratisd}]
        ├─systemd───(sd-pam)
        ├─systemd-journal
        ├─systemd-logind
        ├─systemd-udevd
        ├─tuned───3*[{tuned}]
        ├─udisksd───4*[{udisksd}]
        └─upowerd───2*[{upowerd}]
```
Systemd PID (Process ID) is 1
```
[root@rhel-9 ~]# ps aux | grep -i systemd
root           1  0.0  0.8 109596 14224 ?        Ss   Aug24   0:13 /usr/lib/systemd/systemd --switched-root --system --deserialize 28
root         686  0.0  0.9  53464 16660 ?        Ss   Aug24   0:06 /usr/lib/systemd/systemd-journald
root         701  0.0  0.5  38672  9568 ?        Ss   Aug24   0:00 /usr/lib/systemd/systemd-udevd
root         864  0.0  0.6  31908 10628 ?        Ss   Aug24   0:00 /usr/lib/systemd/systemd-logind
ansible+     932  0.0  0.6  25416 11148 ?        Ss   Aug24   0:00 /usr/lib/systemd/systemd --user
root       10476  0.0  0.1   6424  2628 pts/1    S+   15:57   0:00 grep --color=auto -i systemd
```
We can list all the Process with `ps` command:
```
[root@rhel-9 ~]# ps aux
USER         PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
root           1  0.0  0.8 109596 14224 ?        Ss   Aug24   0:13 /usr/lib/systemd/systemd --switched-root --system --deserialize 28
root           2  0.0  0.0      0     0 ?        S    Aug24   0:00 [kthreadd]
root           3  0.0  0.0      0     0 ?        S    Aug24   0:00 [pool_workqueue_]
root           4  0.0  0.0      0     0 ?        I<   Aug24   0:00 [kworker/R-rcu_gp]
root           5  0.0  0.0      0     0 ?        I<   Aug24   0:00 [kworker/R-sync_wq]
root           6  0.0  0.0      0     0 ?        I<   Aug24   0:00 [kworker/R-slub_flushwq]
root           7  0.0  0.0      0     0 ?        I<   Aug24   0:00 [kworker/R-netns]
root           9  0.0  0.0      0     0 ?        I<   Aug24   0:00 [kworker/0:0H-events_highpri]
root          10  0.0  0.0      0     0 ?        I    Aug24   0:00 [kworker/u8:0-events_unbound]
root          11  0.0  0.0      0     0 ?        I<   Aug24   0:00 [kworker/R-mm_percpu_wq]
root          12  0.0  0.0      0     0 ?        I    Aug24   0:00 [kworker/u8:1-netns]
root          13  0.0  0.0      0     0 ?        I    Aug24   0:00 [rcu_tasks_kthre]
root          14  0.0  0.0      0     0 ?        I    Aug24   0:00 [rcu_tasks_rude_]
root          15  0.0  0.0      0     0 ?        I    Aug24   0:00 [rcu_tasks_trace]
root          16  0.0  0.0      0     0 ?        S    Aug24   0:00 [ksoftirqd/0]
root          17  0.0  0.0      0     0 ?        I    Aug24   0:03 [rcu_preempt]
root          18  0.0  0.0      0     0 ?        S    Aug24   0:00 [rcu_exp_par_gp_]
root          19  0.0  0.0      0     0 ?        S    Aug24   0:00 [rcu_exp_gp_kthr]
root          20  0.0  0.0      0     0 ?        S    Aug24   0:00 [migration/0]
root          21  0.0  0.0      0     0 ?        S    Aug24   0:00 [idle_inject/0]
```

