# Learn OSCP



- [Windows Privilege Escalation Fundamentals](https://www.fuzzysecurity.com/tutorials/16.html)
- [AccessChk v6.12](https://docs.microsoft.com/en-us/sysinternals/downloads/accesschk) 用于确认windows用户权限
- [msfvenom-cheat-sheet](https://nitesculucian.github.io/2018/07/24/msfvenom-cheat-sheet/)



#### Tips



```vbscript
# 无法执行 whoami 以及 echo %username% 的场景
# 解决方案一
echo 1>who.ami
dir /q

# 解决方案二
# 上传 kali 中的 /usr/share/windows-binaries/whoami.exe 到服务器上
```






# ChangeLog
- 20190701 init
