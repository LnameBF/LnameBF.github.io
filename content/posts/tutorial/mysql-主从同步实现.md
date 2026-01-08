---
title: "MySQL 主从同步实现"
date: 2026-01-08T16:37:49+08:00
lastmod: 2026-01-08T16:37:49+08:00
author: ["wait"]
keywords: 
- 
categories:  # 没有分类界面可以不填写
- 
tags: # 标签
- 文章
description: "MySQL 主从同步实现"
weight:
slug: ""
draft: false # 是否为草稿
comments: true # 本页面是否显示评论
reward: true # 打赏
mermaid: true #是否开启mermaid
showToc: true # 显示目录
TocOpen: true # 自动展开目录
hidemeta: false # 是否隐藏文章的元信息，如发布日期、作者等
disableShare: true # 底部不显示分享栏
showbreadcrumbs: true #顶部显示路径
cover:
    image: "" #图片路径例如：posts/tech/123/123.png
    zoom: # 图片大小，例如填写 50% 表示原图像的一半大小
    caption: "" #图片底部描述
    alt: ""
    relative: false
---

# MySQL 主从同步实现

MySQL 主从复制（Replication）是数据库常用的容灾和读写分离解决方案。本文将详细介绍 MySQL 主从同步的原理、配置步骤和维护建议。

## 目录

