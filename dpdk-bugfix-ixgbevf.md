# 什么问题

参考[bond网卡自动启停.md]()
- [bug描述](https://bugs.dpdk.org/show_bug.cgi?id=14)
- 当使用VF功能时，DPDK APP频繁重启，ixgbevf驱动会偶尔出现死锁导致crash

# patch

[邮件发送patch](https://mail.google.com/mail/u/0/#sent/FMfcgxmZSxTXqDZKdDTsCGkHHGDLbKlb)