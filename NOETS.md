## syslog

> /var/log/messages

> tail -f /var/log/messages
> /etc/rsyslog.conf

### log rotation

> /etc/logrotate.conf
> /etc/logrotate.d

> firewall-cmd --parmanent --add service=syslog

> *.* @@ip:514

## Mange logs with jounald

> journalctl -n 10
> journalctl -f
> journalctl -p err
> journalctl --since='2024-04-25'
> journalctl --since='2024-04-24' --until='2024-04-25'
> journalctl --since='2024-04-24' --until='2024-04-25' -p err

> journalctl -xau sshd

log to RAM by default

> /var/log/journal/

> /etc/systemd/journald.conf

> storage=auto
> storage=no
> storage=persistent # create forder if not exists

> ForwardToSyslog=yes


> usermod -a -G adm dpezet

### journald remote logging

> yum install systemd-journal-remote

> /etc/systemd/journal-upload.conf

## Audit passord

> chage  -l username username
> chage  -M(max) 90 username
> chage  -m(min) 1 username
> chage  -E(expire) 09/22/2027 username
> chage  -E(expire) 09/22/2027 -W 7 username

> /etc/login.defs
> /etc/security/pwquality.conf

minlen should be minmum credits

> echo -n pass | shar256sum


> john the ripper (TODO)

> openwall.com

###  Audit The Filesystem

> yum install audit
> systemctl enable --now auditd


> /etc/audit/auditd.conf

> max_log_file = 8

> auditctl -s
> auditctl -l
> auditctl -w  /filepath -p rwx -k rule_name
> auditctl -w  /dirpath/ -p w -k rule_name

>
> /etc/audit/rules.d/audit.rules

>  /var/log/audit/audit.log

> ausearch  -k keyname


### Audit suid and sgid 

```bash

ls -lh /usr/bin/chsh
-rws--x--x 1 root root 25K May 21  2024 /usr/bin/chsh



```


> -rws
> -rwS

> chmod u+s filepath
> chmod 4777 filepath


> chmod g+s filepath
> chmod 2777 filepath

> find / -perm -u+s
> --- find / -perm +4000



### Failtoban

> FreePBX

> SIP

> yum install fail2ban

> systemctl enable --now fail2ban

> /var/log/fail2ban.log

> fail2ban-client status
> cd /etc/fail2ban/
> touch jail.local

```
[DEFAULT]
bantime = 3600 #3600 seconds by default
sender=root@local
destination=root@local
action=%(action_)s
action=%(action_mwl)s
ignoreip=ip
[sshd]
enabled = true


> hydra

> hydra -l root -t2 -P /passlist.txt ip ssh

> https://github.com/danielmiessler/SecLists/blob/master/Passwords/Common-Credentials/10k-most-common.txt


> firewall-cmd --list.all

```

> cd /etc/fail2ban/filter.d


## LUKS disk encryption(TODO)

## Data Encryption with GPG(TODO)

> /proc/sys/kernel/random/entropy_avail


### MD5 and SHA checksums


> https://github.com/nneonneo/sha1collider



> shar256sum --check shar256sum.txt

> shar256sum.txt.asc # signed with a GPG/PGP key


> centos.org/keys

> gpg -import key

> gpg --verify ascfile.asc
> shar256sum --check ascfile.asc

> gpg --delete-key  key-fignerprint

### Data Encryption with SSH


> /etc/ssh/sshd_config

> Protocol 2

