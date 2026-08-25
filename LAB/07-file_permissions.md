## Regular Permissions
```
Add three users for this demo:
# useradd dev1
# useradd tester1
# useradd mgr

Create one directory under / and give full permission so that each user can access it:
# mkdir /permission
# chmod 777 /permission/

# Switch to dev1 user
# su - dev1
$ cd /permission/
$ touch dev-file
$ mkdir dev-dir
$ ll
total 0
drwxr-xr-x. 2 dev1 dev1 6 Aug 25 12:02 dev-dir
-rw-r--r--. 1 dev1 dev1 0 Aug 25 12:02 dev-file

Now, switch to tester1, go to common directory and create the file and dir then repeat the samething for mgr user. Once done, it will look like:
$ ll
total 0
drwxr-xr-x. 2 dev1    dev1    6 Aug 25 12:02 dev-dir
-rw-r--r--. 1 dev1    dev1    0 Aug 25 12:02 dev-file
drwxr-xr-x. 2 mgr     mgr     6 Aug 25 12:05 mgr-dir
-rw-r--r--. 1 mgr     mgr     0 Aug 25 12:05 mgr-file
drwxr-xr-x. 2 tester1 tester1 6 Aug 25 12:04 tester-dir
-rw-r--r--. 1 tester1 tester1 0 Aug 25 12:04 tester-file

Switch back to root user, create 2 groups and add users to those group as below:
# groupadd pool1
# groupadd pool2
# usermod -aG pool1 dev1
# usermod -aG pool2 tester1
# usermod -aG pool1,pool2 mgr

# id dev1
uid=1005(dev1) gid=1006(dev1) groups=1006(dev1),1009(pool1)
# id tester1
uid=1006(tester1) gid=1007(tester1) groups=1007(tester1),1010(pool2)
# id mgr
uid=1007(mgr) gid=1008(mgr) groups=1008(mgr),1009(pool1),1010(pool2)

Now, set the group owning for dev-dir to pool2 and tester-dir to pool1 group:
# chown :pool2 dev-dir/
# chgrp pool1 tester-dir/

# ll
total 0
drwxr-xr-x. 2 dev1    pool2   6 Aug 25 12:02 dev-dir
-rw-r--r--. 1 dev1    dev1    0 Aug 25 12:02 dev-file
drwxr-xr-x. 2 mgr     mgr     6 Aug 25 12:05 mgr-dir
-rw-r--r--. 1 mgr     mgr     0 Aug 25 12:05 mgr-file
drwxr-xr-x. 2 tester1 pool1   6 Aug 25 12:04 tester-dir
-rw-r--r--. 1 tester1 tester1 0 Aug 25 12:04 tester-file

Set the only execute permission to mgr-dir for others:
# chmod 751 /permission/mgr-dir/
drwxr-x--x. 2 mgr     mgr      6 Aug 25 12:05 mgr-dir
```
Lets verify the permissions for dev1 user:
```
switch to dev1 and go to common directory:
$ cd /permission/
$ ll
total 0
drwxr-xr-x. 2 dev1    pool2   6 Aug 25 12:02 dev-dir
-rw-r--r--. 1 dev1    dev1    0 Aug 25 12:02 dev-file
drwxr-x--x. 2 mgr     mgr      6 Aug 25 12:05 mgr-dir
-rw-r--r--. 1 mgr     mgr     0 Aug 25 12:05 mgr-file
drwxr-xr-x. 2 tester1 pool1   6 Aug 25 12:04 tester-dir
-rw-r--r--. 1 tester1 tester1 0 Aug 25 12:04 tester-file

Try to go inside tester-dir and create a file (It will be denied since dev1 user is part of pool1 group but that group has no write permission)
$ cd tester-dir/
$ touch dev-file
touch: cannot touch 'dev-file': Permission denied

Same denial with file as well:
$ echo "I am dev user" > tester-file
-bash: tester-file: Permission denied

dev1 user can write anyting to those files, which are owned by dev1
$ echo "I am dev user" > dev-file
$ cat dev-file
I am dev user

Try to go inside the mgr-dir and list the file (it will be denied again since no read permission for others on that directory)
$ cd mgr-dir/
$ ll
ls: cannot open directory '.': Permission denied
```
Lets verify the permissions for tester1 user:
```
$ cd /permission/
$ ll
total 4
drwxr-xr-x. 2 dev1    pool2    6 Aug 25 12:02 dev-dir
-rw-r--r--. 1 dev1    dev1    14 Aug 25 12:13 dev-file
drwxr-x--x. 2 mgr     mgr      6 Aug 25 12:05 mgr-dir
-rw-r--r--. 1 mgr     mgr      0 Aug 25 12:05 mgr-file
drwxr-xr-x. 2 tester1 pool1    6 Aug 25 12:04 tester-dir
-rw-r--r--. 1 tester1 tester1  0 Aug 25 12:04 tester-file

Try to go inside dev-dir and create a file (It will be denied since tester1 user is part of pool2 group but that group has no write permission)
$ cd dev-dir/
$ touch tester-file
touch: cannot touch 'tester-file': Permission denied

Same denial with file as well:
$ echo "I am tester user" >> dev-file
-bash: dev-file: Permission denied

tester1 user can write anyting to those files, which are owned by tester1
$ echo "I am tester user" >> tester-file
$ cat tester-file
I am tester user

Try to go inside the mgr-dir and list the file (it will be denied again since no read permission for others on that directory)
$ cd mgr-dir/
$ ll
ls: cannot open directory '.': Permission denied
```
Lets verify the permissions for mgr user:
```
$ cd /permission/
$ ll
total 8
drwxr-xr-x. 2 dev1    pool2    6 Aug 25 12:02 dev-dir
-rw-r--r--. 1 dev1    dev1    14 Aug 25 12:13 dev-file
drwxr-x--x. 2 mgr     mgr      6 Aug 25 12:05 mgr-dir
-rw-r--r--. 1 mgr     mgr      0 Aug 25 12:05 mgr-file
drwxr-xr-x. 2 tester1 pool1    6 Aug 25 12:04 tester-dir
-rw-r--r--. 1 tester1 tester1 17 Aug 25 12:25 tester-file

mgr user is part of both groups pool1 and pool2 so he can go inside dev-dir and tester-dir and list the content since pool1 and pool2 group has executer permission on those directories:
$ cd dev-dir/
[mgr@rhel-9 dev-dir]$ ll
total 0

$ cd tester-dir/
[mgr@rhel-9 tester-dir]$ ll
total 0
[mgr@rhel-9 tester-dir]$ touch mgr-file
touch: cannot touch 'mgr-file': Permission denied

But can't create a file since no write permission, but if we allow write permission for groups then mgr can write in files:

Lets set the owning group for dev-file to pool2 and tester-file to pool1 and try to write the content:
# chgrp pool2 dev-file
# chgrp pool1 tester-file

And give the write permission to group:
# chmod 664 dev-file
# chmod 664 tester-file

# ll
total 8
drwxr-xr-x. 2 dev1    pool2  6 Aug 25 12:02 dev-dir
-rw-rw-r--. 1 dev1    pool2 14 Aug 25 12:13 dev-file
drwxr-x--x. 2 mgr     mgr    6 Aug 25 12:05 mgr-dir
-rw-r--r--. 1 mgr     mgr    0 Aug 25 12:05 mgr-file
drwxr-xr-x. 2 tester1 pool1  6 Aug 25 12:04 tester-dir
-rw-rw-r--. 1 tester1 pool1 17 Aug 25 12:25 tester-file


Now, try to write the content:
[mgr@rhel-9 permission]$ echo "I am mgr user" >> dev-file
[mgr@rhel-9 permission]$
[mgr@rhel-9 permission]$ echo "I am mgr user" >> tester-file
[mgr@rhel-9 permission]$
[mgr@rhel-9 permission]$ cat dev-file
I am dev user
I am mgr user
[mgr@rhel-9 permission]$ cat tester-file
I am tester user
I am mgr user
```
```
try to write to tester-file with dev1 user (it will be allowed since tester-file owning group is pool1 which has write permission and dev1 is part of that group)

[dev1@rhel-9 permission]$ id
uid=1005(dev1) gid=1006(dev1) groups=1006(dev1),1009(pool1)
[dev1@rhel-9 permission]$ echo "I am dev user" >> tester-file
[dev1@rhel-9 permission]$ cat tester-file
I am tester user
I am mgr user
I am dev user
```
```
try to write to dev-file with tester1 user (it will be allowed since dev-file owning group is pool2 which has write permission and tester1 is part of that group)

[tester1@rhel-9 permission]$ id
uid=1006(tester1) gid=1007(tester1) groups=1007(tester1),1010(pool2)
[tester1@rhel-9 permission]$ echo "I am tester1 user" >> dev-file
[tester1@rhel-9 permission]$ cat dev-file
I am dev user
I am mgr user
I am tester1 user
```

