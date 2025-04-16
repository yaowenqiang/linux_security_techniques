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
