> rm -f /etc/ssh/*key*

> ssh-keygen -t ecdsa -f /etc/ssh/ssh_host_ecdsa_key
> ssh-keygen -t rsa -f /etc/ssh/ssh_host_rsa_key
> ssh-keygen -t ed25519 -f /etc/ssh/ssh_host_ed25519_key

> /etc/init.d/ssh restart

> ssh-keyscan server_address >> /root/.ssh/known_hosts.

> ssh-keygen -l # get ssh key figerprint

> /etc/ssh/ssh_config
> StrictHostKeyChecking on


### Secure tunnel
1
> ssh -f user@remote  -L 65023:remote:23 -N
> telnet localhost 65023


## Selinux

> sestatus

> /etc/selinux/semanage.conf
> /etc/selinux/config

> chcon -Rv --type=context_type /folder

> restore -Rv /folder

selinux default policy

> /etc/selinux/targeted/contexts/files/file_contexts

> yum install policycoreutils-python

> semanage

> semanage fcontext -s -a https_sys_contextt /website

> semanage fcontext -s -a https_sys_contextt "/website/(/.*)?"


> setenforece 0

> grep httpd /var/log/audit/audit.log

> grep httpd /var/log/audit/audit.log | auditallow -m httpd -w 
> grep httpd /var/log/audit/audit.log | auditallow -M httpd 

> httpd.pe
> httpd.pp

> semodule -i httpd.pp
> semodule -l 

> semanage port -l
> semanage port -a -t http_port_t -p tcp 8080


## chroot jails


> useradd service1
> mkdir  -p /jail/service1
> cd /jail/service1
> mkdir  /jail/service1/home/service1
> mkdir dev etc bin lib64


> mknod -m 666 /jail/service1/dev/null c 1 3
> mknod -m 666 /jail/service1/dev/tty c 5 0
> mknod -m 666 /jail/service1/dev/zero c 1 5
> mknod -m 666 /jail/service1/dev/random c 1 8

> echo export PATH=/bin >>/jail/service1/home/servide1/.profile
> cp /bin/bash /jail/service1/bin/
> cp /bin/ls /jail/service1/bin/
> ldd /bin/bash
> ldd /bin/ls

> vim /etc/ssh/sshd_config
> match user service1
>   ChrootDirectory /jail/service1

> systemctl restart sshd

## LXC Containers

```bash
yum install epel-release
yum install lxc
yum install lxc-templates
yum install libvirt
yum install lxc-extra
yum install perl
systemctl enable --now lxc
systemctl enable --now libvirtd


lxc-checkconfig

/usr/share/lxc/templates

git clone https://github.com/lxc/lxc-templates.git

cd lxc-templates
./autogen
./configure
make
make install

# sudo cp lxc-templates/templates/* /usr/share/lxc/templates/


lxc-create -n webserver -t /usr/local/share/lxc/templates/lxc-centos

/var/lib/lxc/

lxc-ls
lxc-ls  --active
lxc-info webserver
lxc-start webserver -d

lxc-consule -n webserver
lxc-stop -n webserver

shutdown -h now

lxc-destroy -n webserver



```


> lxcd


## Tcp Wrapper

> ldd /usr/sbin/sshd | rg libwrap

> /etc/hosts.allow apply first

> sshd : ip_to_allow
> ALL : ip_to_allow
> sshd : 172.10.0.0/24
> sshd : 172.10.0.
> sshd,sftpd : 172.10.0.
> sshd : 172.10.0.0/255.255.255.0
> vsftpd : 172.10.0.

> vsftpd : ip : allow


# add log to syslog

> vsftpd : ip : allow : severity alert

# run commands
> vsftpd : ip : allow : spawn /bin/echo A connection was made >> /var/log/vsftp.log
> vsftpd : ip : allow : spawn /bin/echo A connection from %a %date %h %d %u %c was made >> /var/log/vsftp.log

> vsftpd : ip : deny
> vsftpd : ip : deny : twist /bin/echo "Your connection has been logged!"
> ALL : ALL : deny


> /etc/hosts.deny

> sshd : ALL
> ALL : ALL
> ALL : domain
> ALL : *.com



## iptables

> systemctl status iptables
> systemctl mask firewalld

> yum install iptables-services
> yum install iptables-persistent # on ubuntu
> systemctl  enable --now iptables

> iptables --list
> iptables --list -n
> iptables --list-rules

> iptables -A INPUT -p tcp --dport 80 -j ACCEPT
> iptables -A INPUT -p tcp -s ip  --dport 80 -j REJECT
> iptables-save

> vim /etc/sysconfig/iptables

> iptables -F 

> iptables -vnL
> iptables -vnL --line
> watch -n 0.5 iptables -vnL --line

## Firewalld

> firewall-cmd --state
> firewall-cmd --get-zones
> firewall-cmd --get-active-zones
> firewall-cmd --get-default-zone
> firewall-cmd --set-default-zone=internal
> firewall-cmd --permanent --new-zone=untrusted
> firewall-cmd --reload
> firewall-cmd --list-all
> firewall-cmd --zone=internal --list-all
> firewall-cmd --get-services
> firewall-cmd --zone=public --add-service=http
> firewall-cmd --zone=public --add-service=https
> firewall-cmd --zone=public --remove-service=dhcpv6-client
> firewall-cmd --permanent --zone=public --remove-service=dhcpv6-client 

> firewall-cmd --reload
> firewall-cmd --zone=public --permanent --add-port=2222/tcp
> /etc/firewalld/services
> firewall-cmd --panic-on


## Verify firewall configuration

> netstat 
> netstat  -t
> netstat  -u
> netstat  -taunp
> ss 
> fuser

> firewall-cmd --remove-service telnet --permanent

> apt install zenmap # gui

> nmap -p 1-65535 ip/host
> nmap -s T 1-65535 ip/host
> nmap -s U ip/host
> nmap -s S  ip # stealth syn
> nmap -P N  # disable ping probe



## tcpdump

> tcpdump host 172.17.0.1 -i eth0 
> tcpdump -i eth0 net 172.17.0.1/16
> tcpdump -i eth0 src 172.17.0.1
> tcpdump -i eth0 dst 172.17.0.1
> tcpdump -i eth0 port 22
> tcpdump -i eth0 nv(vv)S  -w file1.pcap
> tcpdump -i eth0 nv(vv)SX  -w file1.pcap