## Other ways
```
# mkdir other-dir
# ll
total 0
drwxr-xr-x. 2 root root 6 Aug 25 12:56 other-dir

# chmod 750 other-dir/
# ll
total 0
drwxr-x---. 2 root root 6 Aug 25 12:56 other-dir

# chmod g=r other-dir/
# ll
total 0
drwxr-----. 2 root root 6 Aug 25 12:56 other-dir

# chmod o+x other-dir/
# ll
total 0
drwxr----x. 2 root root 6 Aug 25 12:56 other-dir

# chmod u=rwx,g=rx,o= other-dir/
# ll
total 0
drwxr-x---. 2 root root 6 Aug 25 12:56 other-dir

Do it in recursive manner, go inside the other-dir and create 1 dir and 1 file:
[root@rhel-9 other-dir]# ll
total 0
drwxr-xr-x. 2 root root 6 Aug 25 13:05 dir1
-rw-r--r--. 1 root root 0 Aug 25 13:05 file1

create one more file and set the x permssion:
# touch file2
# chmod a+x file2
# ll
total 0
drwxr-xr-x. 2 root root 6 Aug 25 13:05 dir1
-rw-r--r--. 1 root root 0 Aug 25 13:05 file1
-rwxr-xr-x. 1 root root 0 Aug 25 13:05 file2

Now, come out and set the permission 750 in recursive manner:
# chmod -R 750 other-dir/
# ll other-dir/
total 0
drwxr-x---. 2 root root 6 Aug 25 13:05 dir1
-rwxr-x---. 1 root root 0 Aug 25 13:05 file1
-rwxr-x---. 1 root root 0 Aug 25 13:05 file2

Now, try with X (Capital X), create another file and remove this x permisison 
# touch other-dir/file3
# chmod a-x other-dir/file3
# ll other-dir/
total 0
drwxr-x---. 2 root root 6 Aug 25 13:05 dir1
-rwxr-x---. 1 root root 0 Aug 25 13:05 file1
-rwxr-x---. 1 root root 0 Aug 25 13:05 file2
-rw-r--r--. 1 root root 0 Aug 25 13:09 file3


# chmod -R a=rwX other-dir/
# ll other-dir/
total 0
drwxrwxrwx. 2 root root 6 Aug 25 13:05 dir1
-rwxrwxrwx. 1 root root 0 Aug 25 13:05 file1
-rwxrwxrwx. 1 root root 0 Aug 25 13:05 file2
-rw-rw-rw-. 1 root root 0 Aug 25 13:09 file3

(This time, we can see no x permission will be granted with capital X on file3 which was regular file, no x permission)
```

