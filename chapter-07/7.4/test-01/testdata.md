
# 1.start

## 客户端
### slabtop
```
 Active / Total Objects (% used)    : 148424 / 195797 (75.8%)
 Active / Total Slabs (% used)      : 6123 / 6123 (100.0%)
 Active / Total Caches (% used)     : 80 / 107 (74.8%)
 Active / Total Size (% used)       : 27136.95K / 38725.59K (70.1%)
 Minimum / Average / Maximum Object : 0.01K / 0.20K / 8.00K

  OBJS ACTIVE  USE OBJ SIZE  SLABS OBJ/SLAB CACHE SIZE NAME
 62976  43709  69%    0.06K    984       64      3936K kmalloc-64
 17976  11171  62%    0.19K    856       21      3424K dentry
 15028  15028 100%    0.12K    442       34      1768K kernfs_node_cache
 11220  11220 100%    0.04K    110      102       440K selinux_inode_security
  9412   9008  95%    0.58K    724       13      5792K inode_cache
```

### meminfo
```  
# cat /proc/meminfo
MemTotal:        8009284 kB
MemFree:         7287272 kB
MemAvailable:    7195948 kB
Buffers:            5536 kB
Cached:           477360 kB

Slab:              39848 kB
MemFree+Buffers+Cached = 
```
  
## 服务器

### slabtop
```
 Active / Total Objects (% used)    : 164214 / 224958 (73.0%)
 Active / Total Slabs (% used)      : 6745 / 6745 (100.0%)
 Active / Total Caches (% used)     : 84 / 115 (73.0%)
 Active / Total Size (% used)       : 35107.43K / 52638.84K (66.7%)
 Minimum / Average / Maximum Object : 0.01K / 0.23K / 8.00K

  OBJS ACTIVE  USE OBJ SIZE  SLABS OBJ/SLAB CACHE SIZE NAME
 26368  13080  49%    0.06K    412       64      1648K kmalloc-64
 21399  12225  57%    0.19K   1019       21      4076K dentry
 17820  17742  99%    0.11K    495       36      1980K kernfs_node_cache
 14420   3932  27%    0.57K    515       28      8240K radix_tree_node
 13962   7180  51%    0.10K    358       39      1432K buffer_head
 10914  10914 100%    0.04K    107      102       428K selinux_inode_security
```

### meminfo
```
cat /proc/meminfo
MemTotal:        8010316 kB
MemFree:         6883556 kB
MemAvailable:    6802120 kB
Buffers:            6636 kB
Cached:           508704 kB
Slab:              53512 kB
```

# 2. ESTABLISH

## 客户端

### slabtop
```
 Active / Total Objects (% used)    : 474310 / 491787 (96.4%)
 Active / Total Slabs (% used)      : 21230 / 21230 (100.0%)
 Active / Total Caches (% used)     : 80 / 107 (74.8%)
 Active / Total Size (% used)       : 192932.39K / 200578.72K (96.2%)
 Minimum / Average / Maximum Object : 0.01K / 0.41K / 8.00K

  OBJS ACTIVE  USE OBJ SIZE  SLABS OBJ/SLAB CACHE SIZE NAME
144448 144448 100%    0.06K   2257       64      9028K kmalloc-64
 73353  73353 100%    0.19K   3493       21     13972K dentry
 52208  52192  99%    0.25K   3263       16     13052K kmalloc-256
 50148  50148 100%    0.62K   4179       12     33432K sock_inode_cache
 50032  50032 100%    1.94K   3127       16    100064K TCP
 15028  15028 100%    0.12K    442       34      1768K kernfs_node_cache

```

### meminfo
```
cat /proc/meminfo
MemTotal:        8009284 kB
MemFree:         7082428 kB
MemAvailable:    7029324 kB
Buffers:            9284 kB
Cached:           499288 kB
Slab:             206896 kB
```

### 客户端开销
Slab前后相减 = (206896-39848)/50000=3.34


## 服务器

### slabtop
```
 Active / Total Objects (% used)    : 424662 / 451869 (94.0%)
 Active / Total Slabs (% used)      : 17423 / 17423 (100.0%)
 Active / Total Caches (% used)     : 84 / 115 (73.0%)
 Active / Total Size (% used)       : 191325.03K / 202693.96K (94.4%)
 Minimum / Average / Maximum Object : 0.01K / 0.45K / 8.00K

  OBJS ACTIVE  USE OBJ SIZE  SLABS OBJ/SLAB CACHE SIZE NAME
 63936  63936 100%    0.06K    999       64      3996K kmalloc-64
 62517  62517 100%    0.19K   2977       21     11908K dentry
 53088  52913  99%    0.25K   3318       16     13272K kmalloc-256
 50250  50250 100%    0.62K   2010       25     32160K sock_inode_cache
 50240  50240 100%    1.94K   3140       16    100480K TCP
 17820  17742  99%    0.11K    495       36      1980K kernfs_node_cache
```

