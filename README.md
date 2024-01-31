# RHEL Study Guide: Manage Local Users and Groups

[RHEL Study Guide - Table of Contents](https://github.com/pslucas0212/RHEL-Study-Guide)  

A user id is kept in the /etc/passwd file.  The passwd file has the user ID, password, UID, GID, Group Name, User home directory and default shell
```
# cat /etc/passwd
....
student:x:1000:1000:Student User:/home/student:/bin/bash
```
 
Group names and IDs are stored in the /etc/group file.  The group file has group name, x, GID, x
```
# cat /etc/group
...
consultants:x:35000:
```

Check the groups the user belongs to...
```
# groups consultant1
consultant1 : consultant1 consultants
# id consultant1
uid=1002(consultant1) gid=1002(consultant1) groups=1002(consultant1),35000(consultants)
```

User passwords are stored in the /etc/shadow file.  The password file has the username, encrypted password, Days since Jan 1, 1970 that password was last changed, the minimum number of days required between password changes i, The maximum number of days the password is valid, number of days warning password expiration  

This what the /etc/shadow looks like when users are first created, but no password is set.

```
# cat /etc/shadow
...
consultant1:!!:19727:0:30:7:::
consultant2:!!:19727:0:30:7:::
consultant3:!!:19727:0:30:7:::
```

User /etc/login.defs to change maximum number of days a password can be used
```
PASS_MAX_DAYS 30
```

Create a new group with groupadd -g GID groupName  use -g to specify a GID
```
# groupadd -g 35000 consultants
```

Make a group part of sudoers by creating a group specific sudoers file under /etc/sudoers.d/<filename>.  In this example we call the file consultants
```
%consultants ALL=(ALL) ALL
```
In the above line:
- %consultants – the group named "consultants" (% prefix)
- ALL= – on all hosts (if you distribute the same sudoers file to many computers)
- (ALL) – as any target user
- ALL – can run any command

Create new users and assigned them to a "custom" group
```
# useradd -G consultants consultant1
```

Change the new users password
```
# passwd consultant1
```

Here is what the /etc/shadow file looks like after setting the inital password.
```
# cat /etc/shadow
consultant1:$6$LlFbkEt1rCqdoCiS$OuE...lulVZ1:19727:0:30:7:::
consultant2:$6$RbMgZTSPsdQsqGZk$EcW...860V6/:19727:0:30:7:::
consultant3:$6$6Br9Jv7YLPSkZ3je$4I6...9My9W.:19727:0:30:7:::
```

Use the 'chage' command to view and change user password expiry information  


| chage -switch username                        |
|-----------------------------------------------|
  
| Switch  | Result |
|---------|--------|
| -E      | Set password expiration date |
| -M      | Set maxium dates between changing passwords |
| -d      | Set date of last password change|
| -l      | List account password aging |

Set password expiration date with chage -E.  In the example expire in 90 days from today.
```
# date -d "+90 days" +%F
2024-04-03
# chage -E 2024-04-03 consultant1
```
Here is what the /etc/shadow file looks like after changing the password eppiration date
```
# cat /etc/shadow
consultant1:$6$LlFbkEt1rCqdoCiS$OuE...lulVZ1:19727:0:30:7::19817:
consultant2:$6$RbMgZTSPsdQsqGZk$EcW...860V6/:19727:0:30:7::19817:
consultant3:$6$6Br9Jv7YLPSkZ3je$4I6...9My9W.:19727:0:30:7::19817:
```

Change a users password to expired every 15 days
```
# chage -M 15 consultant2
```

Here is what the /etc/shadow file looks like after changing how often the password expires
```
# cat /etc/shadow
consultant1:$6$LlFbkEt1rCqdoCiS$OuE...lulVZ1:19727:0:30:7::19817:
consultant2:$6$RbMgZTSPsdQsqGZk$EcW...860V6/:19727:0:15:7::19817:
consultant3:$6$6Br9Jv7YLPSkZ3je$4I6...9My9W.:19727:0:30:7::19817:
```

Make users change password on first login
```
# chage -d 0 consultant1
```
Here is what the /etc/shadow file looks like after making the user change their password on next login
```
# cat /etc/shadow
consultant1:$6$LlFbkEt1rCqdoCiS$OuE...lulVZ1:0:0:30:7::19817:
consultant2:$6$RbMgZTSPsdQsqGZk$EcW...860V6/:0:0:30:7::19817:
consultant3:$6$6Br9Jv7YLPSkZ3je$4I6...9My9W.:0:0:30:7::19817:
```

