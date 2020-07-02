## 解决在 pod 内无法 ping 通 servicename 和 ClusterIP

### 参考资料

> https://www.cnblogs.com/dribs/p/12666091.html

### 问题解决

#### 问题一

use 'nf_conntrack' instead of 'nf_conntrack_ipv4' for linux kernel >= 4.19

解决方案：

> https://github.com/coreos/bugs/issues/2518