- [原理](#原理)
- [前提条件](#前提条件)
- [主从同步配置步骤](#主从同步配置步骤)
- [验证复制状态](#验证复制状态)
- [常见问题及解决方案](#常见问题及解决方案)
- [性能优化与监控](#性能优化与监控)
- [高可用方案](#高可用方案)

## 原理

MySQL 的主从复制是通过以下三个步骤完成的：

1. **主库记录变更**：主数据库（Master）执行事务时，会记录所有数据变更到二进制日志（Binary Log）。
2. **从库读取变更**：从数据库（Slave）的 I/O 线程连接到主库，读取二进制日志，并写入本地的中继日志（Relay Log）。
3. **从库应用变更**：从库的 SQL 线程读取中继日志中的事件，并将其应用到从库。

![MySQL主从复制原理](https://dev.mysql.com/doc/refman/8.0/en/images/mysql-replication.png)

## 前提条件

- 两台或多台 MySQL 服务器（一台作为主库，其他作为从库）
- 所有服务器应使用相同版本或兼容版本的 MySQL
- 服务器间网络连通
- 主从服务器上的防火墙允许 MySQL 端口（默认 3306）通信

## 主从同步配置步骤

### 1. 配置主库

1. **编辑主库 MySQL 配置文件**（通常是 `/etc/mysql/my.cnf` 或 `/etc/my.cnf`）：

```ini
[mysqld]
# 每个服务器的ID必须唯一，主库通常设为较小值
server-id = 1
# 启用二进制日志
log-bin = mysql-bin
# 可选：指定要复制的数据库
binlog-do-db = your_database_name
# 可选：指定不复制的数据库
binlog-ignore-db = mysql
# 设置二进制日志格式
binlog_format = ROW
```

2. **重启 MySQL 服务**：

```bash
sudo systemctl restart mysql
# 或
sudo service mysql restart
```

3. **创建复制用户**：

```sql
CREATE USER 'repl_user'@'%' IDENTIFIED BY 'repl_password';
GRANT REPLICATION SLAVE ON *.* TO 'repl_user'@'%';
FLUSH PRIVILEGES;
```

4. **获取二进制日志信息**：

```sql
SHOW MASTER STATUS;
```

记下输出中的 `File` 和 `Position` 值，这将在配置从库时使用。

### 2. 准备从库初始数据

如果主库已经包含数据，您需要先将这些数据复制到从库。可以使用以下方法之一：

**方法一：使用 mysqldump**（适用于较小的数据库）

1. 在主库上执行：

```bash
mysqldump --all-databases --master-data=2 -u root -p > dump.sql
```

2. 将 dump.sql 传输到从库：

```bash
scp dump.sql user@slave_host:/tmp/
```

3. 在从库上导入：

```bash
mysql -u root -p < /tmp/dump.sql
```

**方法二：使用物理备份**（适用于较大的数据库）

1. 停止主库：

```bash
sudo systemctl stop mysql
```

2. 复制数据目录：

```bash
rsync -avP /var/lib/mysql/ user@slave_host:/var/lib/mysql/
```

3. 重启主库：

```bash
sudo systemctl start mysql
```

### 3. 配置从库

1. **编辑从库配置文件**：

```ini
[mysqld]
# 从库ID必须唯一且不同于主库
server-id = 2
# 可选：开启中继日志
relay-log = mysql-relay-bin
# 可选：指定要复制的数据库
replicate-do-db = your_database_name
# 可选：在从库启用只读模式
read_only = 1
```

2. **重启从库 MySQL**：

```bash
sudo systemctl restart mysql
```

3. **配置从库连接到主库**：

```sql
CHANGE MASTER TO
    MASTER_HOST='master_ip_address',
    MASTER_PORT=3306,
    MASTER_USER='repl_user',
    MASTER_PASSWORD='repl_password',
    MASTER_LOG_FILE='记录的File值',
    MASTER_LOG_POS=记录的Position值;
```

4. **启动从库复制进程**：

```sql
START SLAVE;
```

## 验证复制状态

在从库上执行以下命令检查复制状态：

```sql
SHOW SLAVE STATUS\G
```

正常工作的从库应显示：

- `Slave_IO_Running: Yes`
- `Slave_SQL_Running: Yes`
- `Seconds_Behind_Master` 显示复制延迟秒数

## 常见问题及解决方案

### 1. 复制无法启动

**症状**：`Slave_IO_Running` 或 `Slave_SQL_Running` 为 `No`

**解决方案**：

- 检查 `Last_IO_Error` 或 `Last_SQL_Error` 字段中的错误信息
- 确认网络连接和防火墙设置
- 验证复制用户权限
- 检查二进制日志文件和位置是否正确

### 2. 数据不一致

**解决方案**：

```sql
-- 停止从库
STOP SLAVE;

-- 跳过错误（谨慎使用）
SET GLOBAL sql_slave_skip_counter = 1;

-- 或重新设置复制点
CHANGE MASTER TO MASTER_LOG_FILE='新文件', MASTER_LOG_POS=新位置;

-- 重新启动从库
START SLAVE;
```

### 3. 复制延迟

**解决方案**：

- 优化主库写入性能
- 增加从库硬件资源
- 考虑使用并行复制（MySQL 5.7+）：

```ini
# 在从库配置中添加
slave_parallel_workers = 4
slave_parallel_type = LOGICAL_CLOCK
```

## 性能优化与监控

### 1. 监控复制延迟

```sql
-- 在从库上执行
SHOW SLAVE STATUS\G

-- 查找 Seconds_Behind_Master 字段
```

### 2. 设置复制警报

可以创建脚本定期检查复制状态，当发现问题时发送告警：

```bash
#!/bin/bash
mysql -u root -p -e "SHOW SLAVE STATUS\G" | grep "Seconds_Behind_Master" | awk '{print $2}' | while read delay; do
  if [ "$delay" == "NULL" ] || [ "$delay" -gt 300 ]; then
    echo "警告：MySQL复制延迟超过5分钟或复制中断" | mail -s "MySQL复制告警" admin@example.com
  fi
done
```

### 3. 使用 Prometheus + Grafana 监控

使用 mysqld_exporter 采集 MySQL 指标，并在 Grafana 中创建复制状态仪表板。

## 高可用方案

### 1. MySQL Group Replication

适用于 MySQL 5.7 及以上版本，实现组复制和自动故障转移。

### 2. MySQL InnoDB Cluster

结合 MySQL Router 和 MySQL Shell，提供完整的高可用解决方案。

### 3. 第三方工具

- **MHA (Master High Availability)**：提供主库故障时的自动故障转移
- **Orchestrator**：MySQL 复制拓扑管理和可视化工具
- **ProxySQL**：实现读写分离和负载均衡

## 结语

MySQL 主从同步是提高数据库可用性和性能的重要手段。正确配置和维护主从复制可以实现：

- 读写分离，提高系统性能
- 数据备份，提高数据安全性
- 高可用性，减少系统故障对业务的影响

随着业务增长，可以考虑扩展到更复杂的拓扑结构，如主-主复制、一主多从等，以满足更高的要求。