## MOST IMPORTANT RULE
```
Lets create a data directory and dev1 user and set the group owner to pool1 which is also a additional group for dev1 and then set the permission 070 to data directory. And then try to access the data directory.

$ mkdir data
$ ll
total 0
drwxr-xr-x. 2 dev1 dev1 6 Aug 25 13:25 data

$ chgrp pool1 data/
$ chmod 070 data/
$ ll
total 0
d---rwx---. 2 dev1 pool1 6 Aug 25 13:25 data

$ ls data/
ls: cannot open directory 'data/': Permission denied

$ cd data/
-bash: cd: data/: Permission denied

$ id
uid=1005(dev1) gid=1006(dev1) groups=1006(dev1),1009(pool1)

Here, dev1 user is part of pool1 group which has full permission bbut since he is the owner of that directory so those permissions will be applicable not the group permission.

Same rule for file as well.
```


## SUID

To simulate SUID (Set User ID) on an executable, you will create a C Code, but the process runs with the privileges of the file owner, not the user who executed it. When SUID is set on an executable file, any user running it executes the program with the permissions of the file's owner.
```
[root@rhel-9 bit]# cat show_id.c
#include <stdio.h>
#include <unistd.h>
#include <sys/types.h>

int main() {
    printf("Real UID: %d\n", getuid());
    printf("Effective UID (Running as): %d\n", geteuid());
    return 0;
}

(Complie the code with gcc, if not installed then install it as "yum install gcc")
[root@rhel-9 bit]# gcc /opt/bit/show_id.c -o /opt/bit/show_id

(change the ownership and set the suid)
[root@rhel-9 ~]# chown root:root /opt/bit/show_id
[root@rhel-9 ~]# chmod 4755 /opt/bit/show_id

(now run it with normal user)
[root@rhel-9 ~]# ls -l /opt/bit/show_id
-rwsr-xr-x. 1 root root 17552 Jul 13 19:57 /opt/bit/show_id

(switch to different user and run the binary)
[path4cloud@rhel-9 ~]$ /opt/bit/show_id
Real UID: 1000
Effective UID (Running as): 0
```

