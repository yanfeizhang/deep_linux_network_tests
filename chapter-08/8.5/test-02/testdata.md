## 开始前

```sh
# cat /etc/redhat-release
CentOS Linux release 7.6.1810 (Core)
# uname -a
Linux hbhly_SG11_130_50 3.10.0-957.el7.x86_6 ......
```

### client
]# cat /proc/meminfo
MemTotal:        8009284 kB
MemFree:         7243496 kB
MemAvailable:    7165024 kB
Buffers:            3124 kB
Cached:           497084 kB
Slab:              40676 kB

 Active / Total Objects (% used)    : 161525 / 204304 (79.1%)
 Active / Total Slabs (% used)      : 6261 / 6261 (100.0%)
 Active / Total Caches (% used)     : 80 / 107 (74.8%)
 Active / Total Size (% used)       : 29540.82K / 39313.45K (75.1%)
 Minimum / Average / Maximum Object : 0.01K / 0.19K / 8.00K

  OBJS ACTIVE  USE OBJ SIZE  SLABS OBJ/SLAB CACHE SIZE NAME
 68352  47821  69%    0.06K   1068       64      4272K kmalloc-64
 18123  12091  66%    0.19K    863       21      3452K dentry
 15028  15028 100%    0.12K    442       34      1768K kernfs_node_cache
 11220  11220 100%    0.04K    110      102       440K selinux_inode_security
  9412   9005  95%    0.58K    724       13      5792K inode_cache
  9360   8800  94%    0.10K    240       39       960K buffer_head
  7938   2633  33%    0.57K    567       14      4536K radix_tree_node



### server

cat /proc/meminfo
MemTotal:        8010316 kB
MemFree:         6873520 kB
MemAvailable:    6796320 kB
Buffers:            8224 kB
Cached:           514240 kB
Slab:              55660 kB



 Active / Total Objects (% used)    : 166662 / 231635 (72.0%)
 Active / Total Slabs (% used)      : 7086 / 7086 (100.0%)
 Active / Total Caches (% used)     : 84 / 115 (73.0%)
 Active / Total Size (% used)       : 36347.01K / 54863.74K (66.2%)
 Minimum / Average / Maximum Object : 0.01K / 0.24K / 8.00K

  OBJS ACTIVE  USE OBJ SIZE  SLABS OBJ/SLAB CACHE SIZE NAME
 25536  13881  54%    0.06K    399       64      1596K kmalloc-64
 22197  12812  57%    0.19K   1057       21      4228K dentry
 17820  17742  99%    0.11K    495       36      1980K kernfs_node_cache
 15249   7864  51%    0.10K    391       39      1564K buffer_head
 14644   4489  30%    0.57K    523       28      8368K radix_tree_node
 11904   7668  64%    0.03K     93      128       372K kmalloc-32
 11304  11067  97%    0.21K    628       18      2512K vm_area_struct
 10914  10914 100%    0.04K    107      102       428K selinux_inode_security


## 开始后
 ss -ant | grep ESTAB | wc -l
1000020


### client
 Active / Total Objects (% used)    : 5927503 / 5941159 (99.8%)
 Active / Total Slabs (% used)      : 323691 / 323691 (100.0%)
 Active / Total Caches (% used)     : 80 / 107 (74.8%)
 Active / Total Size (% used)       : 3383480.85K / 3390012.99K (99.8%)
 Minimum / Average / Maximum Object : 0.01K / 0.57K / 8.00K

  OBJS ACTIVE  USE OBJ SIZE  SLABS OBJ/SLAB CACHE SIZE NAME
1357062 1357062 100%    0.19K  64622       21    258488K dentry
1112064 1110997  99%    0.06K  17376       64     69504K kmalloc-64
1003456 1003202  99%    0.25K  62716       16    250864K kmalloc-256
1000152 1000152 100%    0.62K  83346       12    666768K sock_inode_cache
1000032 1000032 100%    1.94K  62502       16   2000064K TCP
343836 343836 100%    0.64K  28653       12    229224K proc_inode_cache


# cat /proc/meminfo
MemTotal:        8009284 kB
MemFree:         3279816 kB
MemAvailable:    4318676 kB
Buffers:            7172 kB
Cached:           538996 kB
Slab:            3526808 kB

### server

 cat /proc/meminfo
MemTotal:        8010316 kB
MemFree:         3297808 kB
MemAvailable:    4031784 kB
Buffers:           12464 kB
Cached:           603056 kB
Slab:            3199696 kB

 Active / Total Objects (% used)    : 5205511 / 5236267 (99.4%)
 Active / Total Slabs (% used)      : 235674 / 235674 (100.0%)
 Active / Total Caches (% used)     : 84 / 115 (73.0%)
 Active / Total Size (% used)       : 3106322.83K / 3118520.85K (99.6%)
 Minimum / Average / Maximum Object : 0.01K / 0.59K / 8.00K

  OBJS ACTIVE  USE OBJ SIZE  SLABS OBJ/SLAB CACHE SIZE NAME
1020160 1019683  99%    0.06K  15940       64     63760K kmalloc-64
1015098 1015098 100%    0.19K  48338       21    193352K dentry
1006160 1006002  99%    0.25K  62885       16    251540K kmalloc-256
1000325 1000325  99%    0.62K  40013       25    640208K sock_inode_cache
1000192 1000192 100%    1.94K  62512       16   2000384K TCP
 22950  22788  99%    0.21K   1275       18      5100K vm_area_struct



