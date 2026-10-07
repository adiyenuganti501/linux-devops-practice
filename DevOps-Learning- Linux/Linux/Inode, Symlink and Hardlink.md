A file has **metadata** — information about the file itself.

Example:
Person → Name, Age, Job, Aadhaar, etc.
File   → Name, Size, Permissions, Owner, Inode, etc.

![[Pasted image 20261007124512.png]]
ls -li -> list Inode number
stat filename  -> can see the file metadata

# Symlink

**Symlink (Symbolic Link)** is like a **shortcut** to a file or folder.

### Important Points

- Symlink points to the **original file/folder**.
- The **original file and symlink have different Inode numbers**.
- If the **original file is deleted**, the symlink becomes a **broken link**.

/root/devops/linux/symlink/symlink.txt  (file Original location  )

ln -s  /root/devops/linux/symlink/symlink.txt sl.txt   (Created Symlink)

![[Pasted image 20261007125656.png]]

now we can access the content like below
cat sl.txt


Backword compatability

RHEL-8= dnf  (Changed package installer from yum to dnf in RHEL-8)
RHEL-7 = yum
comanyes will affect due to this change.

here yum is symlink/softlink to dnf
![[Pasted image 20261007130349.png]]
this we we can achieve update from yum to dnf


Hardlink
A **Hard Link is not a shortcut**.

It is another name/reference to the **same file data**.

### Important Points

- Hard link and original file have the **same Inode number**.
- Both point to the **same data**.
- If the original file is deleted, the hard link **still works**.
- The data is available through the hard link.
- Hard links are useful when we need another reference to the same file data.
- applicable for only files not folders

/root/aws/ec2/sg.txt

ln /root/aws/ec2/sg.txt sgroup.txt




