## ACL - The Flexible Permissions
- Login with root, go to `/permission` directory (we use this same directory) where `dev1` or other normal users have full permission for this demo, You can use any location.
```
Create a new dir acl_demo
[root@rhel-9 permission]# mkdir acl_demo
[root@rhel-9 permission]# ll
total 0
drwxr-xr-x. 2 root root 6 Sep  3 05:40 acl_demo

We can permissions are define as per user umask
# umask
0022

Check defualt ACL applied to this directory
[root@rhel-9 permission]# getfacl acl_demo/
# file: acl_demo/
# owner: root
# group: root
user::rwx
group::r-x
other::r-x

Set ACL now for user dev1 as rwx, Notice for others we have r-x only
# setfacl -m "u:dev1:rwx" acl_demo/

Check the Permission now, and Notice, dev1 has flexibla permission now via ACL
[root@rhel-9 permission]# getfacl acl_demo/
# file: acl_demo/
# owner: root
# group: root
user::rwx
user:dev1:rwx
group::r-x
mask::rwx
other::r-x
```
- As per the default permission on this directory, othere who is not owning user and part of owning group, can't write to this directory. But we have set up a ACP for dev1 user so lets verify if dev1 user is able to write here:
```
# su - dev1
$ cd /permission/
$ ll
total 0
drwxrwxr-x+ 2 root root 6 Sep  3 05:40 acl_demo

NOTICE: + has been added at the end of permissions that indicates that ACL has been applied.
Since dev1 has rwx, so he can go inside, read the content and create a new file/dir
$ cd acl_demo/
$ ll
total 0
$ touch dev1
$ ll
total 0
-rw-r--r--. 1 dev1 dev1 0 Sep  3 05:43 dev1
```
- Let's verify with mgr user and find out whether he can write or not
```
# su - mgr
$ cd acl_demo/
$ ll
total 0
-rw-r--r--. 1 dev1 dev1 0 Sep  3 05:43 dev1

$ touch mgr
touch: cannot touch 'mgr': Permission denied

mgr user can go inside, list/read the content but can't write anything
```
We have modified the permission (ACL) at existing directory but if we create a new directory or new file under ACL set directory then even `dev1` user can't have write permission. Lets verify:
```
[root@rhel-9 permission]# cd acl_demo/
# mkdir acl_demo/new_dir
# touch acl_demo/file
# echo hello > acl_demo/file# ll acl_demo/
total 4
-rw-r--r--. 1 dev1 dev1 0 Sep  3 05:43 dev1
-rw-r--r--. 1 root root 6 Sep  3 05:44 file
drwxr-xr-x. 2 root root 6 Sep  3 05:44 new_dir

NOTICE: By defualt, no + has been added to new file/direcotry under ACL set /acl_demo directory

Now, switch to dev1 user:
# su - dev1
$ cd /permission/acl_demo/
$ ll
total 4
-rw-r--r--. 1 dev1 dev1 0 Sep  3 05:43 dev1
-rw-r--r--. 1 root root 6 Sep  3 05:44 file
drwxr-xr-x. 2 root root 6 Sep  3 05:44 new_dir

He still can create the new file/dir under /acl_demo
$ touch dev1new
$ ll
total 4
-rw-r--r--. 1 dev1 dev1 0 Sep  3 05:43 dev1
-rw-r--r--. 1 dev1 dev1 0 Sep  3 05:44 dev1new
-rw-r--r--. 1 root root 6 Sep  3 05:44 file
drwxr-xr-x. 2 root root 6 Sep  3 05:44 new_dir

Now, lets try to create under new_dir or try to modify the conent of file
$ echo something > file
-bash: file: Permission denied

$ cd new_dir/
$ touch new_file
touch: cannot touch 'new_file': Permission denied
```

Now, Lets set the ACL with default (Inheritance) mode:
```
# setfacl -d -m "u:dev1:rwx" acl_demo/
# mkdir acl_demo/post_dir
# touch acl_demo/newfile
# echo newfile > acl_demo/newfile

Switch to dev1 user now,
# su - dev1
$ cd /permission/acl_demo/
$ ll
total 8
-rw-r--r--. 1 dev1 dev1 0 Sep  3 05:43 dev1
-rw-r--r--. 1 dev1 dev1 0 Sep  3 05:44 dev1new
-rw-r--r--. 1 root root 6 Sep  3 05:44 file
drwxr-xr-x. 2 root root 6 Sep  3 05:44 new_dir
-rw-rw-r--+ 1 root root 8 Sep  3 05:46 newfile
drwxrwxr-x+ 2 root root 6 Sep  3 05:46 post_dir


NOTICE: the + has been added now to post_dir and newfile beacuse these files/dir is created post defulat mode ACL is applied.
$ getfacl post_dir/
# file: post_dir/
# owner: root
# group: root
user::rwx
user:dev1:rwx
group::r-x
mask::rwx
other::r-x
default:user::rwx
default:user:dev1:rwx
default:group::r-x
default:mask::rwx
default:other::r-x

And now, dev1 user is able to create a new file or modied the existing file
$ touch post_dir/anyfile
$ echo change something > newfile
$ cat newfile
change something
```

THUMB RULE:
- If you change the permissions of ACL applied direcoty then it will restrict the effective permission:
```
# mkdir new_dir
# setfacl -m "u:dev1:rwx"  new_dir/
# getfacl new_dir/
# file: new_dir/
# owner: root
# group: root
user::rwx
user:dev1:rwx
group::rwx
mask::rwx
other::r-x


Now change the permission for this new directory:
# chmod 755 new_dir/
# getfacl new_dir/
# file: new_dir/
# owner: root
# group: root
user::rwx
user:dev1:rwx                   #effective:r-x
group::rwx                      #effective:r-x
mask::r-x
other::r-x

NOTICE: dev1 has rwx but his effective permission is r-x

And now, it he tries to create something inside this then it will be denied.
# su - dev1
$ cd /permission/new_dir/
$ touch h
touch: cannot touch 'h': Permission denied
```