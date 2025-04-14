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