## SGID

To simulate SGID (Set Group ID) on an executable, you will follow a similar process to SUID, but the process will temporarily inherit the file's group privileges instead of the owner's privileges.When SGID is set on an executable file, any user running it executes the program with the permissions of the file's group owner.
```
[root@rhel-9 ~]# cat /opt/bit/show_gid.c
#include <stdio.h>
#include <unistd.h>
#include <sys/types.h>

int main() {
    printf("Real GID: %d\n", getgid());
    printf("Effective GID (Running as group): %d\n", getegid());
    return 0;
}

(complie the program with gcc)
[root@rhel-9 ~]# gcc /opt/bit/show_gid.c -o /opt/bit/show_gid

(Change the ownership and apply th SGID)
[root@rhel-9 ~]# chown root:shared /opt/bit/show_gid
[root@rhel-9 ~]# chmod 2755 /opt/bit/show_gid

[root@rhel-9 ~]# ls -l /opt/bit/show_gid
-rwxr-sr-x. 1 root shared 15928 Jul 13 20:20 /opt/bit/show_gid

(now run it with normal user and check the group id for the user and effective group id which is same as group of binary)
[root@rhel-9 bit]# su - path4cloud
Last login: Thu Jul 16 12:41:37 IST 2026 on pts/1
[path4cloud@rhel-9 ~]$ /opt/bit/show_gid
Real GID: 1000
Effective GID (Running as group): 1003

[path4cloud@rhel-9 ~]$ id
uid=1000(path4cloud) gid=1000(path4cloud) groups=1000(path4cloud),10(wheel) context=unconfined_u:unconfined_r:unconfined_t:s0-s0:c0.c1023

[path4cloud@rhel-9 ~]$ cat /etc/group | grep -i 1003
shared:x:1003:ansibleuser
```

## Sticky bit

When applied to a directory, it prevents users from deleting or renaming files inside it unless they are the explicit owner of that file or the root user.

```
(switch to user1, here it is path4cloud and try to create a one file under /tmp)
[root@rhel-9 bit]# su - path4cloud
Last login: Thu Jul 16 12:44:05 IST 2026 on pts/1

[path4cloud@rhel-9 ~]$ touch /tmp/path4cloud_file.txt
[path4cloud@rhel-9 ~]$ ll /tmp/path4cloud_file.txt
-rw-r--r--. 1 path4cloud path4cloud 0 Jul 16 12:51 /tmp/path4cloud_file.txt

(now, switch to user2, here it is ansibleuser and try to create another file under /tmp)
[root@rhel-9 bit]# su - ansibleuser
Last login: Thu Jul 16 12:52:04 IST 2026 on pts/1

[ansibleuser@rhel-9 ~]$ touch /tmp/ansibleuser_file.txt
[ansibleuser@rhel-9 ~]$ ll /tmp/ansibleuser_file.txt
-rw-r--r--. 1 ansibleuser ansibleuser 0 Jul 16 12:52 /tmp/ansibleuser_file.txt

(now try to delete the ansibleuser file with path4cloud user, it will not allowed)
[path4cloud@rhel-9 ~]$ rm /tmp/ansibleuser_file.txt
rm: remove write-protected regular empty file '/tmp/ansibleuser_file.txt'? y
rm: cannot remove '/tmp/ansibleuser_file.txt': Operation not permitted

(now try to delete the path4cloud file with ansibleuser user, it will not allowed)
[ansibleuser@rhel-9 ~]$ rm /tmp/path4cloud_file.txt
rm: remove write-protected regular empty file '/tmp/path4cloud_file.txt'? y
rm: cannot remove '/tmp/path4cloud_file.txt': Operation not permitted

(now try to delete the any file with root, it will be allowed)
[root@rhel-9 bit]# rm /tmp/path4cloud_file.txt
rm: remove regular empty file '/tmp/path4cloud_file.txt'? y

[root@rhel-9 ~]# ll /tmp/path4cloud_file.txt
ls: cannot access '/tmp/path4cloud_file.txt': No such file or directory
```

## File Color

We noticed, when the SUID or SGID is applied to a file we have red and yellow color highlighted that file name.

When SUID is applied, the file will be highlighted with red color.
When SGID is applied, the file will be highlighted with yellor color.
When Sticky bit is applied, no color but in permission we can see t/T at the end.

![alt text](./lab_images/suid.png)

