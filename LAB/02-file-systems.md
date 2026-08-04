## file systems
```
[path4cloud@rhel-9 ~]$ tree -FL 1 /
/
├── afs/
├── bin -> usr/bin/
├── boot/
├── dev/
├── efi/
├── etc/
├── home/
├── lib -> usr/lib/
├── lib64 -> usr/lib64/
├── media/
├── mnt/
├── opt/
├── proc/
├── root/
├── run/
├── sbin -> usr/sbin/
├── srv/
├── sys/
├── tmp/
├── usr/
└── var/

21 directories, 0 files
[path4cloud@rhel-9 ~]$
```

## Absolute and Relative Path
```
[path4cloud@rhel-9 ~]$ cd /var/log/sssd/
-bash: cd: /var/log/sssd/: Permission denied
[path4cloud@rhel-9 ~]$
[path4cloud@rhel-9 ~]$ cd /var/log/audit/
-bash: cd: /var/log/audit/: Permission denied
[path4cloud@rhel-9 ~]$ cd /etc/NetworkManager/system-connections/
[path4cloud@rhel-9 system-connections]$ ls
'Wired connection 1.nmconnection'  'Wired connection 2.nmconnection'
[path4cloud@rhel-9 system-connections]$
[path4cloud@rhel-9 system-connections]$ cd ../..
[path4cloud@rhel-9 etc]$ cd ~
[path4cloud@rhel-9 ~]$ cd -
/etc
[path4cloud@rhel-9 etc]$ pwd
/etc
```
