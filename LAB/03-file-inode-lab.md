## Checking the inode size for a xfs file system 
```
[path4cloud@rhel-9 ~]$ xfs_info /
meta-data=/dev/sda4              isize=512    agcount=46, agsize=196671 blks
         =                       sectsz=512   attr=2, projid32bit=1
         =                       crc=1        finobt=1, sparse=1, rmapbt=0
         =                       reflink=1    bigtime=1 inobtcount=1 nrext64=0
data     =                       bsize=4096   blocks=8861179, imaxpct=25
         =                       sunit=0      swidth=0 blks
naming   =version 2              bsize=4096   ascii-ci=0, ftype=1
log      =internal log           bsize=4096   blocks=16384, version=2
         =                       sectsz=512   sunit=0 blks, lazy-count=1
realtime =none                   extsz=4096   blocks=0, rtextents=0
```

## checking the inode for a directory and file
```
[path4cloud@rhel-9 ~]$ ls -li ping.sh
2190327 -rwxr-xr-x. 1 path4cloud path4cloud 25 Apr 29 18:34 ping.sh
[path4cloud@rhel-9 ~]$
[path4cloud@rhel-9 ~]$
[path4cloud@rhel-9 ~]$ ls -ldi project/
1782673 drwxr-xr-x. 6 path4cloud path4cloud 51 Apr 29 19:59 project/
```
- here first column represents the inode for file or directory.

