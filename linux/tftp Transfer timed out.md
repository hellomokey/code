###tftp Transfer timed out.

tftp服务器是开了的：
[tangzp@bogon test]$ netstat -a |  grep tftp<br>
udp        0      0 0.0.0.0:tftp            0.0.0.0:* 

[tangzp@bogon test]$ netstat -tunap | grep :69<br>
(Not all processes could be identified, non-owned process info
 will not be shown, you would have to be root to see it all.)<br>
udp        0      0 0.0.0.0:69              0.0.0.0:*  

[tangzp@bogon test]$ sudo service xinetd status -l<br>
Redirecting to /bin/systemctl status  -l xinetd.service<br>
xinetd.service - Xinetd A Powerful Replacement For Inetd<br>
   Loaded: loaded (/usr/lib/systemd/system/xinetd.service; enabled)<br>
   Active: active (running) since Sat 2015-05-30 21:29:17 CST; 12min ago<br>
  Process: 1298 ExecStart=/usr/sbin/xinetd -stayalive -pidfile /var/run/<br>xinetd.pid $EXTRAOPTIONS (code=exited, status=0/SUCCESS)<br>
 Main PID: 1302 (xinetd)<br>
   CGroup: /system.slice/xinetd.service<br>
           1302 /usr/sbin/xinetd -stayalive -pidfile /var/run/xinetd.pid<br>

May 30 21:29:17 localhost.localdomain xinetd[1302]: removing discard<br>
May 30 21:29:17 localhost.localdomain xinetd[1302]: removing discard<br>
May 30 21:29:17 localhost.localdomain xinetd[1302]: removing echo<br>
May 30 21:29:17 localhost.localdomain xinetd[1302]: removing echo<br>
May 30 21:29:17 localhost.localdomain xinetd[1302]: removing tcpmux<br>
May 30 21:29:17 localhost.localdomain xinetd[1302]: removing time<br>
May 30 21:29:17 localhost.localdomain xinetd[1302]: removing time<br>
May 30 21:29:17 localhost.localdomain xinetd[1302]: xinetd Version 2.3.15 started<br> with libwrap loadavg labeled-networking options compiled in.<br>
May 30 21:29:17 localhost.localdomain xinetd[1302]: Started working: 1 available service<br>
May 30 21:29:17 localhost.localdomain systemd[1]: Started Xinetd A Powerful Replacement For Inetd.<br>

其次，服务器是配置如下：<br>
[tangzp@bogon test]$ cat /etc/xinetd.d/tftp<br>
/# default: off<br>
/# description: The tftp server serves files using the trivial file transfer \<br>
/#	protocol.  The tftp protocol is often used to boot diskless \<br>
/#	workstations, download configuration files to network-aware printers, \<br>
/#	and to start the installation process for some operating systems.<br>
service tftp<br>
{<br>
socket_type	= dgram<br>
protocol	= udp<br>
wait	= yes<br>
user	= root<br>
server	= /usr/sbin/in.tftpd<br>
server_args	= -c  /var/lib/tftpboot <br>
disable	= no<br>
per_source	= 11<br>
cps	= 100 2<br>
flags	= IPv4<br>
}<br>

目录同样是可写的：<br>
[tangzp@bogon test]$ sudo chmod 777 -R /var/lib/tftpboot/<br>
[tangzp@bogon test]$ ls -l /var/lib/<br>
total 24<br>
............<br>
drwxrwxrwx. 2 root         root       6 Jun 10  2014 tftpboot<br>
.............<br>
然后防火墙和selinux