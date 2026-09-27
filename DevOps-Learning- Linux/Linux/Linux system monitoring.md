CPU:

top
htop

**Use:** Check CPU usage, CPU cores, load average, and processes.

Memory :
free -h

**Use:** Check RAM, available memory, used memory, and swap.

Disk Usage:

df -h

df → Filesystem disk usage
du → Directory/file size
lsblk → Disks and partitions

Process:
ps -ef

### Kill a process

```
kill <PID>
```

Force kill:

```
kill -9 <PID>
```



# CMDP Memory Trick

```
C → CPU     → top / lscpu
M → Memory  → free -h
D → Disk    → df -h / du -sh
P → Process → ps -ef / pgrep
```

### ⭐ Most important

```
top              # CPU + Memory + Processes
free -h          # Memory
df -h            # Disk
ps -ef           # Processes
```
