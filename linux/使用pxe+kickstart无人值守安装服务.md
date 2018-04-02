##使用pxe+kickstart无人值守安装服务
###配yum源
/etc/yum.repos.d/xx.repo 把本地源改名或直接改

    [rhel7]         
	#the unique id of the yum reportory, avoid confict with other reportories
	name=rhel7
	baseurl=file:///media/cdrom     
	#include FTP(ftp://..),HTTP(http://..),local(file:///..)
	enabled=1       
	#1 is enabled, 0 is disabled
	gpgcheck=1      
	#1 is checked, 0 is not
	gpgkey=file:///media/cdrom/RPM-GPG-KEY-redhat-release   
	#the location of the public key

    [root@linuxprobe yum.repos.d]# mkdir -p /media/cdrom
	[root@linuxprobe yum.repos.d]# mount /dev/cdrom /media/cdrom
	mount: /dev/sr0 is write-protected, mounting read-only
	[root@linuxprobe yum.repos.d]# vim /etc/fstab
	/dev/cdrom /media/cdrom iso9660 defaults 0 0

###dhcp
	yum install dhcp

	[root@linuxprobe ~]# vim /etc/dhcp/dhcpd.conf
	allow booting;
	allow bootp;
	ddns-update-style interim;
	ignore client-updates;
	subnet 192.168.10.0 netmask 255.255.255.0 {
        option subnet-mask      255.255.255.0;
        option domain-name-servers  192.168.10.10;
        range dynamic-bootp 192.168.10.100 192.168.10.200;
        default-lease-time      21600;
        max-lease-time          43200;
        next-server             192.168.10.10;
        filename                "pxelinux.0";
	}

	[root@linuxprobe ~]# systemctl restart dhcpd
	[root@linuxprobe ~]# systemctl enable dhcpd

