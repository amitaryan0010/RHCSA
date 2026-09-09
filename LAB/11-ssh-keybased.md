## SSH
- create a new user and then generate the key with default options, it will create the keys with defaault ed25519 algoright in latest Linux systems:
```
$ ssh-keygen
Generating public/private ed25519 key pair.
Enter file in which to save the key (/home/path4cloud/.ssh/id_ed25519):
Enter passphrase for "/home/path4cloud/.ssh/id_ed25519" (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in /home/path4cloud/.ssh/id_ed25519
Your public key has been saved in /home/path4cloud/.ssh/id_ed25519.pub
The key fingerprint is:
SHA256:CrTRHkNeT+egIs+avcnWzlgzH5foI/xgmyibnuUQ9SU path4cloud@rhel-9
The key's randomart image is:
+--[ED25519 256]--+
|      . . o .    |
|     + . + +     |
|    + B E o .    |
|   . O = o       |
|    + + S        |
|     * .   . .   |
|    + +o* o o    |
|    .B.O+O.o     |
|   .=+B.=o+.     |
+----[SHA256]-----+
```
- But we can can specify the type with `-t` flag:
```
$ ssh-keygen -t rsa
Generating public/private rsa key pair.
Enter file in which to save the key (/home/path4cloud/.ssh/id_rsa):
Enter passphrase for "/home/path4cloud/.ssh/id_rsa" (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in /home/path4cloud/.ssh/id_rsa
Your public key has been saved in /home/path4cloud/.ssh/id_rsa.pub
The key fingerprint is:
SHA256:9bQ+netkq6Zl0IPA6QdsDK4ypzq8uDr3RAXOydqq6Zk path4cloud@rhel-9
The key's randomart image is:
+---[RSA 3072]----+
|    . .          |
|   + + = .       |
|    = o O . .    |
|   o o o + = .   |
|  + =   S + =    |
|   B     . o o . |
|. o .       = =  |
|+=+.       o.+ o |
|XE...     .o.o+  |
+----[SHA256]-----+
```
- By default, keys will be saved under ~/.ssh/ directory, but if we want to change the path, then specify wiith `-f` flag and put `-N ''` for non-interactive prompt:
```
$ ssh-keygen -N '' -f .ssh/custom_rsa
Generating public/private ed25519 key pair.
Your identification has been saved in .ssh/custom_rsa
Your public key has been saved in .ssh/custom_rsa.pub
The key fingerprint is:
SHA256:TmyqkFdeEsmip+y9YFSb/8quCzgRMTWaPmw88mt+9uM path4cloud@rhel-9
The key's randomart image is:
+--[ED25519 256]--+
|o.o              |
| = . . .         |
|+   o +          |
|+. o + o         |
|oBo + o S        |
|o*o+ + B         |
|o X . + .        |
| +.Boo..         |
| o+o*BEo.        |
+----[SHA256]-----+
```
- Verify the keys:
```
$ ls -la .ssh/
-rw-------. 1 path4cloud path4cloud  411 Sep  9 04:51 custom_rsa
-rw-r--r--. 1 path4cloud path4cloud   99 Sep  9 04:51 custom_rsa.pub
-rw-------. 1 path4cloud path4cloud  411 Sep  9 04:53 id_ed25519
-rw-r--r--. 1 path4cloud path4cloud   99 Sep  9 04:53 id_ed25519.pub
-rw-------. 1 path4cloud path4cloud 2602 Sep  9 04:50 id_rsa
-rw-r--r--. 1 path4cloud path4cloud  571 Sep  9 04:50 id_rsa.pub
```
- When we connect first time, it will prompt for fingerprint to set the authenticity of target server, and if we are sure, then can pass `yes` and then it will add the fingerprint to `known_hosts`  file under `~/.ssh/`
```
$ ssh docker
The authenticity of host 'docker (192.168.139.9)' can't be established.
ED25519 key fingerprint is SHA256:ndxaZMWD2t9l6QY56d5xRzEEBpnd3rRBCdMBxIbZXlg.
This key is not known by any other names.
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added 'docker' (ED25519) to the list of known hosts.
path4cloud@docker's password:



$ ls -ld ~/.ssh/known_hosts
-rw-r--r--. 1 path4cloud path4cloud 88 Sep  9 04:50 /home/path4cloud/.ssh/known_hosts

$ cat ~/.ssh/known_hosts
docker ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIGhui4E/XB7nVbdhc3aNe8hsdehM7M8RGxwAXxqlob7f
```