## Create a Hardlink for a file
```
[path4cloud@rhel-9 ~]$ ln ping.sh newping.sh
[path4cloud@rhel-9 ~]$
[path4cloud@rhel-9 ~]$ ls -li *ping.sh
2190327 -rwxr-xr-x. 2 path4cloud path4cloud 25 Apr 29 18:34 newping.sh
2190327 -rwxr-xr-x. 2 path4cloud path4cloud 25 Apr 29 18:34 ping.sh
```
- Notice, here inode are same for both new files since file allocation tables keeps the track of same inode and inode knows the path of actual data.
-  Now, link count for that inode is 2 (next to permission) which was 1 in previous output.
- So, if we update any of file, changes will presented to both file.
```
[path4cloud@rhel-9 ~]$ cat newping.sh
#!/bin/bash
ping 8.8.8.8
[path4cloud@rhel-9 ~]$ cat ping.sh
#!/bin/bash
ping 8.8.8.8
[path4cloud@rhel-9 ~]$ echo "updated in ping.sh" >> ping.sh
[path4cloud@rhel-9 ~]$
[path4cloud@rhel-9 ~]$ cat ping.sh
#!/bin/bash
ping 8.8.8.8
updated in ping.sh
[path4cloud@rhel-9 ~]$ cat newping.sh
#!/bin/bash
ping 8.8.8.8
updated in ping.sh
```
- If a time stamp or a content has been changed, we can see it being applied to both files.
- Lets try to update the time stamp with touch command (since touch will create a empty 0 byte size file if file doesn't exist OR if a file exist then it just updated the time stamp of file)
```
[path4cloud@rhel-9 ~]$ ls -li *ping.sh
2190327 -rwxr-xr-x. 2 path4cloud path4cloud 44 Apr 30 01:32 newping.sh
2190327 -rwxr-xr-x. 2 path4cloud path4cloud 44 Apr 30 01:32 ping.sh
[path4cloud@rhel-9 ~]$
[path4cloud@rhel-9 ~]$ date
Thu Apr 30 01:33:26 AM IST 2026
[path4cloud@rhel-9 ~]$
[path4cloud@rhel-9 ~]$
[path4cloud@rhel-9 ~]$ touch newping.sh
[path4cloud@rhel-9 ~]$ ls -li *ping.sh
2190327 -rwxr-xr-x. 2 path4cloud path4cloud 44 Apr 30 01:34 newping.sh
2190327 -rwxr-xr-x. 2 path4cloud path4cloud 44 Apr 30 01:34 ping.sh
[path4cloud@rhel-9 ~]$
[path4cloud@rhel-9 ~]$ cat newping.sh
#!/bin/bash
ping 8.8.8.8
updated in ping.sh
[path4cloud@rhel-9 ~]$ cat ping.sh
#!/bin/bash
ping 8.8.8.8
updated in ping.sh
[path4cloud@rhel-9 ~]$
```
- if we remove one of file, in that case also, we have data exist at same inode.
```
[path4cloud@rhel-9 ~]$ rm newping.sh
[path4cloud@rhel-9 ~]$ ls -li ping.sh
2190327 -rwxr-xr-x. 1 path4cloud path4cloud 44 Apr 30 01:34 ping.sh
```

## Create a Hardlink for a directory
```
[path4cloud@rhel-9 ~]$ ls
a  ansible-ops  hostinfo  ping.sh  project
[path4cloud@rhel-9 ~]$ ln project/ new_project
ln: project/: hard link not allowed for directory
```
- Hardlink is not allowed for a directory, but we can create a soft link

## Create a Softlink for a directory
```
[path4cloud@rhel-9 ~]$ ln -s project/ new_project
[path4cloud@rhel-9 ~]$ ll
total 16
-rw-r--r--. 1 path4cloud path4cloud   30 Apr 29 20:25 a
drwxr-xr-x. 3 path4cloud path4cloud 4096 Apr  5 23:03 ansible-ops
-rw-r--r--. 1 path4cloud path4cloud   32 Apr 29 20:33 hostinfo
lrwxrwxrwx. 1 path4cloud path4cloud    8 Apr 30 01:40 new_project -> project/
-rwxr-xr-x. 1 path4cloud path4cloud   44 Apr 30 01:34 ping.sh
drwxr-xr-x. 6 path4cloud path4cloud   51 Apr 29 19:59 project
[path4cloud@rhel-9 ~]$
[path4cloud@rhel-9 ~]$ ls -ldi project/ new_project/
1782673 drwxr-xr-x. 6 path4cloud path4cloud 51 Apr 29 19:59 new_project/
1782673 drwxr-xr-x. 6 path4cloud path4cloud 51 Apr 29 19:59 project/

```
- Here, we can see with `-s` option, it has created a new sybolink to the project directory with new_project directory which shares the same inode
- But if we check the inode for file which is linked to project directory, that has a different inode.
```
[path4cloud@rhel-9 ~]$ ls -li
total 16
  166939 -rw-r--r--. 1 path4cloud path4cloud   30 Apr 29 20:25 a
17119681 drwxr-xr-x. 3 path4cloud path4cloud 4096 Apr  5 23:03 ansible-ops
 2190330 -rw-r--r--. 1 path4cloud path4cloud   32 Apr 29 20:33 hostinfo
 2190331 lrwxrwxrwx. 1 path4cloud path4cloud    8 Apr 30 01:40 new_project -> project/
 2190327 -rwxr-xr-x. 1 path4cloud path4cloud   44 Apr 30 01:34 ping.sh
 1782673 drwxr-xr-x. 6 path4cloud path4cloud   51 Apr 29 19:59 project
```
- soft link has a drawback here, if we delete the directory, then soft link will be broken.
```
[path4cloud@rhel-9 ~]$ ls -li
total 16
  166939 -rw-r--r--. 1 path4cloud path4cloud   30 Apr 29 20:25 a
17119681 drwxr-xr-x. 3 path4cloud path4cloud 4096 Apr  5 23:03 ansible-ops
 2190330 -rw-r--r--. 1 path4cloud path4cloud   32 Apr 29 20:33 hostinfo
 2190331 lrwxrwxrwx. 1 path4cloud path4cloud    8 Apr 30 01:40 new_project -> project/
 2190327 -rwxr-xr-x. 1 path4cloud path4cloud   44 Apr 30 01:34 ping.sh
 1782673 drwxr-xr-x. 6 path4cloud path4cloud   51 Apr 29 19:59 project
[path4cloud@rhel-9 ~]$
[path4cloud@rhel-9 ~]$ rm -rf project/
[path4cloud@rhel-9 ~]$ ls -li
total 16
  166939 -rw-r--r--. 1 path4cloud path4cloud   30 Apr 29 20:25 a
17119681 drwxr-xr-x. 3 path4cloud path4cloud 4096 Apr  5 23:03 ansible-ops
 2190330 -rw-r--r--. 1 path4cloud path4cloud   32 Apr 29 20:33 hostinfo
 2190331 lrwxrwxrwx. 1 path4cloud path4cloud    8 Apr 30 01:40 new_project -> project/
 2190327 -rwxr-xr-x. 1 path4cloud path4cloud   44 Apr 30 01:34 ping.sh
[path4cloud@rhel-9 ~]$
[path4cloud@rhel-9 ~]$ cd new_project
-bash: cd: new_project: No such file or directory
```
- you can run `unlink <link_name>` to remove or un link the soft link if not required.