###tftp
	yum install tftp-server
	[root@linuxprobe ~.d]# vim /etc/xinetd.d/tftp
	service tftp
	{
        socket_type             = dgram
        protocol                = udp
        wait                    = yes
        user                    = root
        server                  = /usr/sbin/in.tftpd
        server_args             = -s /var/lib/tftpboot
        disable                 = no
        per_source              = 11
        cps                     = 100 2
        flags                   = IPv4
	[root@linuxprobe xinetd.d]# systemctl restart xinetd
	[root@linuxprobe xinetd.d]# systemctl enable xinetd
	
	[root@linuxprobe ~]# firewall-cmd --permanent --add-port=69/udp
	success
	[root@linuxprobe ~]# firewall-cmd --reload 
	success
###syslinux
	yum install syslinux
	[root@linuxprobe ~]# cd /var/lib/tftpboot
	[root@linuxprobe tftpboot]# cp /usr/share/syslinux/pxelinux.0 .
	[root@linuxprobe tftpboot]# cp /media/cdrom/images/pxeboot/{vmlinuz,initrd.img} .
	[root@linuxprobe tftpboot]# cp /media/cdrom/isolinux/{vesamenu.c32,boot.msg} .
	[root@linuxprobe tftpboot]# mkdir pxelinux.cfg
	[root@linuxprobe tftpboot]# cp /media/cdrom/isolinux/isolinux.cfg pxelinux.cfg/default
	[root@linuxprobe tftpboot]# vim pxelinux.cfg/default
	 **1 default linux **
	 2 timeout 600
	 3
	 4 display boot.msg
	 5
	 6 # Clear the screen when exiting the menu, instead of leaving the menu displa yed.
	 7 # For vesamenu, this means the graphical background is still displayed witho ut
	 8 # the menu itself for as long as the screen remains in graphics mode.
	 9 menu clear
	 10 menu background splash.png
	 11 menu title Red Hat Enterprise Linux 7.0
	 12 menu vshift 8
	 13 menu rows 18
	 14 menu margin 8
	 15 #menu hidden
	 16 menu helpmsgrow 15
	 17 menu tabmsgrow 13
	 18
	 19 # Border Area
	 20 menu color border * #00000000 #00000000 none
	 21
	 22 # Selected item
	 23 menu color sel 0 #ffffffff #00000000 none
	 24
	 25 # Title bar
	 26 menu color title 0 #ff7ba3d0 #00000000 none
	 27
	 28 # Press [Tab] message
	 29 menu color tabmsg 0 #ff3a6496 #00000000 none
	 30
	 31 # Unselected menu item
	 32 menu color unsel 0 #84b8ffff #00000000 none
	 33
	 34 # Selected hotkey
	 35 menu color hotsel 0 #84b8ffff #00000000 none
	 36
	 37 # Unselected hotkey
	 38 menu color hotkey 0 #ffffffff #00000000 none
	 39
	 40 # Help text
	 41 menu color help 0 #ffffffff #00000000 none
	 42 
	 43 # A scrollbar of some type? Not sure.
	 44 menu color scrollbar 0 #ffffffff #ff355594 none
	 45 
	 46 # Timeout msg
	 47 menu color timeout 0 #ffffffff #00000000 none
	 48 menu color timeout_msg 0 #ffffffff #00000000 none
	 49 
	 50 # Command prompt text
	 51 menu color cmdmark 0 #84b8ffff #00000000 none
	 52 menu color cmdline 0 #ffffffff #00000000 none
	 53 
	 54 # Do not display the actual menu unless the user presses a key. All that is displayed is a timeout message.
	 55 
	 56 menu tabmsg Press Tab for full configuration options on menu items.
	 57 
	 58 menu separator # insert an empty line
	 59 menu separator # insert an empty line
	 59 menu separator # insert an empty line
	 60 
	 61 label linux
	 62 menu label ^Install Red Hat Enterprise Linux 7.0
	 63 kernel vmlinuz
	 **64 append initrd=initrd.img inst.stage2=ftp://192.168.10.10 ks=ftp://192.168.10.10/pub/ks.cfg quiet**
	 65

###vsftpd
	yum install vsftpd
	[root@linuxprobe ~]# systemctl restart vsftpd
	[root@linuxprobe ~]# systemctl enable vsftpd
	[root@linuxprobe ~]# cp -r /media/cdrom/* /var/ftp
	[root@linuxprobe ~]# firewall-cmd --permanent --add-service=ftp
	success
	[root@linuxprobe ~]# firewall-cmd --reload 
	success
	[root@linuxprobe ~]# setsebool -P ftpd_connect_all_unreserved=on

###kickstart 应答文件
	[root@linuxprobe ~]# cp ~/anaconda-ks.cfg /var/ftp/pub/ks.cfg
	[root@linuxprobe ~]# chmod +r /var/ftp/pub/ks.cfg
	[root@linuxprobe ~]# vim /var/ftp/pub/ks.cfg 
	 1 #version=RHEL7
	 2 # System authorization information
	 3 auth --enableshadow --passalgo=sha512
	 4 
	 5 # Use CDROM installation media
	 6 url --url=ftp://192.168.10.10
	 7 # Run the Setup Agent on first boot
	 8 firstboot --enable
	 9 ignoredisk --only-use=sda
	 10 # Keyboard layouts
	 11 keyboard --vckeymap=us --xlayouts='us'
	 12 # System language
	 13 lang en_US.UTF-8
	 14 
	 15 # Network information
	 16 network --bootproto=dhcp --device=eno16777728 --onboot=off --ipv6=auto
	 17 network --hostname=localhost.localdomain
	 18 # Root password
	 19 rootpw --iscrypted $6$pDjJf42g8C6pL069$iI.PX/yFaqpo0ENw2pa7MomkjLyoae2zjMz2UZJ7b H3UO4oWtR1.Wk/hxZ3XIGmzGJPcs/MgpYssoi8hPCt8b/
	 20 # System timezone
	 21 timezone Asia/Shanghai --isUtc
	 22 user --name=linuxprobe --password=$6$a9v3InSTNbweIR7D$JegfYWbCdoOokj9sodEccdO.zL F4oSH2AZ2ss2R05B6Lz2A0v2K.RjwsBALL2FeKQVgf640oa/tok6J.7GUtO/ --iscrypted --gecos ="linuxprobe"
	 23 # X Window System configuration information
	 24 xconfig --startxonboot
	 25 # System bootloader configuration
	 26 bootloader --location=mbr --boot-drive=sda
	 27 autopart --type=lvm
	 28 # Partition clearing information
	 29 clearpart --all --initlabel
	 30 
	 31 %packages
	 32 @base
	 33 @core
	 34 @desktop-debugging
	 35 @dial-up
	 36 @fonts
	 37 @gnome-desktop
	 38 @guest-agents
	 39 @guest-desktop-agents
	 40 @input-methods
	 41 @internet-browser
	 42 @multimedia
	 43 @print-client
	 44 @x11
	 45 
	 46 %end

###自动部署
