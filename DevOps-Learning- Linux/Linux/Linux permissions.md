Linux permissions control **who can read, write, or execute** a file or directory.


-rwxr-xr-- 1 adi devops 1234 Sep 27 script.sh

### Permission structure

```
-rwxr-xr--
│├──┬──┤
│  │  │
│  │  └── Others
│  └───── Group
└──────── Owner
```



```
u → owner
g → group
o → others
a → all
```

chmod u+x  script.sh   :  Add execute permission to owner.
chmod g+w script.sh  :  Add write permission to group.
chmod o+r script.sh   :   Add read permission to others.


chmod u-x script.sh    : remove execute permission to user
chmod g-w script.sh   : remove write permission to group
chmod o-r script.sh    : remove read permission to others 


Change won:

chwon adi script.sh  : make adi to the script.sh file
chwon adi:devops script.sh   : Makes adi the owner and `devops` the group owner
chown adi: script.sh  :  Change Owner and Automatically Match Their Primary Group

chown -R adi:devops /folder :  **`-R` (Recursive):** Applies the ownership changes to a directory **and everything inside it** (all sub-folders and files).


**Remember:**


chmod → Permission
chown → Owner
chgrp → Group
umask → Default permissions
sudo  → Elevated privileges
