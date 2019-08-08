# T1169-Sudo

## 来自ATT&CK的描述

sudoers文件`/etc/sudoers`描述了哪些用户可以运行哪些命令以及从哪些终端运行。这还描述了用户可以作为其他用户或组运行的命令。这提供了最小特权的概念，使得用户在大多数时间以最低可能的权限运行，并且仅在需要时提升到其他用户或权限，通常通过提示输入密码。但是，sudoers文件还可以指定何时不提示用户输入类似`user1 ALL=(ALL) NOPASSWD: ALL` [[1\]](https://blog.malwarebytes.com/threat-analysis/2017/04/new-osx-dok-malware-intercepts-web-traffic/)的行的密码。

攻击者可以利用这些配置来执行其他用户的命令或生成具有更高权限的进程。您必须具有提升权限才能编辑此文件。

## 模拟攻击

 cat /etc/sudoers

 vim /etc/sudoers

值得注意的是：攻击者可以利用这些配置来执行其他用户的命令或生成具有更高权限的进程。您必须具有提升权限才能编辑此文件。

## 检测日志源

linux audit日志 （值得注意的是：Ubuntu默认情况下没有audit，需要下载安装并配置相关策略）

## 攻击复现

### 场景一

icbc@icbc:/$ sudo cat /etc/sudoers

### 场景二

icbc@icbc:/$ sudo vim /etc/sudoers

## 攻击留痕

type=USER_CMD msg=audit(1563520773.609:436): pid=3530 uid=1000 auid=1000 ses=3 msg='cwd="/" cmd=636174202F6574632F7375646F657273 terminal=pts/0 res=success'

type=SYSCALL msg=audit(1563520773.609:439): arch=c000003e syscall=257 success=yes exit=3 a0=ffffff9c a1=7fffa02417d1 a2=0 a3=0 items=1 ppid=3530 pid=3531 auid=1000 uid=0 gid=0 euid=0 suid=0 fsuid=0 egid=0 sgid=0 fsgid=0 tty=pts0 ses=3 comm="cat" exe="/usr/bin/cat" key="sudoers_110"

值得注意的是：这里只提取出了异常日志，故省略了很多日志细节。

## 检测规则

index=linux sourcetype="linux_audit" sudoers_110

值得注意的是：你需要自行配置Audit审核规则：root@icbc:~# auditctl -w /etc/sudoers -p war -k sudoers_110

## 参考推荐

Audit配置手册：https://www.cnblogs.com/bldly1989/p/7204358.html
