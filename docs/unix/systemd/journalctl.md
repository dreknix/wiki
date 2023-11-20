# journalctl

* [journalctl(1)](https://manpages.debian.org/journalctl.1.en.html){target="_blank"}

To see messages from other users and the system, add the current user
to the group `systemd-journal`.

## Display Logs

### From the Current Boot

``` console
journalctl -b
```

Show the past boots

``` console
journalctl --list-boots
```

### Time Window

``` console
journalctl --since "2020-01-01" --until "2020-01-02 17:15:00"
```

or

``` console
journalctl --since yesterday --until "1 hour ago"
```

### Kernel Messages

``` console
journalctl -k
```

## Filter

### By Priority

Priority: `emerg`, `alert`, `crit`, `err`, `warning`, `notice`, `info`, `debug`

``` console
journalctl -p err -b
```

### By Facility

Facility: `kern`, `mail`, `syslog`, `local0`, ...

``` console
journalctl --facility=auth
```

Get list of facilities: `journalctl --facility=help`

### By Unit

Get list of systemd units: `systemctl list-units`

``` console
journalctl -u ssh
journalctl -u ssh.service
journalctl --unit ssh
```

### By Identifier

``` console
journalctl -t sshd
journalctl --identifier=sshd
```

### By Field

Get a description of available fields: `man systemd.journal-fields`

Query which entries exist for a speficic field:

``` console
journalctl -F _UID
journalctl -F _PID
```

List all entries in the journal for a specific field:

``` console
journalctl _PID=9099
```

### By Pattern

Grep a pattern in the message text.

Find case insensitive all entry with message containing `error`:

``` console
journal -g "error"
```

## Disk Usage

``` console
journalctl --disk-usage
```

Clear the history

``` console
journalctl --vacuum-time=1week
```