- These fingerprint are keys from target nodes under /etc/ssh/ and will be secure if you reinstalling this target nodes, else known_hosts file will be broken and we need to clear those:
```
[path4cloud@rhel-9 ~]$ ssh docker
path4cloud@docker's password:
#########################################
# Welcome to docker (Ubuntu 24.04)    #
#                                       #
#      managed by Path4Cloud.com        #
#                                       #
# Unauthorized access is prohibited     #
#########################################
Last login: Tue Sep  8 20:56:32 2026 from 192.168.139.5
$ sudo -i
root@docker:~ ()$ cd /etc/ssh/

root@docker:/etc/ssh ()$ ll *key*
-rw------- 1 root root  505 Jun  4  2024 ssh_host_ecdsa_key
-rw-r--r-- 1 root root  174 Jun  4  2024 ssh_host_ecdsa_key.pub
-rw------- 1 root root  399 Jun  4  2024 ssh_host_ed25519_key
-rw-r--r-- 1 root root   94 Jun  4  2024 ssh_host_ed25519_key.pub
-rw------- 1 root root 2602 Jun  4  2024 ssh_host_rsa_key
-rw-r--r-- 1 root root  566 Jun  4  2024 ssh_host_rsa_key.pub
root@docker:/etc/ssh ()$
root@docker:/etc/ssh ()$ cat ssh_host_ed25519_key.pub
ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIGhui4E/XB7nVbdhc3aNe8hsdehM7M8RGxwAXxqlob7f root@osboxes
```
- In above, snippet, we have logged in with user `path4cloud` user to target node `docker` and now it will prompt the password directly since we have already added the fingerprint to know_hosts file on `rhel-9` machine under his home directory. Notice the `known_hosts` and `ssh_host_ed25519_key.pub` from `docker` hosts has same keys.
- Lets try to remove these keys from docker and then try to connect from `rhel-9` again. Notice: Either take the backup of these keys file or recreate
```
$ rm -rf *key*
$ ll *key*
ls: cannot access '*key*': No such file or directory

$ ssh-keygen -A
ssh-keygen: generating new host keys: RSA ECDSA ED25519
$ systemctl restart sshd

$ ll *key*
-rw------- 1 root root  505 Sep  8 23:45 ssh_host_ecdsa_key
-rw-r--r-- 1 root root  173 Sep  8 23:45 ssh_host_ecdsa_key.pub
-rw------- 1 root root  399 Sep  8 23:45 ssh_host_ed25519_key
-rw-r--r-- 1 root root   93 Sep  8 23:45 ssh_host_ed25519_key.pub
-rw------- 1 root root 2602 Sep  8 23:45 ssh_host_rsa_key
-rw-r--r-- 1 root root  565 Sep  8 23:45 ssh_host_rsa_key.pub
```
- Because we generated brand-new host keys, our remote server now has a completely new cryptographic `fingerprint`. The next time we try to SSH into this machine from your laptop or local computer, your SSH client will panic and throw a scary WARNING: REMOTE HOST IDENTIFICATION HAS CHANGED! error. This is normal protection against man-in-the-middle attacks.
```
$ ssh docker
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
@    WARNING: REMOTE HOST IDENTIFICATION HAS CHANGED!     @
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
IT IS POSSIBLE THAT SOMEONE IS DOING SOMETHING NASTY!
Someone could be eavesdropping on you right now (man-in-the-middle attack)!
It is also possible that a host key has just been changed.
The fingerprint for the ED25519 key sent by the remote host is
SHA256:K+UNcRW3HftK9xcN5zvqkKuIRmWPd2zn0IWGglqLb1E.
Please contact your system administrator.
Add correct host key in /home/path4cloud/.ssh/known_hosts to get rid of this message.
Offending ECDSA key in /home/path4cloud/.ssh/known_hosts:3
Host key for docker has changed and you have requested strict checking.
Host key verification failed.
```
- To fix it on your local computer (not the server), run:
```
Currently known_hosts file looks like:

$ cat .ssh/known_hosts
docker ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIGhui4E/XB7nVbdhc3aNe8hsdehM7M8RGxwAXxqlob7f
docker ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABgQCl/cL8ZFxuRHkOq8pVEaB2bN4nYHMYURe0BF6jWsBEynhSRQNFIXxBRloB4izyrRZXh/+uHoNCX9FpeW2yKmZYGx//1mtJH8ZTdZ3LqOeRErHrrHJa6LLLG5JpgcEWdbGLY7E14idosklUXmKZY1gyYIhKN71EQtLNTctrEs3D/tcGPEiE+quaguCU1s3bQdPMTSticeZTFpUNXHFWFiOTPPN7VvnoBK9f70Az/SecSIn3ocsN1sgxbuD8PDZOX7gC5rfW5C1Dxq0JClD2adGHPZgvGOOy+9XvE7m0BCNdlojD+suHXN/V/jx+filvLgSzAbUKOfZm77CKP+NzCkGfG/dkEtIizTFEQX9WVVowWCBNHp285AKbBVm8WIt9p+L5ISUsfDObe2OXq9N5GOvwekh5ixXojtlzwL6ohH24EjpyRweOAvwDaF84Xpv9BNQbjDxrkUmmDkyKwoZ5ii8LRSlDlaq2Fv0YY5sAb7e8q5MhdeaIn/NHFELm4IPLW08=
docker ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBO/wMs7ZdDfbHi703S7KILQ4UGxtHq9tiXUV0rutzlV0kNJt0G4MkNXAGvFs4X/MM6TJtmOWsuiw2nTgXYNBC3A=


$ ssh-keygen -R docker
# Host docker found: line 1
# Host docker found: line 2
# Host docker found: line 3
/home/path4cloud/.ssh/known_hosts updated.
Original contents retained as /home/path4cloud/.ssh/known_hosts.old

$ cat .ssh/known_hosts
```
- File, will be empty now. OR we can go inside the `known_hosts` file and manually delete the all lines containing the target host name and keys.
- Try to reconnet now, and notice, it will prompt for new fingerprint.
```
$ ssh docker
The authenticity of host 'docker (192.168.139.9)' can't be established.
ED25519 key fingerprint is SHA256:K+UNcRW3HftK9xcN5zvqkKuIRmWPd2zn0IWGglqLb1E.
This key is not known by any other names.
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added 'docker' (ED25519) to the list of known hosts.
path4cloud@docker's password:
#########################################
# Welcome to docker (Ubuntu 24.04)    #
#                                       #
#      managed by Path4Cloud.com        #
#                                       #
# Unauthorized access is prohibited     #
#########################################
Last login: Tue Sep  8 23:38:29 2026 from 192.168.139.5
$ hostname
docker
```
### Passwordless Authentication (SSH Keys)
- We have public and private keys are created on local server. So now, copy the `public key` to your remote server:
```
$ ssh-copy-id docker
/usr/bin/ssh-copy-id: INFO: Source of key(s) to be installed: "/home/path4cloud/.ssh/id_ed25519.pub"
/usr/bin/ssh-copy-id: INFO: attempting to log in with the new key(s), to filter out any that are already installed
/usr/bin/ssh-copy-id: INFO: 1 key(s) remain to be installed -- if you are prompted now it is to install the new keys
path4cloud@docker's password:

Number of key(s) added: 1

Now try logging into the machine, with: "ssh 'docker'"
and check to make sure that only the key(s) you wanted were added.
```
- Now when we try to connect, it will not prompt the password, it will login directly:
```
$ ssh docker
#########################################
# Welcome to docker (Ubuntu 24.04)    #
#                                       #
#      managed by Path4Cloud.com        #
#                                       #
# Unauthorized access is prohibited     #
#########################################
Last login: Tue Sep  8 23:51:18 2026 from 192.168.139.5
$
```
- To copy the spcific keys, use `-i` flag to specify the path of that file:
```
$ ssh-copy-id -i ~/.ssh/custom_rsa.pub docker
/usr/bin/ssh-copy-id: INFO: Source of key(s) to be installed: "/home/path4cloud/.ssh/custom_rsa.pub"
/usr/bin/ssh-copy-id: INFO: attempting to log in with the new key(s), to filter out any that are already installed
/usr/bin/ssh-copy-id: INFO: 1 key(s) remain to be installed -- if you are prompted now it is to install the new keys

Number of key(s) added: 1

Now try logging into the machine, with: "ssh -i /home/path4cloud/.ssh/custom_rsa 'docker'"
and check to make sure that only the key(s) you wanted were added.

$ ssh -i .ssh/custom_rsa docker
#########################################
# Welcome to docker (Ubuntu 24.04)    #
#                                       #
#      managed by Path4Cloud.com        #
#                                       #
# Unauthorized access is prohibited     #
#########################################
Last login: Tue Sep  8 23:53:49 2026 from 192.168.139.5
$
```
- Let say, we have multiple keys to connect with different-different servers, then we can copy the respective keys to target node. But while connecting, everytime we need to specify the key file path else, it will take the default key file and if that doesn't matches then it will prompt for passwrd.
```
$ ssh-copy-id -i ~/.ssh/custom_rsa.pub centos9
/usr/bin/ssh-copy-id: INFO: Source of key(s) to be installed: "/home/path4cloud/.ssh/custom_rsa.pub"
/usr/bin/ssh-copy-id: INFO: attempting to log in with the new key(s), to filter out any that are already installed
/usr/bin/ssh-copy-id: INFO: 1 key(s) remain to be installed -- if you are prompted now it is to install the new keys
path4cloud@centos9's password:

Number of key(s) added: 1

Now try logging into the machine, with: "ssh -i /home/path4cloud/.ssh/custom_rsa 'centos9'"
and check to make sure that only the key(s) you wanted were added.

[path4cloud@rhel-9 ~]$ ssh centos9
path4cloud@centos9's password:
```
- To fix this issue, we can create a config file and make an entry for dedicated key file path for respective hosts:
```
$ vim .ssh/config

$ cat .ssh/config
Host centos9
        IdentityFile /home/path4cloud/.ssh/custom_rsa
[path4cloud@rhel-9 ~]$ ssh centos9
#########################################
# Welcome to centos9 (centos-osbopxes)  #
#                                       #
#      managed by Path4Cloud.com        #
#                                       #
# Unauthorized access is prohibited     #
#########################################
Last login: Tue Sep  8 19:58:59 2026 from 192.168.139.5
$
```
- If still, we want the ssh client to prompt for password then explicitly we can sepcify the `-o PreferredAuthentications=password` option with ssh:
```
[path4cloud@rhel-9 ~]$ ssh -o PreferredAuthentications=password centos9
path4cloud@centos9's password:
#########################################
# Welcome to centos9 (centos-osbopxes)  #
#                                       #
#      managed by Path4Cloud.com        #
#                                       #
# Unauthorized access is prohibited     #
#########################################
Last login: Tue Sep  8 20:03:37 2026 from 192.168.139.5
[path4cloud@centos9 ~]$
```

