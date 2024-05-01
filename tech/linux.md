**imlcm的Linux 系统使用笔记**

- [**1.系统的安装使用**](#1系统的安装使用)
  - [**windows与虚拟机的互动**](#windows与虚拟机的互动)
    - [**1.关于网络设置**](#1关于网络设置)
    - [**2.挂载共享文件（实体机下可配置samba服务）**](#2挂载共享文件实体机下可配置samba服务)
    - [**3.Ubuntu没有rc.local(开机自动挂载共享文件夹)**](#3ubuntu没有rclocal开机自动挂载共享文件夹)
  - [wsl迁移到B电脑](#wsl迁移到b电脑)
- [**2.系统管理**](#2系统管理)
  - [如何确保自己的linux系统处于自己的掌控状态？](#如何确保自己的linux系统处于自己的掌控状态)
    - [1. 用户管理](#1-用户管理)
    - [**wsl bug** ：忘了root密码](#wsl-bug-忘了root密码)
    - [2. 环境变量管理](#2-环境变量管理)
    - [3. 端口管理](#3-端口管理)
    - [4. 进程监控](#4-进程监控)
    - [5. 系统日志](#5-系统日志)
    - [6. 软件包管理](#6-软件包管理)
    - [7. 文件系统权限](#7-文件系统权限)
    - [8. 备份和恢复策略](#8-备份和恢复策略)
    - [9. 安全更新和补丁](#9-安全更新和补丁)
    - [10. 物理安全](#10-物理安全)
  - [系统用户](#系统用户)



#  **1.系统的安装使用**

## **windows与虚拟机的互动**

### **1.关于网络设置**

双网络模式
网络1：NAT模式（实现访问外网）
网络2：host-only模式，虚拟机和linux系统同一个网段就行（实现主机互通）。

### **2.挂载共享文件（实体机下可配置samba服务）**     
(1)切换到root用户输入挂载命令：     
`sudo mount -t vboxsf shared_file /home/xingoo/shared`

(2)自动挂载:直接写入bashrc
`vi ~/.bashrc'
```
#added by lcm
#automount windows share file
mount -t vboxsf share /home/imlcm/share
```
挂载失败了，需要sudo
vi退出： ESC    ：wq

### **3.Ubuntu没有rc.local(开机自动挂载共享文件夹)**

    当我们设置开机自启时候，一般都在rc.local文件里设置，但是有的Ubuntu版本没有这个文件了，此时我们可以自己创建一个。

a.创建一个rc-local.service文件        
`sudo vim /etc/systemd/system/rc-local.service`     
  复制粘贴以下内容到文件里
  ```
    [Unit]
    Description=/etc/rc.local Compatibility
    ConditionPathExists=/etc/rc.local
    [Service]
    Type=forking
    ExecStart=/etc/rc.local start
    TimeoutSec=0
    StandardOutput=tty
    RemainAfterExit=yes
    SysVStartPriority=99
    [Install]
    WantedBy=multi-user.target 
 ```
b. 启动rc-local.service服务并设置开机自启
```
sudo systemctl start rc-local.service
sudo systemctl enable rc-local.service
```
c.创建/etc/rc.local文件     
`sudo vim /etc/rc.local`        
 复制粘贴以下内容到文件里       
 ```
    #!/bin/sh -e
    #
    # rc.local
    #
    # This script is executed at the end of each multiuser runlevel.
    # Make sure that the script will "exit 0" on success or any other
    # value on error.
    #
    # In order to enable or disable this script just change the execution
    # bits.
    #
    # By default this script does nothing.
    Moutn -t vboxsf share /home/imlcm/share
    exit 0
 ```        
给予脚本执行权限        
`sudo chmod 744 /etc/rc.local`





[def]: #windows与虚拟机的互动

## wsl迁移到B电脑
A电脑，export tar文件
B电脑：（1）启用wsl；（2）windows官网下载Linux 内核包；（3）import； （4）运行； （5）运行有些命令可能出问题，如shutdown执行不了，需要推出后wsl --update更新。who命令没返回，source .bashrc即可。

# **2.系统管理**

## 如何确保自己的linux系统处于自己的掌控状态？ 

要确保自己的Linux系统处于自己的掌控状态，以下是一些关键方面需要注意：

1. **用户管理**：
   - 创建和管理用户账户，确保每个用户都有适当的权限和访问级别。
   - 使用sudo命令来执行需要管理员权限的操作，而不是直接使用root账户。
   - 定期检查和清理不再需要的用户账户，避免潜在的安全风险。

2. **环境变量管理**：
   - 理解环境变量的作用和影响，特别是`PATH`、`HOME`、`LD_LIBRARY_PATH`等关键变量。
   - 不要随意修改系统级别的环境变量文件（如`/etc/environment`或`/etc/profile`），除非清楚知道自己在做什么。
   - 在用户的shell配置文件中（如`.bashrc`、`.bash_profile`）管理个人环境变量，避免影响其他用户。

3. **端口管理**：
   - 使用`netstat`、`ss`或`lsof`命令查看当前开放的端口和连接状态。
   - 关闭不必要的服务和端口，减少攻击面。
   - 配置防火墙规则，只允许必要的入站和出站流量。

4. **进程监控**：
   - 使用`top`、`htop`或`ps`命令查看当前运行的进程，确保没有异常或可疑的进程在运行。
   - 配置系统监控工具（如Nagios、Zabbix）来实时监控系统的性能、安全状况等。

5. **系统日志**：
   - 定期查看和分析系统日志（如`/var/log/syslog`、`/var/log/auth.log`等），以便及时发现潜在的安全问题或异常行为。
   - 配置日志轮转和保留策略，避免日志占用过多的磁盘空间。

6. **软件包管理**：
   - 使用包管理器（如apt、yum、dnf等）来安装、更新和卸载软件包，确保系统的软件包处于最新状态。
   - 避免从不可信的来源安装软件包，以防止恶意软件的入侵。

7. **文件系统权限**：
   - 确保文件和目录的权限设置合理，避免未经授权的访问和修改。
   - 使用`chmod`和`chown`命令来修改文件和目录的权限和所有权。

8. **备份和恢复策略**：
   - 制定并定期执行备份计划，确保重要数据的安全性。
   - 熟悉数据恢复流程，以便在发生意外情况时能够迅速恢复系统和数据。

9. **安全更新和补丁**：
   - 及时关注系统安全公告和漏洞信息，应用相关的安全更新和补丁。
   - 使用自动化工具（如unattended-upgrades）来自动安装安全更新。

10. **物理安全**：
    - 确保服务器或计算机的物理安全，防止未经授权的访问和篡改。
    - 使用强密码和访问控制策略来保护远程访问和物理访问。

总之，要确保Linux系统处于自己的掌控状态，需要从多个方面入手，包括用户管理、环境变量管理、端口管理、进程监控、系统日志、软件包管理、文件系统权限、备份和恢复策略、安全更新和补丁以及物理安全等。通过综合应用这些措施，可以提高系统的安全性和稳定性，降低潜在的安全风险。

---

当然可以，以下是对您提到的每一部分的详细管理策略及配套的命令，并提供了一些示例。

### 1. 用户管理

**管理策略**:
- 创建用户时指定用户组、主目录和默认shell。
- 定期审核用户列表，删除或禁用不活跃的用户账户。
- 使用sudo而不是root用户执行特权命令。

**命令**:
- 创建用户: `sudo adduser <username>`
- 删除用户: `sudo deluser <username>`
- 修改用户密码: `sudo passwd <username>`
- 查看所有用户: `cat /etc/passwd`

### **wsl bug** ：忘了root密码
对于Windows Subsystem for Linux (WSL) 中 Ubuntu 忘了 root 密码的问题，可以按照以下步骤进行密码重置：
w
1. **打开 PowerShell**：
   首先，需要在 Windows 环境下打开 PowerShell。这是执行与 WSL 相关的命令所必需的。

2. **进入 WSL 的 Ubuntu 实例**：
   在 PowerShell 中，使用以下命令进入 WSL 的 Ubuntu 实例。如果 Ubuntu 是你的默认 WSL 分发版，你可以简单地输入：
   ```powershell
   wsl -u root
   ```
   如果你需要进入非默认的 WSL 分发版，比如你的分发版名称是 Ubuntu-20.04，则使用：
   ```powershell
   wsl -d Ubuntu-20.04 -u root
   ```

3. **重置 root 密码**：
   一旦以 root 用户身份进入 Ubuntu 实例，你可以使用 `passwd` 命令来重置密码。系统会提示你输入新的密码并确认。
   ```bash
   passwd root
   ```
   然后按照提示输入新的密码并确认。

4. **退出 WSL**：
   完成密码重置后，可以使用 `exit` 命令退出 WSL 的 Ubuntu 实例。
   ```bash
   exit
   ```

如果你无法通过上述方法以 root 用户身份进入 WSL，可能是因为 root 用户没有设置密码或者由于其他原因无法直接登录。在这种情况下，你可以尝试以下替代方法：

- **通过已知密码的用户修改 root 密码**：如果你有一个具有 sudo 权限的用户账户和该账户的密码，你可以通过这个账户登录 WSL，并使用 `sudo passwd root` 命令来设置或重置 root 密码。
  
- **重新安装 WSL**：如果上述方法都不可行，且你无法接受数据丢失的风险，最后的手段可能是卸载并重新安装 WSL。请注意，这将导致你丢失所有在 WSL 中的数据，因此应谨慎考虑。

在执行任何操作之前，请确保已备份所有重要数据，以防数据丢失。

### 2. 环境变量管理

**管理策略**:
- 在用户的shell配置文件中（如`.bashrc`、`.bash_profile`）定义个人环境变量。
- 避免在系统级别的配置文件中修改环境变量，除非有充分理由。

**命令**:
- 查看当前环境变量: `printenv` 或 `env`
- 设置环境变量: `export VARNAME=value`
- 在`.bashrc`中添加环境变量: `echo 'export VARNAME=value' >> ~/.bashrc`

### 3. 端口管理

**管理策略**:
- 仅开放必要的端口。
- 使用防火墙工具（如iptables或ufw）管理端口访问。

**命令**:
- 查看所有监听端口: `netstat -tuln` 或 `ss -tuln`
- 开放端口（以ufw为例）: `sudo ufw allow <port>/<protocol>`
- 关闭端口（以ufw为例）: `sudo ufw deny <port>/<protocol>`

### 4. 进程监控

**管理策略**:
- 定期检查运行中的进程，确保没有异常或可疑进程。
- 使用系统监控工具进行实时监控。

**命令**:
- 查看所有进程: `ps aux` 或 `top`
- 杀死进程: `kill <pid>` 或 `kill -9 <pid>`
- 使用htop进行交互式监控: `htop`

### 5. 系统日志

**管理策略**:
- 定期查看和分析系统日志，以便及时发现潜在问题。
- 配置日志轮转策略，避免日志占用过多磁盘空间。

**命令**:
- 查看系统日志: `cat /var/log/syslog` 或 `journalctl`
- 实时查看日志: `tail -f /var/log/syslog`
- 配置日志轮转: `/etc/logrotate.conf` 和 `/etc/logrotate.d/` 目录下的配置文件

### 6. 软件包管理

**管理策略**:
- 使用包管理器进行软件包的安装、更新和卸载。
- 定期更新软件包以保持系统安全。

**命令**:
- 安装软件包: `sudo apt install <package>`（以Debian/Ubuntu为例）
- 更新软件包列表: `sudo apt update`
- 升级所有软件包: `sudo apt upgrade`

### 7. 文件系统权限

**管理策略**:
- 设置适当的文件和目录权限，避免未经授权的访问。
- 定期审查权限设置，确保它们符合安全最佳实践。

**命令**:
- 改变文件权限: `chmod <options> <file>`
- 改变文件所有者: `chown <user>:<group> <file>`
- 改变文件所属组: `chgrp <group> <file>`

### 8. 备份和恢复策略

**管理策略**:
- 制定备份计划，包括备份频率、保留期限和存储位置。
- 定期测试备份的恢复过程，确保备份的有效性。

**命令**:
- 使用`tar`命令进行手动备份: `tar -czvf backup.tar.gz /path/to/directory`
- 使用专门的备份工具（如`rsync`、`duplicity`等）进行更复杂的备份策略。

### 9. 安全更新和补丁

**管理策略**:
- 启用自动安全更新，确保系统及时获得安全补丁。
- 定期手动检查并应用安全更新。

**命令**:
- 在Debian/Ubuntu上启用自动安全更新: 配置`/etc/apt/apt.conf.d/50unattended-upgrades`文件。
- 手动检查并应用安全更新: `sudo apt update && sudo apt upgrade`

### 10. 物理安全

**管理策略**:
- 确保服务器或计算机的物理访问受到限制。
- 使用强密码和访问控制策略来保护远程访问。

**命令**:
- 物理安全主要依赖于物理控制措施，而不是命令行工具。但你可以使用`passwd`命令来设置强密码。

以上是一些基本的管理策略和命令示例，具体实施时可能需要根据你的具体环境和需求进行调整。

## 系统用户

**(base) imlcm@landuse:/mnt/c/Users/imlcm$ cat /etc/passwd**
root:x:0:0:root:/root:/bin/bash
daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin
bin:x:2:2:bin:/bin:/usr/sbin/nologin
sys:x:3:3:sys:/dev:/usr/sbin/nologin
sync:x:4:65534:sync:/bin:/bin/sync
games:x:5:60:games:/usr/games:/usr/sbin/nologin
man:x:6:12:man:/var/cache/man:/usr/sbin/nologin
lp:x:7:7:lp:/var/spool/lpd:/usr/sbin/nologin
mail:x:8:8:mail:/var/mail:/usr/sbin/nologin
news:x:9:9:news:/var/spool/news:/usr/sbin/nologin
uucp:x:10:10:uucp:/var/spool/uucp:/usr/sbin/nologin
proxy:x:13:13:proxy:/bin:/usr/sbin/nologin
www-data:x:33:33:www-data:/var/www:/usr/sbin/nologin
backup:x:34:34:backup:/var/backups:/usr/sbin/nologin
list:x:38:38:Mailing List Manager:/var/list:/usr/sbin/nologin
irc:x:39:39:ircd:/run/ircd:/usr/sbin/nologin
gnats:x:41:41:Gnats Bug-Reporting System (admin):/var/lib/gnats:/usr/sbin/nologin
nobody:x:65534:65534:nobody:/nonexistent:/usr/sbin/nologin
systemd-network:x:100:102:systemd Network Management,,,:/run/systemd:/usr/sbin/nologin
systemd-resolve:x:101:103:systemd Resolver,,,:/run/systemd:/usr/sbin/nologin
messagebus:x:102:105::/nonexistent:/usr/sbin/nologin
systemd-timesync:x:103:106:systemd Time Synchronization,,,:/run/systemd:/usr/sbin/nologin
syslog:x:104:111::/home/syslog:/usr/sbin/nologin
_apt:x:105:65534::/nonexistent:/usr/sbin/nologin
uuidd:x:106:112::/run/uuidd:/usr/sbin/nologin
tcpdump:x:107:113::/nonexistent:/usr/sbin/nologin
imlcm:x:1000:1000:,,,:/home/imlcm:/bin/bash
sshd:x:108:65534::/run/sshd:/usr/sbin/nologin
rtkit:x:109:117:RealtimeKit,,,:/proc:/usr/sbin/nologin

> 怎么有这么多用户，都是干啥的

在Linux系统中，这些用户账户大多数都是为了特定的系统服务或功能而创建的。它们被称为系统用户或伪用户，通常没有登录shell，也不用于人机交互。下面是对一些常见系统用户的简要解释：

- **root**：超级用户，拥有系统的最高权限。用于执行系统级的任务和管理操作。
- **daemon**、**bin**、**sys**、**sync**、**games**、**man**、**lp**、**mail**、**news**、**uucp**、**proxy**、**www-data**、**backup**、**list**、**irc**、**gnats**、**nobody**：这些用户通常是为了运行特定的系统服务或守护进程而创建的。例如，`www-data`用户通常用于运行Web服务器进程。
- **systemd-network**、**systemd-resolve**、**messagebus**、**systemd-timesync**、**syslog**：这些用户与systemd系统和服务管理器相关，用于执行特定的系统任务，如网络配置、时间同步和日志记录。
- **_apt**、**uuidd**、**tcpdump**：这些用户是为特定的应用程序或工具创建的，以限制它们对系统资源的访问权限。例如，`_apt`用户用于APT包管理器，而`tcpdump`用户用于网络数据包捕获工具。
- **imlcm**：这是一个普通用户账户，通常用于人机交互和日常任务。与系统用户不同，普通用户具有有限的权限，并受到系统安全策略的限制。
- **sshd**：用于运行SSH守护进程，以提供远程登录功能。
- **rtkit**：实时工具包守护进程，用于管理实时任务的优先级。

这些系统用户和服务账户的存在是为了确保系统的安全性和稳定性。通过将不同的服务和任务分配给不同的用户账户，可以限制它们对系统资源的访问权限，减少潜在的安全风险。同时，这也使得系统管理和维护更加灵活和可控。

需要注意的是，不同的Linux发行版可能会使用不同的系统用户和服务账户。因此，在实际使用中，你可能会遇到其他特定的系统用户。如果你对某个特定的系统用户有疑问，可以查看相关的系统文档或手册以获取更多信息。
