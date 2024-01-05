# RHEL Study Guide: Manage Local Users and Groups

[RHEL Study Guide - Table of Contents](https://github.com/pslucas0212/RHEL-Study-Guide)  

A user id is kept in the /etc/passwd file.  The file has the user ID, password, UID, GID, Group Name, User home directory and default shell
```
student:x:1000:1000:Student User:/home/student:/bin/bash
```
 
Group names and IDs are stored in the /etc/group file.  The has group name, x, GID, x
```
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

Create new users and assigned them to a "custom" group
```
# useradd -G consultants consultant1
```

Change the new users password
```
# passwd consultant1
```


| chage -switch username                        |
|-----------------------------------------------|
  
| Switch  | Result |
|---------|--------|
| -E      | Set password expiration date |
| -M      | Set maxium dates between changing passwords |
| -d      | xx|
| -l      | List account password aging |

Set password expiration date with chage -E.  In the example expire in 90 days from today.
```
# date -d "+90 days" +%F
2024-04-03
# chage -E 2024-04-03 consultant1
```


Change a users password to expired every 15 days
```
# chage -M 15 consultant2
```

Make users change password on first login
```
# chage -d 0 consultant1
```