## Hardening SSH Security
- By default file at centos9 server:
```
# # grep ^[^#] /etc/ssh/sshd_config
Include /etc/ssh/sshd_config.d/*.conf
PermitRootLogin yes
AuthorizedKeysFile      .ssh/authorized_keys
Subsystem       sftp    /usr/libexec/openssh/sftp-server
```
- lets put the custom config file:
```
[root@centos9 ~]# cat /etc/ssh/sshd_config.d/90-custom.conf
AllowUsers path4cloud
PasswordAuthentication no
PermitRootLogin no

[root@centos9 ~]# systemctl reload sshd
```
- We have allowed only `path4cloud` user and disabled the password authentication for everyone.
- Lets try with different user:
```
[root@rhel-9 ~]# ssh ansibleuser@centos9
ansibleuser@centos9: Permission denied (publickey,gssapi-keyex,gssapi-with-mic)
```
- Lets try with `ansibleuser` only but with password:
```
[root@rhel-9 ~]# ssh path4cloud@centos9
path4cloud@centos9: Permission denied (publickey,gssapi-keyex,gssapi-with-mic).
OR
[path4cloud@rhel-9 ~]$ ssh -o PreferredAuthentications=password centos9
path4cloud@centos9: Permission denied (publickey,gssapi-keyex,gssapi-with-mic)
```
- Now, lets try with keys:
```
[path4cloud@rhel-9 ~]$ ssh centos9
#########################################
# Welcome to centos9 (centos-osbopxes)  #
#                                       #
#      managed by Path4Cloud.com        #
#                                       #
# Unauthorized access is prohibited     #
#########################################
Last login: Tue Sep  8 20:22:25 2026 from 192.168.139.5
[path4cloud@centos9 ~]$
```
- Check the logs at `centos9`
```
[root@centos9 ~]# tail -n 50 /var/log/secure
Sep  8 20:22:00 centos9 sshd-session[4331]: User ansibleuser from 192.168.139.5 not allowed because not listed in AllowUsers
Sep  8 20:22:00 centos9 sshd-session[4331]: Connection closed by invalid user ansibleuser 192.168.139.5 port 42278 [preauth]

(when tried to login with password)
Sep  8 20:24:13 centos9 sshd-session[4387]: Connection closed by authenticating user path4cloud 192.168.139.5 port 37144 [preauth]


Sep  8 20:24:47 centos9 sshd-session[4390]: Accepted publickey for path4cloud from 192.168.139.5 port 43880 ssh2: ED25519 SHA256:TmyqkFdeEsmip+y9YFSb/8quCzgRMTWaPmw88mt+9uM
Sep  8 20:24:47 centos9 systemd[4394]: pam_unix(systemd-user:session): session opened for user path4cloud(uid=1000) by path4cloud(uid=0)
Sep  8 20:24:48 centos9 sshd-session[4390]: pam_unix(sshd:session): session opened for user path4cloud(uid=1000) by path4cloud(uid=0)
Sep  8 20:24:50 centos9 sudo[4438]: path4cloud : TTY=pts/0 ; PWD=/root ; USER=root ; COMMAND=/bin/bash
```