### meminfo
```
# cat /proc/meminfo
MemTotal:        8010316 kB
MemFree:         6714896 kB
MemAvailable:    6655600 kB
Buffers:            7444 kB
Cached:           512912 kB
Slab:             206032 kB
```

### 服务器计算

SLAB开销：(206032-53512)/50000=3.05 

MemFree+Buffers+Cached


# 3. FIN_WAIT2

```
# netstat -antp | grep FIN_WAIT2 | wc -l
50000
```

```
Active / Total Objects (% used)    : 264949 / 336095 (78.8%)
 Active / Total Slabs (% used)      : 10928 / 10928 (100.0%)
 Active / Total Caches (% used)     : 80 / 107 (74.8%)
 Active / Total Size (% used)       : 46827.12K / 58782.72K (79.7%)
 Minimum / Average / Maximum Object : 0.01K / 0.17K / 8.00K

  OBJS ACTIVE  USE OBJ SIZE  SLABS OBJ/SLAB CACHE SIZE NAME
144640  95285  65%    0.06K   2260       64      9040K kmalloc-64
 50032  50032 100%    0.25K   3127       16     12508K tw_sock_TCP
 21210  14414  67%    0.19K   1010       21      4040K dentry
 15028  15028 100%    0.12K    442       34      1768K kernfs_node_cache
 11220  11220 100%    0.04K    110      102       440K selinux_inode_security
 10764  10764 100%    0.10K    276       39      1104K buffer_head

```

```
cat /proc/meminfo
MemTotal:        8009284 kB
MemFree:         7213176 kB
MemAvailable:    7148996 kB
Buffers:            9716 kB
Cached:           525932 kB
Slab:              59684 kB
```

SLAB开销：(59684-39848)/50000=0.396

# 4. CLOSE_WAIT

```
$ netstat -ant|grep CLOSE_WAIT | wc -l
50001
```

```
Active / Total Objects (% used)    : 473143 / 501469 (94.4%)
 Active / Total Slabs (% used)      : 20474 / 20474 (100.0%)
 Active / Total Caches (% used)     : 84 / 115 (73.0%)
 Active / Total Size (% used)       : 202333.53K / 214409.87K (94.4%)
 Minimum / Average / Maximum Object : 0.01K / 0.43K / 8.00K

  OBJS ACTIVE  USE OBJ SIZE  SLABS OBJ/SLAB CACHE SIZE NAME
103104 103082  99%    0.25K   6444       16     25776K kmalloc-256
 65472  65472 100%    0.06K   1023       64      4092K kmalloc-64
 62853  62853 100%    0.19K   2993       21     11972K dentry
 50250  50250 100%    0.62K   2010       25     32160K sock_inode_cache
 50240  50240 100%    1.94K   3140       16    100480K TCP
 17820  17742  99%    0.11K    495       36      1980K kernfs_node_cache
 14420   4257  29%    0.57K    515       28      8240K radix_tree_node

```

```
 cat /proc/meminfo
MemTotal:        8010316 kB
MemFree:         6657108 kB
MemAvailable:    6611656 kB
Buffers:            7768 kB
Cached:           540020 kB
Slab:             219084 kB
```

SLAB开销：(219084-53512)/50000=3.3 

# 5. TIME_WAIT

```
# netstat -antp | grep TIME_WAIT | wc -l
50016
```

```
 Active / Total Objects (% used)    : 269663 / 343285 (78.6%)
 Active / Total Slabs (% used)      : 11166 / 11166 (100.0%)
 Active / Total Caches (% used)     : 80 / 107 (74.8%)
 Active / Total Size (% used)       : 47838.35K / 60166.95K (79.5%)
 Minimum / Average / Maximum Object : 0.01K / 0.17K / 8.00K

  OBJS ACTIVE  USE OBJ SIZE  SLABS OBJ/SLAB CACHE SIZE NAME
144640  93988  64%    0.06K   2260       64      9040K kmalloc-64
 50032  50032 100%    0.25K   3127       16     12508K tw_sock_TCP
 21861  14721  67%    0.19K   1041       21      4164K dentry
 15834  15834 100%    0.10K    406       39      1624K buffer_head
```

```
cat /proc/meminfo
MemTotal:        8009284 kB
MemFree:         7199052 kB
MemAvailable:    7146056 kB
Buffers:           10260 kB
Cached:           546716 kB
Slab:              56776 kB

Slab:              60692 kB
```

SLAB开销：(60692-39848)/50000=0.41

备注，第一次做实验手慢了。TIME_WAIT是重做的，所以slab这里得调整一下。
(60692-39848)/50000=0.41
(60692-43764)/50000=0.338
(56776-39848)/50000=0.338