### Troubleshooting Common Errors
- Connection refused
```
[root@centos9 ~]# systemctl status sshd
○ sshd.service - OpenSSH server daemon
     Loaded: loaded (/usr/lib/systemd/system/sshd.service; enabled; preset: enabled)
     Active: inactive (dead) since Tue 2026-09-08 20:37:34 EDT; 40s ago


[path4cloud@rhel-9 ~]$ ssh centos9
ssh: connect to host centos9 port 22: Connection refused
```
- Permission denied
```
[path4cloud@rhel-9 ~]$ ssh centos9
Load key "/home/path4cloud/.ssh/custom_rsa": invalid format
path4cloud@centos9: Permission denied (publickey,gssapi-keyex,gssapi-with-mic).

[path4cloud@rhel-9 ~]$ ssh centos9
path4cloud@centos9: Permission denied (publickey,gssapi-keyex,gssapi-with-mic).
```
- Connection timed out
- No route to host
```
[root@centos9 ~]# firewall-cmd --zone=public --remove-service=ssh
success
[root@centos9 ~]# firewall-cmd --runtime-to-permanent
success

[path4cloud@rhel-9 ~]$ ssh centos9
ssh: connect to host centos9 port 22: No route to host

TO fix it again:
[root@centos9 ~]# firewall-cmd --zone=public --add-service=ssh
success
[root@centos9 ~]# firewall-cmd --runtime-to-permanent
success
```
- Host key verification failed
```
ssh docker
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
@    WARNING: REMOTE HOST IDENTIFICATION HAS CHANGED!     @
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
IT IS POSSIBLE THAT SOMEONE IS DOING SOMETHING NASTY!
Someone could be eavesdropping on you right now (man-in-the-middle attack)!
It is also possible that a host key has just been changed.
The fingerprint for the ED25519 key sent by the remote host is
SHA256:K+UNcRW3HftK9xcN5zvqkKuIRmWPd2zn0IWGglqLb1E.
Please contact your system administrator.
Add correct host key in /home/path4cloud/.ssh/known_hosts to get rid of this message.
Offending ECDSA key in /home/path4cloud/.ssh/known_hosts:3
Host key for docker has changed and you have requested strict checking.
Host key verification failed.
```